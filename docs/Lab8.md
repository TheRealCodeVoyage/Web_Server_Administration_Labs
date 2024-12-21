# Lab 8: Load Balancing with HAProxy

In this lab, you will set up HAProxy as a load balancer with AWS EC2 instances as backend servers. You will explore different load balancing algorithms, configure health checks, enable sticky sessions, and use HAProxy stats for monitoring. This setup simulates a real-world cloud environment for testing.

## Part 1: Set Up AWS Instances as Backend Servers
1. Launch AWS EC2 Instances
 - Go to the AWS EC2 console and launch two instances for the backend servers.
 - Instance Type: Choose `t2.micro` (for free-tier eligibility).
 - AMI: Select an Ubuntu or Amazon Linux AMI.
 - Security Group: Configure inbound rules to allow HTTP (port 80) and SSH (port 22) access.
2. Connect to Each Instance: Use SSH to connect to each instance:

3. Install Nginx on Each Instance
Once connected, update the package list and install Nginx:

```sh
sudo apt update
sudo apt install nginx -y
```

4. Customize Nginx Default Page
Edit the default `index.html` on each instance to display a unique message:

```sh
echo "Hello from server <Instance-ID-or-Name>" | sudo tee /var/www/html/index.html
```

5. Retrieve Public IPs of EC2 Instances
Note the public IP addresses of both EC2 instances, as they will be needed in the HAProxy configuration.

## Part 2: Set Up HAProxy on a Separate AWS EC2 Instance
1. Launch an HAProxy EC2 Instance
 - Go to the AWS EC2 console and launch a new instance to serve as the HAProxy server.
 - Instance Type: Choose `t2.micro`.
 - AMI: Select Ubuntu or Amazon Linux.
 - Security Group: Open HTTP (port 80) and HTTPS (port 443) for client access and SSH (port 22) for remote access.
2. Install HAProxy
SSH into the HAProxy instance and install HAProxy:

```sh
sudo apt update
sudo apt install haproxy -y
```

3. Configure HAProxy with AWS Backend Servers
Open the HAProxy configuration file:

```sh
sudo nano /etc/haproxy/haproxy.cfg
```

Set up a frontend to listen on port 80 and define your backend servers:

```haproxy
frontend http_front
    bind *:80
    default_backend aws_backends

backend aws_backends
    balance roundrobin
    server aws1 <EC2-Instance1-IP>:80 check
    server aws2 <EC2-Instance2-IP>:80 check
```

Replace `<EC2-Instance1-IP>` and `<EC2-Instance2-IP>` with the public IPs of your EC2 backend instances.
4. Restart HAProxy
Save the configuration and restart HAProxy:

```sh
sudo systemctl restart haproxy
```

## Part 3: Test Load Balancing Algorithms
- Round Robin Algorithm
- Configuration:
`balance roundrobin`
- Testing: Use `curl http://<HAProxy-instance-IP>` to see traffic alternating between servers.
----
- Least Connections Algorithm
- Configuration:
`balance leastconn`
- Testing: Run multiple simultaneous requests using a load-testing tool (e.g., JMeter) to confirm traffic is routed to the server with fewer connections.
----
- Source IP Hashing Algorithm
- Configuration:
`balance source`
- Testing: Access `http://<HAProxy-instance-IP>` multiple times from the same IP and confirm consistent routing to the same backend.
----
- Weighted Round Robin Algorithm
- Configuration:
`balance roundrobin`

```haproxy
server aws1 <EC2-Instance1-IP>:80 check weight 2
server aws2 <EC2-Instance2-IP>:80 check weight 1
```

- Testing: Access repeatedly to observe that `aws1` receives more requests due to the higher weight.
---

## Part 4: Configuring Health Checks
1. Enable Health Checks
In the `backend` section, add the `check` option for each server to enable health checks:

```haproxy
backend aws_backends
    balance roundrobin
    server aws1 <EC2-Instance1-IP>:80 check
    server aws2 <EC2-Instance2-IP>:80 check
```

2. Advanced Health Check Options
Use parameters like `inter`, `rise`, and `fall` for more control:

```haproxy
server aws1 <EC2-Instance1-IP>:80 check inter 2000 rise 3 fall 2
server aws2 <EC2-Instance2-IP>:80 check inter 2000 rise 3 fall 2
```

3. Testing Health Checks
- Stop Nginx on one backend

```sh
sudo systemctl stop nginx
```

 and check the HAProxy stats page to verify that the server is marked as DOWN.
- Restart Nginx and ensure it’s marked as UP after passing health checks.
Part 5: Enabling Sticky Sessions
1. Configure Sticky Sessions in HAProxy
Modify the `backend` section to add `stick-table` and `stick on` directives:

```haproxy
backend aws_backends
    balance roundrobin
    stick-table type ip size 200k expire 30m
    stick on src
    server aws1 <EC2-Instance1-IP>:80 check
    server aws2 <EC2-Instance2-IP>:80 check
```

2. Testing Sticky Sessions
- Access `http://<HAProxy-instance-IP>` multiple times from the same client IP and confirm consistent routing to the same backend server.

## Part 6: Monitoring HAProxy with Stats
1. Enable HAProxy Stats Page
Configure HAProxy to expose a stats page by adding a `stats` URI:

```haproxy
frontend http_front
    bind *:80
    stats uri /haproxy?stats
    default_backend aws_backends
```

2. Access the Stats Page
Open `http://<HAProxy-instance-IP>/haproxy?stats` in your browser to view real-time monitoring, including connection counts, backend health, and server status.
