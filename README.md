# Learn Website Backend [Lumen]
   Simply CRUD with Lumen for Backend API. 
   
   Stagging : [https://api.bignetlab.com/]
   
   Frontend Repo : https://github.com/gilangvperdana/Learn-Vue.js
   
```Environment to Deploy:
Environment:
    1. Linux Ubuntu Server or Windows.
    2. Nginx
    3. Text Editor (VS CODE, Sublime, etc)
    4. Composer
    5. php7.2 (because CPanel recommendation)
    6. Webserver (Nginx for Linux & XAMPP for Windows)
    7. MySQL
```

# Before deploy. Make sure this environment must be configured on LINUX:
```
Dont forget to install mysql server, php7.2 & Composer:
$ sudo apt install mysql-server
$ sudo apt-get install php7.2-mysql
$ apt install composer

After MySQL installed, create database with random name in MySQL.
$ sudo mysql
$ SELECT user,authentication_string,plugin,host FROM mysql.user;
$ ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY '123';
Customize 'CACHING_SHA2_PASSWORD' with your plugin name.

Create Database:
$ CREATE DATABASE DATABASE_NAME;

After database created, goes to ./env and edit the database configuration, adjust it to the database name along with the username and password that you created earlier.
```

# Deploy without WebServer on Linux:
```
Clone this repository
Make sure you have followed the configuration steps above.
$ cd learn-lumen
$ composer install
$ php artisan migrate
$ php -S 0.0.0.0:8000 -t public

Access on : ip/domain.com:8000
```

# Deploy with Nginx on Linux:
```
Clone this repository, 
Make sure you have followed the configuration steps above. then:
$ cd adaptivenetlab-lumen
$ composer install
$ php artisan migrate

The configuration on the project is complete, now it's time for us to configure Nginx as the Lumen webserver.
$ sudo apt install -y nginx
$ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lumenproject
$ sudo nano /etc/nginx/sites-available/lumenproject
on 'lumenproject' file, delete all contents. then fill with:

## This is nginx configuration with SSL:
server {
    listen 80;
    index index.php index.html;
    server_name _;
    root /var/www/html/lumenproject/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_index index.php;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/IPorDOMAIN.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/IPorDOMAIN.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
    
}
server {
    if ($host = www.IP/DOMAIN.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = IP/DOMAIN.com ) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


	listen 80 ;
	listen [::]:80 ;
    server_name www.IP/DOMAIN.com IP/DOMAIN.com;
    return 404; # managed by Certbot
}

don't forget to configure your ip or domain in 'IP/DOMAIN'.
$ sudo apt install certbot python3-certbot-nginx
$ certbot --nginx -d domain.com -d www.domain.com (make sure you have configure DNS with A & CNAME)

## This is nginx configuration without SSL:
server {
    listen 80;
    index index.php index.html;
    server_name _;
    root /var/www/html/lumenproject/public;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_index index.php;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}

save, exit.
After nginx has been configured, move this repo directory to /var/www/html/lumenproject/:
$ cd learn-lumen
$ mv -r * /var/www/html/lumenproject/

After all Lumen project file has been moved, give permission to /storage directory:
$ sudo chmod -Rf 0777 /var/www/html/lumenproject/storage

Apply nginx configuration:
$ sudo ln -s /etc/nginx/sites-available/lumenproject /etc/nginx/sites-enabled
$ sudo nginx -t
$ sudo nginx -s reload
$ systemctl restart nginx

Access on:
https://IPorDOMAIN.com for SSL.
http://IPorDOMAIN.com for without SSL.
```

# Before deploy. Make sure this environment must be configured on WINDOWS:
```
Install XAMPP : https://www.apachefriends.org/download.html
Install Composer : https://getcomposer.org/download/
```

# Deploy with XAMPP on Windows:
```
actually XAMPP here is needed only for database (MySQL) purposes because Lumen has its own webserver.

so, the way on Windows just:
1. Open XAMPP
2. Turn on MySQL Service
3. Go to PHPMyAdmin
4. Create a new database with a random database name
5. Go to the learn-lumen directory
6. Edit the configuration database in the .env file in the learn-lumen directory 
7. Run ' $ composer install '
8. Migrate the project $ php artisan migrate
9. Run ' $ php -S localhost:8000 -t public '
10. Access to http://localhost:8000/
```

# Deploy to CPanel
```
1. UPLOAD LUMEN DENGAN BENTUK ZIP
2. BUAT SUBDOMAIN BARU KE SALAH SATU FOLDER DI FILE MANAGER UNTUK MENEMPATKAN PROJECT LUMEN
3. EKSTRAK ZIP LUMEN PADA FILE MANAGER
LALU BUAT .htaccess PADA FOLDER HASIL EKSTRAKAN DENGAN ISI:
<IfModule mod_headers.c>
Header set Access-Control-Allow-Origin "*"
</IfModule>

# Other option is also enable the headers in your script if you are using PHP, for example:
<?php
header("Access-Control-Allow-Headers: Authorization, Content-Type");
header("Access-Control-Allow-Origin: *");
header('content-type: application/json; charset=utf-8');
?>

4. COPY FILE index.php dan .httaccess PADA FOLDER HASIL EKSTRAKAN LUMEN (Lumen/public/) KE FOLDER SUBDOMAIN
Edit .httaccess dengan hasil akhir seperti ini:
<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews -Indexes
    </IfModule>

    RewriteEngine On

    # Handle Authorization Header
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$
    RewriteRule ^ %1 [L,R=301]

    # Handle Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
</IfModule>

<IfModule mod_headers.c>
Header set Access-Control-Allow-Origin "*"
</IfModule>

# Other option is also enable the headers in your script if you are using PHP, for example:
<?php
header("Access-Control-Allow-Headers: Authorization, Content-Type");
header("Access-Control-Allow-Origin: *");
header('content-type: application/json; charset=utf-8');
?>

5. GANTI INDEX.PHP AGAR MENGARAHKAN APP.PHP KE ARAH FOLDER PROJECT LUMEN YANG TADI DI-EKSTRAK 
6. BUAT DATABASE PADA CPANEL BESERTA USER DAN PASSWORD
7. SETELAH DATABASE TERBUAT SILAHKAN GANTI KONFIGURASI DATABASE PADA FILE .env
8. IMPORT FILE SQL KE PHPMYADMIN CPANEL
9. AKSES WEB SEPERTI BIASA
```

