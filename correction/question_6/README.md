Edit /etc/hosts

127.0.0.1	localhost.localdomain	localhost    openup default


docker build --tag=apache-vhosts .

docker run -p 80:80 apache-vhosts
