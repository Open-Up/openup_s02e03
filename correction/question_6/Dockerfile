FROM monsantoco/min-jessie:latest

RUN apt-get update 

RUN apt-get install -y apache2

RUN /etc/init.d/apache2 start

RUN touch /var/log/apache2/error.log

COPY 000-default.conf /etc/apache2/sites-enabled/
COPY openup.conf /etc/apache2/sites-enabled/
COPY openup.html /var/www/openup/index.html

ENTRYPOINT /etc/init.d/apache2 start && tail -f /var/log/apache2/error.log
