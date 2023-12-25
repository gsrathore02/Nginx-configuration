
Install WordPress – 6.1.2

cd /tmp/

wget https://wordpress.org/wordpress-6.1.2.tar.gz

tar -xvf wordpress-6.1.2.tar.gz

cp -r wordpress /usr/share/nginx/html/

cd /usr/share/nginx/html/

vim /etc/nginx/conf.d/default.conf 

#change the documentroot path as mention below

/usr/share/nginx/html/wordpress

nginx -s reload

chown -R nginx:nginx /usr/share/nginx/html/wordpress/

chmod -R 755 /usr/share/nginx/html/wordpress/

mkdir wordpress/wp-content/uploads

chown -R nginx:nginx wordpress/wp-content/uploads/

http://192.168.54.243/wp-admin/setup-config.php



Enter the Databas name: qntm_wordpress
Username: root
password: 8b53&
hostname: localhost
---------------------------------------------------------------

Note:- if servere ip change and not able to access please run below mentioned mql query.

check existing ip of server using select query.

#Select * from wp_options where option_name IN('siteurl');

update the query using below command

#update wp_options set option_value="http://192.168.2.136" where option_id=1;
