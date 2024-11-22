# Deploying Laravel project in Ubuntu

## Update & Upgrade

```
sudo apt update
```
```
sudo apt upgrade
```

## Install apache2

```
sudo apt install apache2
```
### Check if apache2 is running
```
sudo service apache2 status
```

### Configure firewall

```
sudo apt install ufw
```

```
sudo ufw allow "Apache Full"
```
Test your apache2 by visiting VPS IP adresse in your browser

---

### Install PHP & MySQL

#### PHP
```
sudo apt install php libapache2-mod-php php-mbstring php-gd php-xml php-cli php-zip php-json php-curl php-intl php-mysql
```

### MySQL

```
sudo apt install mysql-server
```

#### To set root password, use these commandes:
```
sudo mysql
```

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'YOUR_PASSWORD';
```
#### Then exit:
```
exit
```
#### Installtion:
provide the root password that you set before, for answers choose (no, yes, yes, yes, yes)

```
sudo mysql_secure_installation
```

#### For security create a user following these commandes:
```
sudo mysql -p
```
Enter database root password

#### Create user:
```
CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'StrongPassword123!';
```

#### Create database:
```
CREATE DATABASE laravel_db;
```

#### Grant privileges on created database:
```
GRANT ALL PRIVILEGES ON laravel_db.* TO 'laravel_user'@'localhost';
```
#### Apply the changes:
```
FLUSH PRIVILEGES;
```

#### Then exit:
```
exit
```

## Laravel

```
cd /var/www
```

#### Install git:
```
sudo apt install git
```

#### Clone your laravel project repository:
```
git clone REPOSITORY_URL FOLDER_NAME
```
```
cd FOLDER_NAME
```

#### Set up laravel configuration (.env) using nano
```
cp .env.sample .env
```

```
nano .env
```

#### Install Composer:
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

#### To make it accessible globaly:
```
sudo mv composer.phar /usr/local/bin/composer
```

#### Then run this commande:
```
composer install
```

### Generate laravel app key:
```
php artisan key:generate --ansi
```

### Migrate and seed:
```
php artisan migrate --seed
```

#### Setup folders permissions:
```
sudo chgrp -R www-data /var/www/FOLDER_NAME/
```
Explanation: we're changing folder's group from root to www-data.

```
sudo chmod -R 775 /var/www/FOLDER_NAME/storage/
```
Explanation: we're changing folder's permissions, so group can read, write and execute.

## Virtual Hosts
```
cd /etc/apache2/sites-available/
```

```
sudo nano DOMAIN_NAME.TLD.conf
```

Add to the file:
```
<VirtualHost *:80>
   ServerName DOMAIN_NAME.TLD
   ServerAdmin webmaster@thedomain.com
   DocumentRoot /var/www/FOLDER_NAME/public

   <Directory /var/www/FOLDER_NAME>
       AllowOverride All
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/error.log
   CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Disable default config:
```
sudo a2dissite 000-default.conf
```

Now enable our config:
```
sudo a2ensite DOMAIN_NAME.TLD.conf
```

Then rewrite:
```
sudo a2enmod rewrite
```

Restart apache2:
```
sudo service apache2 restart
```

## Install SSL

```
sudo apt install certbot python3-certbot-apache
```

```
sudo certbot --apache
```
