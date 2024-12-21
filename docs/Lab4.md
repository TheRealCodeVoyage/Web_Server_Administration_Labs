# Lab 4: Introduction to Nginx and Basic Module Configuration

## Part 1: Environment Setup
In this part, we will create a virtual machine (VM) to simulate an environment where Nginx can be installed and run. This VM will serve as the platform for all subsequent configurations and experiments.

1.	VM Setup:

```sh
multipass launch --name nginx-lab --cpus 2 --memory 2G --disk 10G
```

```sh
multipass shell nginx-lab
```

2.	Basic Package Installation:

```sh
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
```

3.	Start and enable Nginx so it runs automatically on system boot:

```sh
sudo systemctl start nginx
sudo systemctl enable nginx
```
4.	Verify Nginx is running by checking its status:

```sh
systemctl status nginx
```

To confirm that Nginx is properly serving requests, open your web browser and enter the VM's IP address. Use the following command to retrieve the IP:
```sh
multipass info nginx-lab
```

You can also test connectivity using curl from within the VM:

```sh
curl http://localhost
```

## Part 2: Basic Nginx Configuration
This part will focus on understanding how Nginx handles HTTP requests using server blocks, which define virtual hosts. You'll learn how to configure a basic virtual host for a custom website.

1.	Understanding the Default Configuration:
The Nginx configuration file contains the main settings for the server. By understanding its structure, you'll be able to make informed changes to meet specific requirements.

Open the Nginx configuration file to explore its content:
```sh
sudo nano /etc/nginx/nginx.conf
```
Identify important sections such as:
- `http {}` block: Handles web service configurations.
- `server {}` block: Defines a virtual host for handling requests.

Before making changes, create a backup of the original configuration:

```sh
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
```

2. Create a Simple Virtual Host:
Virtual hosts allow Nginx to host multiple websites on a single server. In this step, you'll create a simple site and configure Nginx to serve it.
First, create a directory for your website files:

```sh
sudo mkdir -p /var/www/mywebsite
sudo nano /var/www/mywebsite/index.html
```

Step: Add basic HTML content to index.html:

```html
<html>
<head>
    <title>Welcome to Nginx!</title>
</head>
<body>
    <h1>Success! Your Nginx server is working!</h1>
</body>
</html>
```

Adjust the permissions for the website directory so Nginx can access the files:

```sh
sudo chown -R www-data:www-data /var/www/mywebsite
sudo chmod -R 755 /var/www/mywebsite
```

3. Modify Nginx Configuration for Virtual Host:
Nginx uses server blocks to define how it should handle incoming requests for a particular domain. You'll set up a basic server block for your new website.
Create a virtual host configuration file for mywebsite: 

```sh
sudo nano /etc/nginx/sites-available/mywebsite
```

Add the following configuration to define a simple server block:

```nginx
server {
    listen 80;
    server_name mywebsite.local;
    root /var/www/mywebsite;
    index index.html;
}
```

Enable the virtual host by creating a symbolic link:

```sh
sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/
```

Test the Nginx configuration to ensure there are no syntax errors:

```sh
sudo nginx -t
```
Restart Nginx to apply the new configuration:

```sh
sudo systemctl restart nginx
```

**Test the Website Locally:**
To simulate accessing your website via a domain name, you’ll add a local DNS entry to your machine’s hosts file.
Add an entry in `/etc/hosts` for `mywebsite.local`:

```sh
sudo nano /etc/hosts
```
Add the following line:

```hosts
127.0.0.1 mywebsite.local
```

Access your new website by opening a browser or using curl:

```sh
curl http://mywebsite.local
```

## Part 3: Basic Security Configurations 
Web security is crucial for protecting your server from unauthorized access. In this part, you’ll explore simple security practices like restricting access based on IP and setting up basic authentication.

1.	Restricting Access to the Website:
In certain scenarios, you may want to limit who can access your website by IP address. This step demonstrates how to configure such restrictions in Nginx. Modify your server block in `/etc/nginx/sites-available/mywebsite` file to allow only specific IPs to access the site:

```nginx
location / {
allow 192.168.0.0/24;
deny all;
}
```

Reload the Nginx configuration and test access from a different IP (such as local host).
After you successfully tested you can remove this and reload again.

2. Setting Up Basic Authentication:
Basic authentication adds a layer of security by requiring a username and password for access. Nginx supports this via `.htpasswd` files.
Install the Apache tools package to manage password files:

```sh
sudo apt install apache2-utils
```
Create a password file and add a user:

```sh
sudo htpasswd -c /etc/nginx/.htpasswd admin
```
Modify your virtual host to enable authentication:

```nginx
location / {
    auth_basic "Restricted Area";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

Reload Nginx and test the setup by visiting the website. You should be prompted for credentials.

## Part 4: Static Module Configuration
Nginx supports various static modules that can enhance its functionality, such as enabling compression or directory listings. This part will guide you through configuring some commonly used static modules.

1.	Enable Gzip Compression:
Gzip compression reduces the size of files sent to the browser, improving website load times. You’ll enable and configure it for text-based files.
Modify the `nginx.conf` file to enable Gzip:

```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript;
gzip_min_length 256;
```

Test the setup by serving a large text or CSS file. You can use browser developer tools to verify that the file is being compressed.

To test, we need a big file! Here is how you can create a random generated file:

```sh
tr -dc "A-Za-z 0-9" < /dev/urandom | fold -w100|head -n 100000 > bigfile.txt
```

**Command Breakdown**
1.	`/dev/urandom`: This is a special file in Unix-like systems that provides an endless stream of pseudo-random bytes. It’s used here as the source of random data.

2.	`tr -dc "A-Za-z0-9"`:
    - `tr` is a command that translates or deletes characters.
    - `-d`: Deletes characters that don't match the specified pattern.
    - `-c`: Complements the specified set, meaning it deletes all characters except for those that match `"A-Za-z0-9"` (uppercase and lowercase letters, and digits).
    - Result: This command filters the random stream from `/dev/urandom`, keeping only alphanumeric characters (A-Z, a-z, 0-9) and discarding everything else.

3.	`fold -w100`:
    - `fold` breaks lines at a specific width.
    - `-w100` specifies that each line should be wrapped at 100 characters.
    - Result: This takes the random alphanumeric string and breaks it into lines of 100 characters each.


4.	`head -n 100000`:
- `head` prints the first part of input.
- `-n 100000` tells head to output the first 100,000 lines.
- `Result`: This ensures that only 100,000 lines are printed.

After we created our file, we should copy it to the default virtual host:

```sh
sudo cp bigfile.txt /var/www/html/
```

Now by navigating to `http://<VM IP>/bigfile.txt` we can access our file from a browser in Host Machine.

## Part 5: Logging and Monitoring 
Understanding how Nginx logs activity is important for debugging and monitoring server performance. In this part, you’ll explore both access logs and error logs.
1.	Access Logs:
Nginx logs every request it receives in the access logs. By examining these logs, you can monitor website traffic and troubleshoot issues.
View the Nginx access logs in real-time:

```sh
sudo tail -f /var/log/nginx/access.log
```

2. Error Logs:
Error logs provide information about issues encountered by Nginx, such as misconfigurations or denied requests.
View the Nginx error logs:

```sh
sudo tail -f /var/log/nginx/error.log
```

Induce an error (e.g., by modifying the virtual host configuration to point to a non-existent directory) and observe the corresponding log entry
