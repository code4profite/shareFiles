

# Run Docker
docker pull ubuntu
winpty docker run -v '//c/Users/dell/Desktop/app/wordpress:/var/www/html/' --cap-add=NET_ADMIN  --cap-add=sys_nice -it -p 0.0.0.0:80:80 -p 0.0.0.0:443:443 -p 0.0.0.0:3306:3306 -p 0.0.0.0:22:22 lempww bash
passwd root
apt-get install software-properties-common -y
apt-add-repository ppa:ondrej/php
apt update && apt upgrade && apt install sudo && apt install ssh -y && apt install nginx -y && apt install ufw -y && apt install mysql-server -y && apt install php php-cli php-common php-fpm -y && apt install vim
ufw allow http && ufw allow https && ufw allow ssh
service nginx start
service mysql start
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by '1234';
mysql_secure_installation
Change the password for root:no
service mysql restart
apt install php-fpm
groupadd wordpress_user
useradd -g wordpress_user wordpress_user

vim /etc/php/8.1/fpm/pool.d/wordpress_pool.conf

[wordpress_site]
user = wordpress_user
group = wordpress_user
listen = /var/run/php8.1-fpm-wordpress-site.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off
; Choose how the process manager will control the number of child processes.
pm = dynamic
pm.max_children = 75
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
pm.process_idle_timeout = 10s

php-fpm8.1 -t
service php8.1-fpm start


server {
         listen       80;
         server_name  localhost;
         root         /var/www/html/;

         index index.html index.htm index.php;

         location / {
            try_files $uri $uri/ /index.php$is_args$args;
         }

         location ~ \.php$ {
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            fastcgi_pass unix:/var/run/php8.1-fpm-wordpress-site.sock;
            fastcgi_index index.php;
            include fastcgi.conf;
    }
}
nginx -t 
nginx -s reload
cd /usr/share/nginx/html/
apt install unzip
wget https://wordpress.org/latest.zip
unzip latest.zip
mv wordpress1/ /var/www/html/

apt install php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip php-mysqli php-mysql php-cli php-opcache php-redis php-soap php-zip -y

ln -s /usr/lib/php/20210902/ /etc/php/8.1/fpm/extension_dir
vim /etc/php/8.1/fpm/php.ini
/extension_dir 
extension_dir = '/etc/php/8.1/fpm/extension_dir/'

create database wordpress;
CREATE USER 'wordpress1'@'localhost' IDENTIFIED WITH mysql_native_password  BY '1234';
GRANT ALL PRIVILEGES ON *.* TO 'wordpress1'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

https://stackoverflow.com/questions/20203379/linux-wordpress-cant-not-write-wp-config-file

chown -R wordpress_user:wordpress_user /var/www/html/wordpress

find /var/www/html/wordpress -type d -exec chmod 755 {} \;
find /var/www/html/wordpress -type f -exec chmod 644 {} \;
chmod -R 755 /var/www/html/wordpress/wp-content
chmod 440 wordpress/wp-config.php
remove man and nano