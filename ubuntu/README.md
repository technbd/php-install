## Install PHP 7.4 on Ubuntu 20.04:
To install PHP 7.4 on Ubuntu 20.04, the defaults install PHP 7.4.


```
sudo apt update
sudo apt install -y php php-cli php-fpm php-json php-common php-mysql php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath php-ldap php-imap
```


### Install PHP with Apache2 on Ubuntu:
Follow these steps to complete PHP installation on Ubuntu:

**Step-1:**
Install dependencies required like software-properties-common and etc to help you manage distributions and independent software sources:

```
sudo apt update

sudo apt install software-properties-common ca-certificates lsb-release apt-transport-https 
```


**Step-2:**
Add the "ondrej/php" PPA to your system, which provides all versions of PHP packages for the Ubuntu systems. After the PPA loads, press Enter to confirm adding the repository. 

```
sudo add-apt-repository ppa:ondrej/php

or,

LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php
```


**Step-3:**
Now you can install specific PHP versions. For example:

```
### Install PHP 7.4:

sudo apt update

sudo apt -y install php7.2 
sudo apt -y install php7.4
sudo apt -y install php8.1
sudo apt -y install php5.6 
```


You can use the "update-alternatives" command to set the default PHP version.

```
sudo update-alternatives --config php
```


```
systemctl status apache2
```


### Test PHP Installation (apache2):

To test PHP functionality create a "phpinfo.php" file in "/var/www/html" with following contents and Next, open with the web browser: http://your-server-ip/phpinfo.php

```
<?php
phpinfo();
?>
```


---
---


###  Install PHP with Nginx on Ubuntu:
To install PHP for Nginx, use one of the following commands:

```
sudo add-apt-repository ppa:ondrej/php
```


```
sudo apt install php7.4-fpm -y

sudo apt install php8.1-fpm -y
```


To enable PHP support by editing the server block:

```
vim /etc/nginx/sites-available/default

server {
    listen 80 default_server;
    # . . .
    # . . .

    root /var/www/html;

    # Add index.php to the list if you are using PHP
    index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        # Add the following line to your server block file for Nginx to make use of PHP:
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        }


}

save and quite
```


```
sudo systemctl restart nginx
sudo systemctl status nginx
```


```
sudo systemctl reload php7.4-fpm
sudo systemctl status php7.4-fpm
```


### Test PHP Installation (Nginx):

To test PHP functionality create a "phpinfo.php" file in "/var/www/html" with following contents and Next, open with the web browser: http://your-server-ip/phpinfo.php

```
<?php
phpinfo();
?>
```



### Verify PHP Installation:

```
apt-cache search php | grep php7.4*
apt show php7.4
apt-cache show php7.4
```


```
php -v

PHP 7.4.33 (cli) (built: Sep  2 2023 08:03:15) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.33, Copyright (c), by Zend Technologies
```


```
php -m

[PHP Modules]
calendar
Core
ctype
date
exif
FFI
fileinfo
filter
ftp
...
...
```

### PHP Module Install: 
Some applications required other PHP extensions, that can also be added using the below-mentioned syntax:

```
sudo apt install php7.4-[extension]

sudo apt install php7.4-mysql php7.4-mbstring php7.4-gd php7.4-xml php7.4-curl -y
```


```
ll /etc/php/7.4/mods-available/
```


### Enable php module:

The command phpenmod is typically used in Debian-based distributions like Ubuntu to enable PHP modules.
- phpenmod: Used to enable modules in PHP
- phpdismod: Used to disable modules in PHP
- phpquery: Used to view status of modules of PHP


```
phpquery -v 7.4 -s apache2 -m gd
```


```
### Syntax
sudo phpenmod <MODULE_NAME>

sudo phpenmod gd

sudo phpdismod gd
```


"a2enmod" command, which is typically used in Apache HTTP Server environments to enable Apache modules. 

```
a2query -m

setenvif (enabled by maintainer script)
mime (enabled by maintainer script)
authz_host (enabled by maintainer script)
status (enabled by maintainer script)
dir (enabled by maintainer script)
autoindex (enabled by maintainer script)
authn_file (enabled by maintainer script)
mpm_prefork (enabled by maintainer script)
authz_core (enabled by maintainer script)
access_compat (enabled by maintainer script)
authn_core (enabled by maintainer script)
auth_basic (enabled by maintainer script)
php7.4 (enabled by maintainer script)
```

```
sudo a2enmod ldap
sudo a2dismod ldap
```


### Uninstall PHP:

```
sudo apt purge php<version>

sudo apt purge php7.4
sudo apt remove php7.4
sudo apt autoremove
```


This confirms that you have successfully installed PHP 7.4 on your system.


