# Lab 4.2: Setting Up Proxy Server Authentication with Custom Headers in Nginx

### Objective:
In this lab, you will configure two Nginx servers communicating securely using custom headers. The proxy server will authenticate requests based on a client’s token, replace it with its own token, and forward the request to the backend server. The backend server will validate the proxy token and ensure the original client token is present to serve the `index.html` file.

### Architecture Overview:
![Architecture Overview](images/lab4.2-fig1.png)

### Requirements:
- Two machines (or virtual machines) running Nginx: one for the proxy server and one for the backend server.
- Administrative access to configure Nginx.
---
### Note: In lab, the following Nginx modules were used:

1. **`ngx_http_rewrite_module`**
   - This module was used to manipulate the request and response headers. Specifically, we used the `map` directive to set values based on custom header conditions (validating the token).

2. **`ngx_http_proxy_module`**
   - This module enables Nginx to act as a reverse proxy. The `proxy_set_header` directive was used to forward the custom authentication header from the proxy to the backend server.

3. **`ngx_http_map_module`**
   - The `map` directive allows us to create variables based on conditions or input. It was used to validate the auth token and generate a value that would be used later in the `proxy_set_header` and `if` statements.

These modules are all part of the standard Nginx distribution and should be available by default without needing to install any additional third-party modules.

Other alternative to these modules could be `libnginx-mod-http-headers-more-filter`, but we will not use it in this lab.

---

## **🔧 Part 1: Installation and Setup of Nginx on Both Machines**

### Step 1: Set Up Nginx on Both Proxy and Backend Servers

1. **Install Nginx on the Proxy Server:**

   On the proxy server machine, execute the following commands:

   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

2. **Install Nginx on the Backend Server:**

   On the backend server machine, execute the same commands:

   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

3. **Start and Enable Nginx on Both Servers:**

   After installation, start the Nginx service on both machines and enable it to start on boot:

   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

4. **Verify Nginx is Running:**

   You can verify that Nginx is running by visiting the server’s IP address in a web browser or using `curl`:

   ```bash
   curl http://localhost
   ```

---

## **🔧 Part 2: Configuring the Nginx Proxy and Backend Servers**

### Step 1: Modify `/etc/hosts` to Add Proxy and Backend Domains

On both the **proxy** and **backend** servers, you will need to modify the `/etc/hosts` file to map custom domain names (`proxy.example.com` and `backend.example.com`) to the local machine's IP address.

1. **On Proxy Server:**

   Open the `/etc/hosts` file for editing:

   ```bash
   sudo nano /etc/hosts
   ```

   Add the following lines:

   ```hosts
   127.0.0.1    proxy.example.com
   <backend server public IPv4>    backend.example.com
   ```

   Save and exit the editor.

2. **On Backend Server:**

   Repeat the same process on the backend server:

   ```bash
   sudo nano /etc/hosts
   ```

   Add the same lines:

   ```hosts
   <proxy server public IPv4>    proxy.example.com
   127.0.0.1    backend.example.com
   ```

   Save and exit.

3. **On Client Machine / Or Your Host Machine**

    Modify the hosts file:
    - On Unix-based Machine:

    ```bash
   sudo nano /etc/hosts
   ```

    - On Windows Machine:
    Modify `c:\Windows\System32\Drivers\etc\hosts`

    Add the Virtual hosts for Proxy and Backend Servers:

    ```hosts
   <proxy server public IPv4>    proxy.example.com
   <backend server public IPv4>    backend.example.com
   ```


### Step 2: Configure the Proxy Server

1. **Create the Proxy Server Configuration File:**

   On the proxy server, create a configuration file for the proxy server in `/etc/nginx/sites-available/proxy`:

   ```bash
   sudo nano /etc/nginx/sites-available/proxy
   ```

2. **Add the Following Configuration to the Proxy Server File:**

   ```nginx
   map $http_x_client_auth $auth_valid {
       default "no";
       "UserSecret123" "yes";  # Valid client token
   }

   server {
       listen 80;
       server_name proxy.example.com;

       location / {
           # Check if the client token is valid
           if ($auth_valid = "no") {
               return 403 "Forbidden: Invalid Client Token \n";
           }

           # Forward the request to the backend server with a new auth header
           proxy_pass http://backend.example.com;
           proxy_set_header X-Proxy-Auth "ProxySecret456";   # Proxy token
           proxy_set_header X-Client-Auth $http_x_client_auth;  # Forward client auth
       }
   }
   ```

   **Explanation:**
   - **`map` directive:** Validates the `X-Client-Auth` header. If the token matches `"UserSecret123"`, the request is considered valid.
   - **`proxy_pass`**: Forwards the request to the backend server.
   - **`proxy_set_header`**: Replaces the `X-Client-Auth` header with the `X-Proxy-Auth` header for the backend.

