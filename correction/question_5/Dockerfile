FROM monsantoco/min-jessie:latest

RUN apt-get update 

RUN apt-get install -y apache2

RUN /etc/init.d/apache2 start

RUN touch /var/log/apache2/error.log

COPY index.html /var/www/html

ENTRYPOINT /etc/init.d/apache2 start && tail -f /var/log/apache2/error.log

