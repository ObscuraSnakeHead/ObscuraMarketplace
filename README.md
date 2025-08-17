# ObscuraMarketplaceScript
Obscura Marketplace Script, developed by The ObscuraSec Hacking Group, is a DarkNet Market Script that was created by The ObscuraSec Hacking Group.

# Obscura Market
Obscura Market is a DarkNet Market developed by The ObscuraSec Hacking Group. Administrated by ObscuraSnakeHead, Obscura Market is a Monero only DarkNet Market that was developed with OPSec, customer service and functionality in mind.

# Operating System Requirements
The Obscura Marketplace Script is developed on the "Kali Linux" platform, but it is recommended to host the market on "Tails OS (Linux)" for maximum anonymity and OPSec.

# System Preparation
Begin by updating your system to ensure all packages are current:
```bash
sudo apt-get update
sudo apt-get upgrade -y
```

# Installing Required Dependencies
# PHP 8.4 Installation
First, we'll install PHP 8.4 along with essential extensions required for the marketplace:
```bash
sudo apt-get install -y php8.4-fpm php8.4-mysql php8.4-curl php8.4-gd php8.4-mbstring \ php8.4-xml php8.4-zip php8.4-bcmath php8.4-gnupg php8.4-intl php8.4-readline \
php8.4-common php8.4-cli php8.4-gmp
```
We also need to install unzip because composer will use it to extract packages:
```bash
sudo apt-get install -y unzip
```

# Composer Installation
Install Composer 2, which we'll need for managing Laravel dependencies:
```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
HASH="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
```

# Additional Dependencies
Install Git for version control:
```bash
sudo apt-get install -y git
```

# Database Server
Install and configure MariaDB server:
```bash
sudo apt-get install -y mariadb-server mariadb-client
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

# Web Server
Install and enable Nginx:
```bash
sudo apt-get install -y nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

# Frontend Development Tools

Install Node.js and NPM for use as marketplace dev tools:

```bash
sudo apt-get install -y nodejs npm
```

# Verification Steps
Verify all installations by checking their versions:
```bash
php -v
composer -V
git --version
mysql --version
nginx -v
```

Expected output (versions may vary):
```
Composer version 2.8.4 2025-07-11 11:57:47
PHP version 8.4.x (/usr/bin/php8.4)
git version 2.34.1
mysql  Ver 8.0.40-0ubuntu0.22.04.1 for Linux on x86_64 ((Ubuntu))
nginx version: nginx/1.18.0 (Ubuntu)
```

# Database Configuration
# Secure MariaDB Setup
Run the MySQL/MariaDB secure installation script:
```bash
sudo mysql_secure_installation
```

When prompted:
1. Enable the VALIDATE PASSWORD COMPONENT (select 'y')
2. Choose password validation level 1
3. Answer 'y' to all subsequent security questions

# Database and User Creation
Access the MySQL prompt:
```bash
sudo mysql
```

Create a new database user (replace the placeholder values with your own):
```sql
CREATE USER 'your_user'@'localhost' IDENTIFIED BY 'your_password';
```

Create the database:
```sql
CREATE DATABASE your_database_name;
```

Grant necessary privileges:
```sql
GRANT ALL PRIVILEGES ON your_database_name.* TO 'your_user'@'localhost';
FLUSH PRIVILEGES;
```

Exit the MySQL prompt:
```sql
exit;
```

# Verify Database Access
Test your new database user credentials:
```bash
mysql -u your_user -p
```

After entering your password, verify database creation:
```sql
SHOW DATABASES;
```

Your database name should appear in the list. Make sure to securely store your database credentials, as they'll be required for the marketplace .env configuration file.

# Repository Setup and Laravel Configuration

Let's start by navigating to your Downloads folder:
```bash
cd Downloads
```

Now, we will download our repository from GitHub. This command will create a folder named "Obscura" in your Downloads folder:
```bash
git clone https://github.com/ObscuraSnakeHead/Obscura.git
```

We need to move our "Obscura" repository to /var/www/ directory, as this will be the location Nginx uses to serve our application:
```bash
sudo mv Obscura /var/www/
```

Open a new terminal and navigate to our project directory:
```bash
cd /var/www/Obscura
```

First, let's create our environment configuration file by copying the example file:
```bash
cp .env.example .env
```

Now, we'll edit our .env file using nano text editor:
```bash
sudo nano .env
```

In this file, you'll find various configuration settings. You need to update the database settings with the credentials we created earlier. Look for these lines and modify them:
```
DB_DATABASE=your_database_name
DB_USERNAME=your_user
DB_PASSWORD=Y0ur_P@ssw0rd!23
```

To save the changes and exit the text editor, press CTRL+X, then 'y' to confirm, and finally press Enter.

Next, let's install NPM frontend dev tools using:
```bash
npm install
```

Next, let's install all required packages using Composer:
```bash
composer install
```

Generate the application encryption key:
```bash
php artisan key:generate
```

Now, let's create our database tables by running migrations:
```bash
php artisan migrate
```

