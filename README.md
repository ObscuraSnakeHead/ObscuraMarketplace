# ObscuraMarketplaceScript
Obscura Marketplace Script, developed by The ObscuraSec Hacking Group, is a DarkNet Market Script that was created by The ObscuraSec Hacking Group.
# Obscura Market
Obscura Market is a DarkNet Market developed by The ObscuraSec Hacking Group. Administrated by ObscuraSnakeHead, Obscura Market is a Monero only DarkNet Market that was developed with OPSec, customer service and functionality in mind.
# Operating System Requirements
The Obscura Marketplace Script is developed on the "Kali Linux" platform, but it is recommended to host the market on "Tails OS (Linux) for maximum anonymity and OPSec.
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