3. **Enable the Proxy Configuration:**

   Create a symbolic link to enable the proxy server:

   ```bash
   sudo ln -s /etc/nginx/sites-available/proxy /etc/nginx/sites-enabled/
   ```

### Step 3: Configure the Backend Server

1. **Create the Backend Server Configuration File:**

   On the backend server, create a configuration file in `/etc/nginx/sites-available/backend`:

   ```bash
   sudo nano /etc/nginx/sites-available/backend
   ```

2. **Add the Following Configuration to the Backend Server File:**

   ```nginx
   map $http_x_proxy_auth $proxy_valid {
       default "no";
       "ProxySecret456" "yes";  # Valid proxy token
   }

   server {
       listen 80;
       server_name backend.example.com;

       location / {
           # Check if the proxy token is valid
           if ($proxy_valid = "no") {
               return 403 "Forbidden: Invalid Proxy Token \n";
           }

           # Check if the client token is present in the request
           if ($http_x_client_auth = "") {
               return 401 "Unauthorized: Missing Client Token \n";
           }

           # Serve the index.html file if both tokens are valid
           root /var/www/html;
           index index.html;
       }
   }
   ```

   **Explanation:**
   - **`map` directive:** Validates the `X-Proxy-Auth` header. If the token matches `"ProxySecret456"`, the request is considered valid.
   - **`if` statements:** Check if the original `X-Client-Auth` token is present.
   - **`root` and `index` directives:** Serve the `index.html` file if both headers are valid.

3. **Enable the Backend Configuration:**

   Create a symbolic link to enable the backend server:

   ```bash
   sudo ln -s /etc/nginx/sites-available/backend /etc/nginx/sites-enabled/
   ```

---

## **🔧 Part 3: Testing the Configuration**

### Step 1: Create `index.html` on the Backend Server

1. **Create the `index.html` file:**

   On the backend server, create the `index.html` file to serve as the response:

   ```bash
   sudo mkdir -p /var/www/html
   sudo nano /var/www/html/index.html
   ```

   Add the following content:

   ```html
   <html>
       <head><title>Welcome to the Backend Server</title></head>
       <body><h1>Authenticated Request Successful!</h1></body>
   </html>
   ```

### Step 2: Restart Nginx on Both Servers

1. **Restart Nginx on Proxy and Backend Servers:**

   Apply the changes by restarting Nginx on both machines:

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### Step 3: Test the Setup

1. **Valid Client Request:**

   On the client machine, test with a valid `X-Client-Auth` token:

   ```bash
   curl -H "X-Client-Auth: UserSecret123" http://proxy.example.com/
   ```

   **Expected Response:**

   ```
   <html>
       <head><title>Welcome to the Backend Server</title></head>
       <body><h1>Authenticated Request Successful!</h1></body>
   </html>
   ```

2. **Invalid Client Token:**

   Test with an invalid token:

   ```bash
   curl -H "X-Client-Auth: WrongToken" http://proxy.example.com/
   ```

   **Expected Response:**

   ```
   Forbidden: Invalid Client Token
   ```

3. **Missing Client Token:**

   Test with no `X-Client-Auth` token:

   ```bash
   curl http://proxy.example.com/
   ```

   **Expected Response:**

   ```
   Forbidden: Invalid Client Token
   ```

4. **Tampered Proxy Token:**

   Test with an invalid `X-Proxy-Auth` token on the backend:

   ```bash
   curl -H "X-Proxy-Auth: FakeToken" http://backend.example.com/
   ```

   **Expected Response:**

   ```
   Forbidden: Invalid Proxy Token
   ```

5. **Missing Client Token on Backend:**

   Test with no `X-Client-Auth` token but a valid `X-Proxy-Auth`:

   ```bash
   curl -H "X-Proxy-Auth: ProxySecret456" http://backend.example.com/
   ```

   **Expected Response:**

   ```
   Unauthorized: Missing Client Token
   ```

---

### Conclusion:
In this lab, you successfully configured a proxy server and backend server to communicate securely using custom headers. The proxy validates the client token, replaces it with its own token, and forwards the request to the backend. The backend checks both the proxy token and the client token before serving content.