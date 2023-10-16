Install Php7.4 with fpm

apt -y install software-properties-common

add-apt-repository ppa:ondrej/php

apt-get update
------------------------------------------------------------------
### Install php7.4
apt-get install -y php7.4 php7.4-mysql php7.4-fpm php7.4-common php7.4-mbstring php7.4-xmlrpc php7.4-gd php7.4-xml php7.4-mysql php7.4-cli php7.4-zip php7.4-curl php7.4-gmp php7.4-bcmath
------------------------------------------------------------------
### Install php7.0
apt-get install -y php8.0 php8.0-mysql php8.0-fpm php8.0-common php8.0-mbstring php8.0-xmlrpc php8.0-gd php8.0-xml php8.0-mysql php8.0-cli php8.0-zip php8.0-curl php8.0-gmp php8.0-bcmath


------------------------------------------------------------------
Php fpm configuration

cd /etc/php/7.4/fpm/pool.d

cat www.conf

[www]
user = www-data
group = www-data

listen = /run/php/php7.4-fpm.sock

listen.owner = www-data
listen.group = www-data

pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3

##################################################################
Parameter changed required in php.ini

fpm
upload_max_filesize = 100M

cli
upload_max_filesize = 100M

systemctl restart php7.4-fpm
systemctl enable php7.4-fpm
