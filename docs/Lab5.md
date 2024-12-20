# Lab 5: Installing LAMP and LNMP Stacks with WordPress

## Part 1: Setting Up the Virtual Machines (VMs)

- Create Two Virtual Machines:
Use VMWare, Multipass, or AWS EC2 to create two VMs running Ubuntu Server 22.04 (or a similar version). One VM will be for the LAMP stack and the other for the LNMP stack.

- Connect to the VMs:
Once the VMs are created and running, use SSH to connect to each VM:
```sh
ssh <username>@<VM-IP>
```

- Update Each VM:
Before installing any packages, ensure the system is up to date:
```sh
sudo apt update && sudo apt upgrade -y
```

## Part 2: Installing LAMP Stack on the First VM

### Step 1: Install Apache
Install Apache Web Server:
```sh
sudo apt install apache2 -y
```
Start and Enable Apache:
```sh
sudo systemctl start apache2
sudo systemctl enable apache2
```

Verify Apache Installation:
Open a web browser and visit `http://<VM-IP>`. You should see the default Apache page, "It works!".

### Step 2: Install MySQL
Install MySQL Server:
```sh
sudo apt install mysql-server -y
```

Secure MySQL Installation:
```sh
sudo mysql_secure_installation
```

Create a Database for WordPress:
```sh
sudo mysql -u root -p
```
```sql
CREATE DATABASE wordpressdb;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY '!@#$%^ZXCVBNzxcvbn123456';
GRANT ALL PRIVILEGES ON wordpressdb.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Step 3: Install PHP
Install PHP and Required Modules:
```sh
sudo apt install php libapache2-mod-php php-mysql -y
```

Test PHP Installation:
Create a test PHP file:
```sh
sudo nano /var/www/html/info.php
```
Add the following code:
```php
<?php phpinfo(); ?>
```

Open `http://<VM-IP>/info.php` in your browser to verify the PHP installation.

## Part 3: Installing WordPress on LAMP
### Step 1: Download and Configure WordPress
Download and Extract WordPress:
```sh
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xvzf latest.tar.gz
sudo mv wordpress/* /var/www/html/
sudo rm latest.tar.gz
```

Set Permissions:
```sh
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

Configure WordPress:
```sh
sudo mv /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
sudo nano /var/www/html/wp-config.php
```

Update the database settings in the `wp-config.php` file:
```php
define( 'DB_NAME', 'wordpressdb' );
define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', 'password' );
define( 'DB_HOST', 'localhost' );
```

Restart Apache:
```sh
sudo systemctl restart apache2
```
### Step 2: Complete WordPress Installation
Open a Browser:
Navigate to `http://<VM-IP>/wp-admin` and follow the WordPress setup wizard:

- Choose your language.
- Set up an admin username, password, and email.
- Complete the installation.

Log in to the Admin Dashboard:
After installation, log in at `http://<VM-IP>/wp-admin`.

## Part 4: Installing LNMP Stack on the Second VM
### Step 1: Install Nginx
Install Nginx:
```sh
sudo apt install nginx -y
```
Start and Enable Nginx:
```sh
sudo systemctl start nginx
sudo systemctl enable nginx
```

Verify Nginx Installation:
Visit `http://<VM-IP>` in your browser. You should see the default Nginx welcome page.

### Step 2: Install MySQL

Follow the same steps as in Part 2 to install and configure MySQL on this VM.
Create a Database and User for WordPress (if not done in Part 2).

### Step 3: Install PHP and PHP-FPM
Install PHP and PHP-FPM:
```sh
sudo apt install php-fpm php-mysql -y
```

Configure Nginx to Use PHP:
```sh
sudo nano /etc/nginx/sites-available/default
```
Update the server block to handle PHP files:
```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Restart Nginx and PHP-FPM:
```sh
sudo systemctl restart nginx
sudo systemctl restart php7.4-fpm
```

## Part 5: Installing WordPress on LNMP
### Step 1: Download and Configure WordPress
Download and Extract WordPress:

Follow the same steps as in Part 3 to download and configure WordPress.
Set Permissions:
```sh
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```
Configure WordPress (edit wp-config.php):

Same as in Part 3.
Restart Nginx:
```sh
sudo systemctl restart nginx
```

### Step 2: Complete WordPress Installation
Open a Browser:
Navigate to `http://<VM-IP>/admin` and follow the WordPress setup wizard as in Part 3.

Log in to the Admin Dashboard:
After installation, log in at `http://<VM-IP>/wp-admin`.

Wrap-up and Reflection
Discuss the differences between the LAMP and LNMP stacks for WordPress hosting.
Reflect on the performance differences of Apache vs. Nginx.


## Deliverables:
Show the proof of successful deployment of LAMP and LNMP Stack and Walk through the steps you took to achieve it. 