Finally, we need to set proper file permissions for security. Run these commands in sequence:
```bash
sudo chown -R www-data:www-data /var/www/Obscura
sudo find /var/www/Obscura -type f -exec chmod 644 {} \;
sudo find /var/www/Obscura -type d -exec chmod 755 {} \;
sudo chmod -R 775 /var/www/Obscura/storage
sudo chmod -R 775 /var/www/Obscura/bootstrap/cache
sudo chmod 640 /var/www/Obscura/.env
```

# Secure Nginx Configuration

Now we need to configure Nginx to serve our marketplace. Let's create a new server configuration file:
```bash
sudo nano /etc/nginx/sites-available/Obscura
```

Copy and paste the following configuration into the file. This is a simple Nginx configuration:
```nginx
server {
    listen 80;
    listen [::]:80;

    root /var/www/Obscura/public;
    index index.php;

    error_page 503 /maintenance.php;

    location / {
        if (-f $document_root/../storage/framework/down) {
            return 503;
        }
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

After pasting the configuration, save and exit the editor by pressing CTRL+X, then 'y', and finally Enter.

Now, we need to enable our site by creating a symbolic link:
```bash
sudo ln -s /etc/nginx/sites-available/Obscura /etc/nginx/sites-enabled/
```

Let's remove the default Nginx configuration to avoid any conflicts:
```bash
sudo rm /etc/nginx/sites-enabled/default
```

Before we restart Nginx, let's test our configuration to make sure everything is correct:
```bash
sudo nginx -t
```

If you see a message saying the test is successful, we can safely restart Nginx:
```bash
sudo systemctl restart nginx
```

# Verify Marketplace Installation
Now our Nginx server is configured and should be serving our marketplace on localhost. To test out installation open the default web browser and enter localhost. If the Marketplace login page loads on localhost then we know our installation was successful and now we only have to broadcast our webserver over the Tor Network.

**Important:** Your marketplace requires Tor to function properly. The XMR price fetching feature uses Tor's SOCKS proxy to anonymously retrieve cryptocurrency prices from external APIs. Without Tor configured, your marketplace will display 'UNAVAILABLE' for XMR prices instead of live market data.

The following section will guide you through installing and configuring Tor to enable both the price fetching functionality and publish your marketplace as a hidden service on the Tor network.

# Installing and Configuring Tor Hidden Service & Tor Browser

Install Tor to set up the hidden service functionality:
```bash
sudo apt-get install -y tor
```

Install Tor Browser to navigate to our .onion address:
```bash
sudo apt-get install -y torbrowser-launcher
```

Edit the Tor configuration file to specify our hidden service settings and enable SOCKS proxy:
```bash
sudo nano /etc/tor/torrc
```

Add the following configuration lines at the beginning of the file. The SocksPort enables anonymous XMR price fetching, while the HiddenService settings make your marketplace accessible via .onion address:
```bash
SocksPort 9050
HiddenServiceDir /var/lib/tor/obscura_hidden_service/
HiddenServicePort 80 127.0.0.1:80
```

Save the configuration file by pressing CTRL+X, then 'y' to confirm, and finally press Enter.

# Marketplace Configuration Settings
To edit our marketplace settings we need to use terminal to install dolphin file manager and kate text editor:
```bash
sudo apt-get install -y dolphin kate
```

Next open Dolphin File Manager and navigate to /var/www/Obscura/config/ to see all configuration files.

For this example config we will be setting the Monero node to be used for payment processing. First open terminal and paste the following command:
```bash
kate /var/www/Obscura/config/Monero.php
```
Now Kate Text Editor will open Monero.php for editing, and you should see the monero node config settings, simply set the host, port, and if using https then select yes, if using a Tor node select no for https.

Next we need to ensure that our Monero.php Node Config matches our Node Config in our .env file from earlier using nano as kate does not have access to .env using terminal paste the following command:
```bash
sudo nano /var/www/Obscura/.env
```
Now simply add the Monero Node Host, Port, and SSL selection exactly as our Monero.php input.

There are many other config settings for us to customise, simply open and read all the /var/www/Obscura/config/ PHP files to understand what each configs function is. These configs will be used to edit Advertisement Prices, Marketplace Commission Percentages, Vendor Bond Pricing and much more.

# Change Marketplace Logo
To change the Marketplace logo to your own logo is very simple.

First open dolphin file manager and navigate to /var/www/Obscura/public/images/

Second rename your logo image to obscura.png and then replace your logo with the default logo shown in /var/www/Obscura/public/images

# Restart all Services to Complete Installation
Open a new terminal and restart both the Nginx and Tor services to apply the changes:
```bash
sudo systemctl restart php8.4-fpm
sudo systemctl restart nginx
sudo systemctl restart tor
```

# Access Your New Market
You can now copy your marketplace onion address from the hostname file:
```bash
sudo nano /var/lib/tor/obscura_hidden_service/hostname
```

Now you can launch Tor Browser from your application menu, and paste your marketplace onion and search. After a short load your marketplace login page should now be shown, login with your user:pass that you setup in the database.
