# Lab 2: Setting up and Configuring Apache Web Server and IIS

## Part1: Apache Web Server

### Step 1: Install Multipass

If not already installed, install Multipass by following instructions on the official website Multipass Installation Guide. [Install Multipass](https://canonical.com/multipass/install)

### Step 2: Launch a Multipass VM

- Run the following command to launch a new Ubuntu VM:
    - For Windows Machines:
    ```sh
    multipass launch --name apache-server --memory 1G --disk 5G –network Wi-Fi
    ```
    - For Unix-based Machines:
    ```sh
    multipass launch --name apache-server --memory 1G --disk 5G
    ```
    - This creates a VM with 1GB of RAM and 5GB of disk space.

### Step 3: Access the VM

- Once the VM is running, access it by executing:
```sh
multipass shell apache-server
```
You are now inside the VM, ready to install and configure Apache.

### Step 4: Update the Package Manager

- Before installing Apache, update the package list:
```sh
sudo apt update
```

### Step 5: Install Apache

- Install the Apache web server using the following command:
```sh
sudo apt install apache2 -y
```

### Step 6: Verify Apache Installation

- To verify if Apache was installed successfully, check the status of the service:
```sh
sudo systemctl status apache2
```
If Apache is running, you should see a status of "active (running)."

### Step 7: Test Apache Installation

- Open a web browser on your local machine and type the VM's IP address. Use this command in the VM to find the IP address:
```sh
multipass info apache-server
```
- Navigate to `http://<vm-ip-address>` in your browser. You should see the default Apache welcome page.

### Step 8: Create a Simple HTML Page

- Replace the default Apache page with a custom HTML page:
```sh
sudo rm /var/www/html/index.html
echo "<h1>Welcome to My Simple Website</h1>" | sudo tee /var/www/html/index.html
```
### Step 9: Test the Website

- Refresh the browser. You should now see your custom webpage with the "Welcome to My Simple Website" message.

### Step 10: Apache Configuration Files

- Apache’s main configuration file is located at `/etc/apache2/apache2.conf`. You can open it for viewing with:
```sh
sudo nano /etc/apache2/apache2.conf
```

### Step 11: Enabling Modules

- Apache has various modules that extend its functionality. Enable the rewrite module (for URL rewriting):
```sh
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Step 12: Configuring Virtual Hosts

- Apache allows you to host multiple websites on a single server using virtual hosts.
Create a new directory for your virtual host:
```sh
sudo mkdir /var/www/mywebsite
echo "<h1>My Website</h1>" | sudo tee /var/www/mywebsite/index.html
```
- Create a new virtual host configuration file:
```sh
sudo nano /etc/apache2/sites-available/mywebsite.conf
```
- Add the following configuration:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@mywebsite.com
    ServerName mywebsite.com
    DocumentRoot /var/www/mywebsite
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### Step 13: Enabling Virtual Host

- Enable the newly created virtual host:
```sh
sudo a2ensite mywebsite.conf
sudo systemctl reload apache2
```
- Test the configuration syntax to ensure there are no errors:
```sh
sudo apachectl configtest
```

### Step 14: Update `/etc/hosts` on Your Local Machine

- For Windows Machines: `C:/Windows/system32/drivers/etc/hosts`

- Since the domain `mywebsite.com` is not a real domain, you need to map it to your VM’s IP address in the `/etc/hosts` file on your local machine.
Add the following line (replace `<vm-ip-address>` with the actual IP):

```hosts
<vm-ip-address> mywebsite.com
```

### Step 15: Test the Virtual Host

- Open your browser and go to `http://mywebsite.com`. You should see the "My Website" page.

### Step 16: Securing Apache with Firewall

- Go back to apache-server shell
- Ensure that Apache is configured correctly in the firewall:
```sh
sudo ufw allow 'Apache'
sudo ufw enable
sudo ufw status
```

### Step 17: Configuring Directory Permissions

- Set custom permissions for your website directories. Open the Apache configuration file:
```sh
sudo nano /etc/apache2/apache2.conf
```

- Find the section for `/var/www/` and modify the directory permissions:

```apache
<Directory /var/www/>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

### Step 18: Setting Up Server Security Headers

- Add security headers to improve the security of your web server:
```sh
sudo nano /etc/apache2/conf-available/security.conf
```

- Add the following headers:

```apache
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "DENY"
Header always set X-XSS-Protection "1; mode=block"
```

#### What are these Headers?!

`X-Content-Type-Options "nosniff"`: This header prevents the browser from interpreting files as a different MIME type than what is specified in the Content-Type header. It helps mitigate attacks based on MIME-type confusion (e.g., forcing a browser to execute a malicious file as a script).

`X-Frame-Options "DENY"`: This header prevents the website from being embedded in a frame or iframe on another site. It protects against clickjacking attacks, where an attacker might trick a user into clicking on something different than they perceive.

`X-XSS-Protection "1; mode=block"`: This header enables the browser’s built-in cross-site scripting (XSS) filter. When a potential XSS attack is detected, the browser will block the page from loading, protecting the user from malicious content.

- Enable the Headers Module and Restart Apache
```sh
sudo a2enmod headers
sudo systemctl restart apache2
```

### Step 19: Remove the VM
- Once you are done, exit the VM and stop it:
```sh
exit
multipass delete apache-server
multipass purge
```

## Part2: Setting up and Configuring IIS Web Server on an EC2 Instance

### Step 1: Launch a Windows EC2 Instance

- Log in to the AWS Management Console and navigate to the EC2 Dashboard.
- Click Launch Instance and configure the following:
    - AMI: Select a Windows Server 2019 (or later) AMI.
    - Instance Type: Choose a t2.micro (free tier eligible).
    - Key Pair: Select an existing key pair or create a new one to access the instance.
    - Security Group: Ensure that port 80 (HTTP) and port 3389 (RDP) are open.
- Launch the instance.

### Step 2: Access the EC2 Instance

- Once the instance is launched, go to the Instances section of the EC2 Dashboard.
- Select your instance, click Connect, and use the provided instructions to connect via Remote Desktop (RDP).
- Download the RDP file and use your key pair to retrieve the password.
- Log in to the instance using the administrator credentials.

### Step 3: Open Server Manager

- Once logged in to your Windows Server, open Server Manager from the taskbar or the Start menu.

### Step 4: Add IIS Role

- In Server Manager, click on Add roles and features.
- Select Role-based or feature-based installation.
- Select your server (should be listed automatically).
- On the Select server roles page, scroll down and check the box for Web Server (IIS).
- Click Next through the wizard until the Install button is available, and then click Install.

### Step 5: Verify IIS Installation

- Once the installation completes, open a web browser inside the Windows Server and type http://localhost. You should see the default IIS welcome page.

### Step 6: Test IIS from Your Local Machine

- Find the public IP address of your EC2 instance from the AWS EC2 Dashboard.
From your local machine, open a browser and go to `http://<instance-public-ip>`. The IIS default page should appear, confirming the server is reachable.

### Step 7: Create a Simple HTML Page

- Open File Explorer and navigate to the default IIS web root:
```
C:\inetpub\wwwroot
```

- Delete the existing iisstart.htm file and replace it with a new HTML file:
- Create a file named index.html with the following content:
```html
<h1>Welcome to My IIS Website</h1>
```

### Step 8: Test the Website

- From your local machine, refresh the browser pointing to your EC2 instance’s public IP. You should now see the custom message: “Welcome to My IIS Website.”



### Step 9: IIS Manager Overview

- Open IIS Manager from the Start menu (type `inetmgr`).
- In the left-hand pane, you’ll see your server name. Expand it to see Sites and the Default Web Site.

### Step 10: Creating a New Website

- Let’s create a new website with a custom folder.
First, create a new folder for the site: `C:\MyWebsite`

- Place an `index.html` file in this folder with the content:
```html
<h1>This is My New Website</h1>
```

- In IIS Manager, right-click Sites and select Add Website.
- Fill in the following:
    - Site Name: `MyWebsite`
    - Physical Path: `C:\MyWebsite`
    - Binding: Use the default HTTP on port `80`.
- Click OK to create the new site.

### Step 11: Test the New Website

- Go to your EC2 instance's public IP in your browser. You will still see the default site.
- To test the new website, stop the Default Web Site in IIS Manager by right-clicking and selecting Stop.

- Now, refresh the browser, and you should see “This is My New Website.”

### Step 12: Configuring Virtual Directories

- A virtual directory allows you to map a folder to a URL path.
- In IIS Manager, right-click your MyWebsite and select Add Virtual Directory.
- For the Alias, enter images, and for the Physical Path, create a folder `C:\MyWebsite\Images`.
- Place an image in the Images folder.
You can now access this image by navigating to `http://<instance-public-ip>/images/<image-file-name>`.

### Step 13: Enabling Directory Browsing

- By default, IIS doesn’t allow directory browsing. You can enable it:
    Select your website in IIS Manager.
    Double-click Directory Browsing in the features view.
    On the right-hand side, click Enable.
- Now you can navigate to `http://<instance-public-ip>/images/` to see a list of files in the images directory.

### Step 14: Setting Custom Error Pages
- You can configure IIS to show custom error pages.
- In IIS Manager, double-click Error Pages.
- Click Add on the right-hand side, and configure a custom error page for 404 Not Found errors.
- Create a simple `404.html` file in your website’s folder with a custom message, such as:
```html
<h1>Oops! Page not found.</h1>
```

### Step 15: Logging and Monitoring

IIS generates logs that help monitor the performance and traffic to your website.
- In IIS Manager, double-click Logging.
- Ensure that logging is enabled, and review the log file directory:
`C:\inetpub\logs\LogFiles`

### Step 16: Terminate the EC2 Instance
- Once you’ve finished the lab, go to the EC2 Dashboard and select your instance.
- Click Actions > Instance State > Terminate to stop and delete the instance.
