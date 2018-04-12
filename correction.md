# Web infrastructure correction

## 1 Installing Apache 2

In the `/etc/hosts` file of your host computer, register the needed domain names:

```
127.0.0.1 test.openup.com phpmyadmin.openup.com wordpress.openup.com hello.openup.com
```

Install Apache 2 web server

```
apt-get install apache2
```

Create the website content:

```
$ cd /var/www
/var/www $ mkdir test.openup.com
/var/www $ mkdir hello.openup.com

/var/www/test.openup.com $ cat index.html
This is test.openup.com

/var/www/hello.openup.com $ cat index.html
This is hello.openup.com
```

We now need to register virtual hosts for these websites in Apache 2:

```
$ cat /etc/apache2/sites-available/test.openup.com.conf
<VirtualHost *:80>
	ServerName test.openup.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/test.openup.com

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

```
$ cat /etc/apache2/sites-available/hello.openup.com.conf
<VirtualHost *:80>
	ServerName hello.openup.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/hello.openup.com

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Now we need to link them into the sites-enabled folder to activate them (and only them):

```
cd /etc/apache2/sites-enabled
/etc/apache2/sites-enabled $ rm *
/etc/apache2/sites-enabled $ ln -s ../sites-available/test.openup.com.conf
/etc/apache2/sites-enabled $ ln -s ../sites-available/hello.openup.com.conf
```

We reload the configuration:

```
/etc/init.d/apache2 reload
```

To access the website from your host computer, you can set up port forwarding:

 - In virtualbox click on `settings` for your virtual machine
 - Go to network settings
 - Select card 1. It should be NAT configured.
 - Go to advanced settings.
 - Click port forwarding/port redirection button.
 - Here is the port forwarding you need to set: `Host IP: 127.0.0.1, Host port: 8000, Guest IP: 127.0.0.1, Guest port: 80` (you may need to change guest ip to one listed by ifconfig)

Open [http://test.openup.com:8000](http://test.openup.com:8000) and [http://hello.openup.com:8000](http://hello.openup.com:8000) in your browser

They should display different content. If so, the question is a success.

## 2 Executing PHP

We need to add  the module for executing PHP to Apache 2.

To find the right package:

```
$ apt-cache search lib mod apache2 php
...
libapache2-mod-php5 - server-side, HTML-embedded scripting language (Apache 2 module)
...
$ apt-get install libapache2-mod-php5
```

Then we can create some PHP content:

```
mv /var/www/test.openup.com/index.html /var/www/test.openup.com/index.php
# edit index.php
cat /var/www/test.openup.com/index.php
<?php echo "[PHP]: Hello world!" ?>
```

Open [http://test.openup.com:8000](http://test.openup.com:8000). You should see the `[PHP]: Hello world!` message.

## 3 Setting up a database

We will install `mariadb-server` : 

```
apt-get install mariadb-server
```

You will be prompted for the database password. Give one, and remember it!

Now we can install `phpmyadmin` to manage our SQL database:

```
apt-get install phpmyadmin
```

You will be propted for:
 - MySQL password. It is the previous value.
 - A phpmyadmin password (need to be confirmed). I advise to put the same to avoid confusion.

We need to create a apache2 virtualhost for it:

```
$ cat /etc/apache2/sites-available/phpmyadmin.openup.com.conf 
<VirtualHost *:80>
	ServerName phpmyadmin.openup.com

	ServerAdmin webmaster@localhost
	DocumentRoot /usr/share/phpmyadmin

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Again we need to link it to sites-enabled:

```
$ cd /etc/apache2/sites-enabled
/etc/apache2/sites-enabled $ ln -s ../sites-available/phpmyadmin.openup.com.conf
```

We reload the configuration:

```
/etc/init.d/apache2 reload
```

We can connect to [http://phpmyadmin.openup.com:8000](http://phpmyadmin.openup.com:8000). We connect with root credentials. We create a new `wordpress` user (users form). We don't forget to create a database for user wordpress (check box at the bottom of the creation form).

## 4 Installing wordpress

We need to get wordpress website. We will download it from the official website:

```
cd /var/www
/var/www $ wget https://wordpress.org/latest.zip
/var/www $ unzip latest.zip
```

Wordpress needs to be able to create some configuration pages. Thus we should make `www-data` the owner of `/var/www/wordpress` :

```
chown -R www-data:www-data /var/www/wordpress/
```

Again we will create a virtualhost:

```
$ cat /etc/apache2/sites-available/wordpress.openup.com.conf
<VirtualHost *:80>
	ServerName wordpress.openup.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/wordpress

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Again we need to link it to sites-enabled:

```
$ cd /etc/apache2/sites-enabled
/etc/apache2/sites-enabled $ ln -s ../sites-available/wordpress.openup.com.conf
```

We reload the configuration:

```
/etc/init.d/apache2 reload
```

We can connect to [http://wordpress.openup.com:8000](http://wordpress.openup.com:8000). We can finish the installation:
 - Database name : `wordpress`
 - Database user : `wordpress`. Remember the credential you put in step 4...
 - Continue...
 - Give some information for your website form.

And you are done! You can play a bit and customize your website more...

## 5 NGinx and SSL

We will start by installing NGinx:

```
apt-get install nginx
```

You will notice nginx do not start as it tries to bind port 80, already bound by apache.

#### Reverse proxying

Let's start by setting up a reverse proxy for test.openup.com...

1.

We need domain name resolution in the guest. Edit /etc/hosts and add:

```
127.0.0.1 test.openup.com phpmyadmin.openup.com wordpress.openup.com hello.openup.com
```

2.

Let's create the configuration file for test.openup.com reverse proxy:

```
/etc/nginx/sites-available# cat test.openup.com 
server {
	listen 443;
	listen [::]:443;

	server_name test.openup.com;

	location / {
		proxy_pass http://test.openup.com:80;
	}
}

```

3.

Let's enable it:

```
cd /etc/nginx/sites-enabled
/etc/nginx/sites-enabled# rm *
/etc/nginx/sites-enabled# ln -s ../sites-available/test.openup.com
```

4.

start nginx (by removing the default website we solved the port conflict)

```
/etc/init.d/nginx start
```

5.

Set up port redirection for `port 443`:

 - In virtualbox click on `settings` for your virtual machine
 - Go to network settings
 - Select card 1. It should be NAT configured.
 - Go to advanced settings.
 - Click port forwarding/port redirection button.
 - Here is the port forwarding you need to set: `Host IP: 127.0.0.1, Host port: 4443, Guest IP: 127.0.0.1, Guest port: 443` (you may need to change guest ip to one listed by ifconfig)

6.

Open [http://test.openup.com:4443](http://test.openup.com:4443) in your browser.

#### SSL set up

We can generate SSL keys/certificate quite simply:

```
mkdir /etc/nginx/ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
```

Now we cange our nginx reverse proxy configuration to set up encryption:

```
/etc/nginx/sites-available# cat test.openup.com 
server {
	listen 443 ssl;
	listen [::]:443 ssl;

	server_name test.openup.com;
	
        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;

	location / {
		proxy_pass http://test.openup.com:80;
	}
}
```

We can reload nginx configuration:

```
/etc/init.d/nginx reload
```

And connect to [https://test.openup.com:4443/](https://test.openup.com:4443/). As the certificate is self signed, we have some warning in the browser that needs to be ignored. You can add a security exception.

## 6 Setting up a FTP server for uploads

This, is left for students as an exercice.
