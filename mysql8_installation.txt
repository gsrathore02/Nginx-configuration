Mysql (8.0.33)

$apt update

$apt install mysql-server

$systemctl start mysql

$systemctl enable mysql

mysql -V

mysql  Ver 8.0.33-0ubuntu0.20.04.2 for Linux on x86_64

We need to set mysql root user account password

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '8b53&lM#1P3pS$H6';

FLUSH PRIVILEGES;
