# Lab 9: Nginx as Load Balancer, Forward Proxy, and Reverse Proxy

## Part 1: Setting Up Virtual Machines
1. Using Multipass:
Launch five VMs: one for each Nginx role (load balancer, forward proxy, and reverse proxy) and two for backend servers.

#### Commands to launch VMs:
Note: make sure to choose the network adapter based on your host machine
```sh
multipass launch -n load-balancer-vm --memory 1G --disk 5G --network en0

multipass launch -n forward-proxy-vm --memory 1G --disk 5G --network en0

multipass launch -n reverse-proxy-vm --memory 1G --disk 5G --network en0

multipass launch -n backend1 --memory 1G --disk 5G --network en0

multipass launch -n backend2 --memory 1G --disk 5G --network en0
```

2. Using AWS EC2 Instances:
Launch five EC2 instances (t2.micro or equivalent) with Ubuntu.
Configure security groups to allow HTTP (port 80) and SSH (port 22) access between instances.

3. Install Nginx on Each VM:
SSH into each backend VM (backend1 and backend2), as well as the three Nginx VMs (load-balancer-vm, forward-proxy-vm, and reverse-proxy-vm):
```sh
# Example for backend1
multipass shell backend1
sudo apt update && sudo apt install -y nginx
```

## Part 2: Configure Backend Servers to Display Their IP Addresses
### Step 1: Set Up Default Page to Show Backend IP Address
1. Edit the Default Nginx Page:
On each backend VM (`backend1` and `backend2`), edit the Nginx default configuration file:
```sh
sudo nano /var/www/html/index.nginx-debian.html
```
Modify the content to include the IP address of the backend server:
```html
<html>
<head>
    <title>Backend Server</title>
</head>
<body>
<h1>This is Backend Server with IP: your_backend_IP</h1>
</body>
</html>
```
Restart Nginx on Each Backend Server:
```sh
sudo systemctl restart nginx
```
Repeat these steps on both backend1 and backend2 VMs, each displaying its own IP address.

### Step 2: Verify Backend Servers:
Access each backend server directly to verify the setup by navigating to `http://backend1_IP` and `http://backend2_IP`. You should see a page displaying the server’s IP address.

## Part 3: Configure Nginx as a Load Balancer
### Step 1: Set Up Load Balancing on `load-balancer-vm`
SSH into the Load Balancer VM:
```sh
multipass shell load-balancer-vm
```
Edit Nginx Configuration for Load Balancing:
```sh
sudo nano /etc/nginx/sites-available/load_balancer.conf
```
Add the following configuration replacing backend1_IP and backend2_IP with the IPs of the backend VMs:
```nginx
upstream backend_servers {
    server backend1_IP;
    server backend2_IP;
}

server {
    listen 80;
    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Remove the Default Nginx Site:
```sh
sudo rm /etc/nginx/sites-enabled/default
```
Enable the Load Balancer Configuration:
```sh
sudo ln -s /etc/nginx/sites-available/load_balancer.conf /etc/nginx/sites-enabled/
```
Test and Restart Nginx:
```sh
sudo nginx -t
sudo systemctl restart nginx
```
### Step 2: Testing Load Balancing
Access the load balancer IP using `curl` or a browser:
```sh
curl http://load-balancer-vm_IP
```
Refresh the page multiple times. You should see the IP addresses of backend1 and backend2 alternate, confirming load balancing.

## Part 4: Configure Nginx as a Forward Proxy on a Separate VM
### Step 1: Set Up Forward Proxy on `forward-proxy-vm`
SSH into `forward-proxy-vm` and create a new configuration file:
```sh
sudo nano /etc/nginx/sites-available/forward_proxy.conf
```
Add the following configuration:
```nginx
server {
    listen 8888;
    resolver 8.8.8.8;
    location / {
        proxy_pass http://$http_host$request_uri;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Enable and Restart:
```sh
sudo ln -s /etc/nginx/sites-available/forward_proxy.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
### Step 2: Configuring Chrome to Use the Forward Proxy
Configure Proxy Settings in Chrome by following these steps:
Go to Settings > System > Open your computer’s proxy settings.
- Windows: In Manual proxy setup, enable Use a proxy server with forward-proxy-vm_IP and port 8888.
- macOS: Go to Proxies, enable Web Proxy (HTTP) with IP forward-proxy-vm_IP and port 8888

<ins>***Alternatively***</ins>, test using curl:
```sh
curl -x http://forward-proxy-vm_IP:8888 http://example.com
```
## Part 5: Configure Nginx as a Reverse Proxy on a Separate VM
### Step 1: Set Up Reverse Proxy on `reverse-proxy-vm`
SSH into `reverse-proxy-vm` and create a new configuration file:
```sh
sudo nano /etc/nginx/sites-available/reverse_proxy.conf
```
Add the following configuration , replacing backend1_IP with the IP of one backend server:
```nginx
server {
    listen 80;
    server_name reverseproxy.example.com;

    location / {
        proxy_pass http://backend1_IP;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Enable and Restart Nginx:
```sh
sudo ln -s /etc/nginx/sites-available/reverse_proxy.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
### Step 2: Testing Reverse Proxy
Access `http://reverse-proxy-vm_IP` using a browser or `curl`:
```sh
curl http://reverse-proxy-vm_IP
```
The backend server’s IP should appear, confirming reverse proxy functionality.
