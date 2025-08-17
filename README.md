# ObscuraMarketplaceScript
Obscura Marketplace Script, developed by The ObscuraSec Hacking Group, is a DarkNet Market Script that was created by The ObscuraSec Hacking Group.
# Obscura Market
Obscura Market is a DarkNet Market developed by The ObscuraSec Hacking Group. Administrated by ObscuraSnakeHead, Obscura Market is a Monero only DarkNet Market that was developed with OPSec, customer service and functionality in mind.
# Operating System Requirements
The Obscura Marketplace Script is developed on the "Kali Linux" platform, but it is recommended to host the market on "Tails OS (Linux)" for maximum anonymity and OPSec.
# Installation Instructions
Step 1.) Install PHP 8.4 and PHP extenstions by running:
"sudo apt-get install php8.4-fpm php8.4-mysql php8.4-curl php8.4-gd php8.4-mbstring php8.4-xml php8.4-zip php8.4-bcmath php8.4-gnupg php8.4-intl php8.4-readline php8.4-common php8.4-cli php8.4-gmp"

Step 2.) Install unzip module by running:
"sudo apt-get install unzip"

Step 3.) Install composer using the following command:
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
HASH="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"

Step 4.) Install git using the command:
"sudo apt-get install git"

Step 5.) Install MariaDB and Enable it using:
"sudo apt-get install mariadb-server mariadb-client"
"sudo systemctl start mariadb"
"sudo systemctl enable mariadb"

Step 6.) Install and enable nginx using:
"sudo apt install nginx"
"sudo systemctl start nginx"
"sudo systemctl enable nginx"

Step 7.) Install Node and NPM using:
"sudo apt-get install nodejs npm"

Step 8.) MySQL Secure Setup:
"sudo mysql_secure_installation"
When Prompted Choose: Y, password validation level 1, and Y.

Step 9.) Create database and user:
"sudo mysql"

CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';

CREATE DATABASE databasename;

GRANT ALL PRIVILEGES ON databasename.* TO 'user'@'localhost';
FLUSH PRIVILEGES;

exit;

Step 10.) Verify database and user:
"mysql -u your_user -p"
"SHOW DATABASES;"

Step 11.) Marketplace Script Setup:
"cd Downloads"

"git clone https://github.com/ObscuraSnakeHead/ObscuraMarketplace.git"

"sudo mv ObscuraMarketplace /var/www/"

"cd /var/www/ObscuraMarketplace/"

"cp .env.example .env"

"sudo nano .env"
add these lines to .env file:
DB_DATABASE=databasename
DB_USERNAME=user
DB_PASSWORD=password
save .env with CTRL+X 

"npm install"

"composer install"

"php artisan key:generate"

"php artisan migrate"

Step 11b.) Set Folder Permissions Using:
sudo chown -R www-data:www-data /var/www/ObscuraMarketplace
sudo find /var/www/ObscuraMarketplace -type f -exec chmod 644 {} \;
sudo find /var/www/ObscuraMarketplace -type d -exec chmod 755 {} \;
sudo chmod -R 775 /var/www/ObscuraMarketplace/storage
sudo chmod -R 775 /var/www/ObscuraMarketplace/bootstrap/cache
sudo chmod 640 /var/www/ObscuraMarketplace/.env

Step 12.) Setup Nginx Config by:
"sudo nano /etc/nginx/sites-available/ObscuraMarketplace"

Copy and Paste the Config Below:
server {
    listen 80;
    listen [::]:80;

    root /var/www/ObscuraMarketplace/public;
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

Step 12b.) Setup Config SymLinks:
"sudo ln -s /etc/nginx/sites-available/ObscuraMarketplace /etc/nginx/sites-enabled/"

"sudo rm /etc/nginx/sites-enabled/default"

Test Config - "sudo nginx -t" 

Successful Config Run - "sudo systemctl restart nginx"

Step 13.) Configure webserver to be served via Tor:
"sudo apt-get install tor"
"sudo nano /etc/tor/torrc"

Uncomment or Add to Beginning of File:
SocksPort 9050
HiddenServiceDir /var/lib/tor/obscura_hidden_service/
HiddenServicePort 80 127.0.0.1:80

Step 14.) Restart all dependencies using:
sudo systemctl restart php8.4-fpm
sudo systemctl restart nginx
sudo systemctl restart tor

Step 15.) Open web browser and navigate to localhost. The marketplace login page should be visible from here if installation was done correctly.

Step 16.) Get your .onion address:
"sudo nano /var/lib/tor/obscura_hidden_service/hostname"

Step 17.) Install Tor Browser:
"sudo apt-get install torbrowser-launcher"

Step 18.) Run Tor Browser and copy/paste your .onion into the URL box and your new marketplace should now be active.
