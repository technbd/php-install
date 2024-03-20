## Install PHP 7.4 on centos-7:
To install PHP 7.4 on CentOS 7, you can follow these steps:

**Step-1:**

To install the latest  PHP 7.x, you need to turn on EPEL and Remi repository.

```
yum install -y yum-utils
yum install -y epel-release 
```


```
yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```


**Step-2:**

Enable remi repo as per the desired PHP version, for PHP 7.4, If you want to install PHP 7.3, you can use the following command: "yum-config-manager --enable remi-php73".

```
yum-config-manager --disable 'remi-php*'

yum-config-manager --enable remi-php74
```


**Step-3:**

Now install PHP 7.4 with all necessary modules with the command below:

```
## Install PHP 7.4:

yum update

yum install -y php php-cli php-common php-gd php-mbstring php-curl php-mysql php-ldap php-imap php-zip php-process php-fpm php-mcrypt php-devel php-xml php-json
```


```
yum search php | grep php74
```


### Verify PHP Installation:

```
php -v

PHP 7.4.33 (cli) (built: Dec 12 2023 14:45:16) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```


```
php -m

[PHP Modules]
bz2
calendar
Core
ctype
curl
date
exif
fileinfo
filter
ftp
gd
...
...
```

**Some of PHP following modules:**

- php7.4-cli - command interpreter, useful for testing PHP scripts from a shell or performing general shell scripting tasks
- php7.4-json - for working with JSON data
- php7.4-common - documentation, examples, and common modules for PHP
- php7.4-mysql - for working with MySQL databases
- php7.4-zip - for working with compressed files
- php7.4-gd - for working with images
- php7.4-mbstring - used to manage non-ASCII strings
- php7.4-curl - lets you make HTTP requests in PHP
- php7.4-xml - for working with XML data
- php-ldap - PHP LDAP extension for LDAP support
- php7.4-bcmath - used when working with precision floats


### Test PHP Installation:

To test PHP functionality create a "phpinfo.php" file in "/var/www/html" with following contents and Next, open with the web browser: http://your-server-ip/phpinfo.php

```
<?php
phpinfo();
?>
```


```
systemctl start httpd
```

This confirms that you have successfully installed PHP 7.4 on your system.


