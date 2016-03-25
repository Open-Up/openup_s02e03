# Docker and Apache

In this practical work, we will see docker basis. You will know how to deploy a server in a docker container.

## Exercice 1

Docker Hello world :

Create a docker container with an entry point that says Hello world.

## Exercice 2

Docker Hello world 2 :

Create a docker container that read the content of a file in a container and prints its content, setted to "Hello world".

## Exercice 3

Docker Hello world 3 :

Create a docker container that read the content of a file outside the container and prints its content.

## Exercice 4

Install Apache in a docker container. You should be able to render the default page in your browser.

Hint : remember to call apt-get update before installing package apache2.

Hint 2 : You can not call */etc/init.d/apache2 start* or equivalent as an entry point.

Hint 3 : You need to load */etc/apache2envvars* before launching apache2

Hint 4 : You need to initialize directories. I did that with a **RUN /etc/init.d/apache2 start** (not for starting server ! Yes, it's cheeting....) 

## Exercice 5

Replace this default page by yours ! (Hello world "your name"!) for instance.

Hint : you need to replace /var/www/html/index.html by yours !

## Exercice 6

Add a virtual host to Apache and a second site. You should be able to access both sites using your browser.

You should have the two site's domain names in your */etc/hosts* file, pointing to 127.0.0.1.

Hint : you need to secify a server name for /etc/apache2/sites-enabled/000-default.conf
