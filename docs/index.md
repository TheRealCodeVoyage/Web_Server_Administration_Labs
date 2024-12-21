# ACIT 3475: Web Server Administration Labs
This repository contains lab exercises for BCIT's ACIT 3475 Web Server Administration 
course. This course introduces students to the deployment and operation of web applications. Students will implement an existing application into a web stack including web servers, databases, and application servers, improving the stack throughout the course. Topics include Web Server Management, Application Servers, Load Balancing, Caching, Performance Measurement, Reverse Proxies, TLS/SSL Security, Authentication Mechanisms, and Centralized Logging.  

## Labs Index:
- [Lab 1: Setup: VMWare Pro, Multipass, AWS EC2](Lab1.md)  
- [Lab 2: Setting up and Configuring Apache Web Server and IIS](Lab2.md)  
- [Lab 3: Exploring HTTP Headers and Protocols with cURL and Browser](Lab3.md)  
- [Lab 4: Introduction to Nginx and Basic Module Configuration](Lab4.md)  
- [Lab 5: Installing LAMP and LNMP Stacks with WordPress](Lab5.md)  
- [Lab 6: SSL/TLS Certificate and Its Implemenetation](Lab6.md)  
- [Lab 7: Implementation of OAuth with Express.js](Lab7.md)  
- [Lab 8: Load Balancing with HAProxy](Lab8.md)  
- [Lab 9: Nginx as Load Balancer, Forward Proxy, and Reverse Proxy](Lab9.md)  
- [Lab 10: CDN and Logging](Lab10.md)  

### Key Learning Outcomes
- Describe components of a scalable, distributed web stack.  
- Set up, manage, and deploy web servers.  
- Integrate dynamic web applications with server modules, reverse proxies, and standalone application servers.  
- Implement software-based load balancing with HAProxy.  
- Apply caching, compression, and encryption offloading to optimize performance.  
- Configure secure communication using TLS and X.509 certificates.  
- Implement token-based authentication mechanisms such as OAuth.  
- Integrate Content Delivery Networks (CDNs).  
- Implement and manage a centralized log server.  

## Getting Started
To begin, please clone the repository and follow the setup instructions in each lab. You will 
need a laptop with VMWare Workstation (or VirtualBox) and Docker, along with the required software mentioned in the labs.
```sh
 __   __  _______  _______  _______  __   __                              
|  | |  ||   _   ||       ||       ||  | |  |                             
|  |_|  ||  |_|  ||    _  ||    _  ||  |_|  |                             
|       ||       ||   |_| ||   |_| ||       |                             
|       ||       ||    ___||    ___||_     _|                             
|   _   ||   _   ||   |    |   |      |   |                               
|__| |__||__|_|__||___|___ |___|__    |___| _  ___   __    _  _______  __ 
|   |    |       ||   _   ||    _ |  |  |  | ||   | |  |  | ||       ||  |
|   |    |    ___||  |_|  ||   | ||  |   |_| ||   | |   |_| ||    ___||  |
|   |    |   |___ |       ||   |_||_ |       ||   | |       ||   | __ |  |
|   |___ |    ___||       ||    __  ||  _    ||   | |  _    ||   ||  ||__|
|       ||   |___ |   _   ||   |  | || | |   ||   | | | |   ||   |_| | __ 
|_______||_______||__| |__||___|  |_||_|  |__||___| |_|  |__||_______||__|
```