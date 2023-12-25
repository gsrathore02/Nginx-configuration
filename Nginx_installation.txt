
#Nginx installation (1.23.4)

apt-get update

apt install -y curl gnupg2 ca-certificates lsb-release ubuntu-keyring

curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
| sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

gpg --dry-run --quiet --no-keyring --import --import-options import-show /usr/share/keyrings/nginx-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
    | sudo tee /etc/apt/preferences.d/99nginx

apt update

apt install nginx

nginx -v
systemctl status nginx
systemctl start nginx
systemctl enable nginx

nginx -v

nginx version: nginx/1.25.1


Reference URL :- https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#installing-prebuilt-ubuntu-packages

#Nginx Configuration
---------------------
cd /etc/nginx

cat fastcgi.conf

fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx;
fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;
------------------------------------------------------------------
cd /etc/nginx
cat fastcgi-php.conf

# regex to split $uri to $fastcgi_script_name and $fastcgi_path
fastcgi_split_path_info ^(.+\.php)(/.+)$;

# Check that the PHP script exists before passing it
try_files $fastcgi_script_name =404;

# Bypass the fact that try_files resets $fastcgi_path_info
# see: http://trac.nginx.org/nginx/ticket/321
set $path_info $fastcgi_path_info;
fastcgi_param PATH_INFO $path_info;

fastcgi_index index.php;
include fastcgi.conf;

------------------------------------------------------------------
cat nginx.conf
#user www-data;
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

------------------------------------------------------------------
cd /etc/nginx/conf.d
cat default.conf
server {
        listen      80;
        server_name  localhost;
        root /usr/share/nginx/html/wordpress;
        index index.php index.html index.htm;    
        keepalive_timeout   70;
        #access_log  /var/log/nginx/host.access.log  main;
        client_max_body_size 100M;

       location / {
                # Added support for PHP routers
                # try_files $uri $uri/ /index.php?args;
                try_files $uri $uri/ /index.php?$query_string;
        }
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
                fastcgi_read_timeout 3000;
        }
}
----------------------------------------------------------------
mkdir /etc/nginx/snippets/
cd /etc/nginx/snippets/
vim fastcgi-php.conf
# regex to split $uri to $fastcgi_script_name and $fastcgi_path
    fastcgi_split_path_info ^(.+\.php)(/.+)$;

    # Check that the PHP script exists before passing it
    try_files $fastcgi_script_name =404;

    # Bypass the fact that try_files resets $fastcgi_path_info
    # see: http://trac.nginx.org/nginx/ticket/321
    set $path_info $fastcgi_path_info;
    fastcgi_param PATH_INFO $path_info;

    fastcgi_index index.php;
    include fastcgi.conf;
