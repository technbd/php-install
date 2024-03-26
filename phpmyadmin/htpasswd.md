
## htpasswd:
**.htpasswd** file is typically used when protecting a file, folder, or entire website with a password using HTTP authentication and implemented using rules within the .htaccess file. User credentials are stored on separate lines, with each line containing a username and password separated by a colon (:). Usernames are stored in plain text but passwords are stored in an encrypted hashed format.


```
### For Debian/Ubuntu:

apt update 
apt install apache2-utils -y
```


```
### For CentOS/RHEL:

yum install httpd-tools -y 
```


### htpasswd syntax: 

- c: Create a new password file.
- n: Display the results on standard output, without updating the password file.
- b: Use the command line to provide the password, rather than prompting for it.
- D: Delete the specified user from the password file.
- m: Use MD5 encryption for passwords (default for most systems).


```
htpasswd [options] password-file username
```


```
### Creating a new password file: 
htpasswd -c /path/to/your/.htpasswd username 

### Adding a new user to an existing password file:
htpasswd /path/to/your/.htpasswd new_username 

### Updating a user’s password:
htpasswd /path/to/your/.htpasswd existing_username 

### Deleting a user:
htpasswd -D /path/to/your/.htpasswd username_to_delete 
```


### Apache2 web server Authentication: [method-1]
### Ubuntu:

```
htpasswd -c /etc/apache2/.htpasswd user1
	New password: admin1
	Re-type new password:
```


```
ll /etc/apache2/sites-available/000-default.conf
```


```
vim /etc/apache2/sites-available/000-default.conf


<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

  ### Add this line: 
  ## For secure http:
  <Directory "/var/www/html">
      AuthType Basic
      AuthName "Restricted Content"
      AuthUserFile /etc/apache2/.htpasswd
      Require valid-user
  </Directory>

   ## For secure phpmyadmin: [for phpmyadmin]
  <Directory /usr/share/phpmyadmin>
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
  </Directory>

</VirtualHost>
	
	

save and quit
```


```
systemctl restart apache2
```


Now you can access phpMyAdmin using the URL: **your_ip/phpmyadmin**


### phpmyadmin Authentication: [method-2]
### Ubuntu:

```
htpasswd -c /etc/phpmyadmin/.htpasswd admin
	New password: admin
	Re-type new password:
```


```
ll /etc/phpmyadmin/apache.conf
ll /usr/share/phpmyadmin
```


```
vim  /etc/phpmyadmin/apache.conf

Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php

    # Add this line for htpasswd authentication:
    AllowOverride All

    # limit libapache2-mod-php to files and directories necessary by pma
    <IfModule mod_php7.c>
       php_admin_value upload_tmp_dir /var/lib/phpmyadmin/tmp
    ...
    ...

    </IfModule>

</Directory>

# Disallow web access to directories that don't need it
<Directory /usr/share/phpmyadmin/templates>
    Require all denied
</Directory>

<Directory /usr/share/phpmyadmin/libraries>
    Require all denied
</Directory>


save and quit
```


**Create the file:**

```
vim /usr/share/phpmyadmin/.htaccess

AuthType Basic
AuthName "Stay Away"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user


save and quit
```


```
systemctl restart apache2
```

Now you can access phpMyAdmin using the URL: **your_ip/phpmyadmin**


---
---


### HTTPD/Apache web server Authentication: (method-1)
### CentOS/RHEL: 


```
htpasswd -c /etc/httpd/.htpasswd user3
```


```
ll /usr/share/phpMyAdmin
```


```
vim /etc/httpd/conf.d/vhost.conf

<VirtualHost *:80>
    ServerName www.yourdomain.com
    ServerAlias yourdomain.com

    DocumentRoot /var/www/html

    ErrorLog /var/log/httpd/error_log
    CustomLog /var/log/httpd/access_log combined


  ## For secure http:
  <Directory "/var/www/html">
      AuthType Basic
      AuthName "Restricted Content"
      AuthUserFile /etc/httpd/.htpasswd
      Require valid-user
  </Directory>

  ## For secure phpmyadmin: [for phpmyadmin]
  <Directory /usr/share/phpMyAdmin>
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /etc/httpd/.htpasswd
    Require valid-user
  </Directory>

</VirtualHost>


save and quit
```


```
systemctl restart httpd
```


Now you can access phpMyAdmin using the URL: **your_ip/phpmyadmin**



### phpmyadmin Authentication (Method-2):
### CentOS/RHEL: 

```
ll /etc/phpMyAdmin/
```


```
htpasswd -c /etc/phpMyAdmin/.htpasswd admin
	New password: admin
	Re-type new password:
```


```
ll /usr/share/phpMyAdmin/
```


```
vim  /etc/httpd/conf.d/phpMyAdmin.conf

Alias /phpMyAdmin /usr/share/phpMyAdmin
Alias /phpmyadmin /usr/share/phpMyAdmin

<Directory /usr/share/phpMyAdmin/>
   AddDefaultCharset UTF-8


######## For phpmyadmin auth ########
#    Options SymLinksIfOwnerMatch
#    DirectoryIndex index.php
    AllowOverride All

######## end ########


   <IfModule mod_authz_core.c>
     # Apache 2.4
     <RequireAny>
       Require ip 192.168.10.44
       Require ip ::1
     </RequireAny>
   </IfModule>

   <IfModule !mod_authz_core.c>
     # Apache 2.2
     Order Deny,Allow
     Deny from All
     Allow from 192.168.10.44
     Allow from ::1
   </IfModule>
</Directory>

...
...


save and quit
```


**Create the file:**

```
vim /usr/share/phpMyAdmin/.htaccess

AuthType Basic
AuthName "Stay Away"
AuthUserFile /etc/phpMyAdmin/.htpasswd
Require valid-user


save and quit
```


```
systemctl restart httpd
```


Now you can access phpMyAdmin using the URL: **your_ip/phpmyadmin**

