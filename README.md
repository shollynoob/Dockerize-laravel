# Dockerize-laravel

### Create user and add to sudo group<br/>

`sudo su`<br/>
`adduser olusola`<br/>
`usermod -aG sudo olusola`<br/>
`su olusola`<br/>
<br/>
<br/>
<br/>

### Installing Docker

`sudo apt update`

install a few prerequisite packages which let apt use packages over HTTPS<br/>
`sudo apt install apt-transport-https ca-certificates curl software-properties-common`

add the GPG key for the official Docker repository<br/>
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

Add the Docker repository to APT sources<br/>
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"`

Ensure docker is install from docker repo insted of default ubuntu<br/>
`apt-cache policy docker-ce`

install Docker<br/>
`sudo apt install docker-ce -y`
<br/>
<br/>
<br/>

### Executing the Docker Command Without Sudo<br/>

add username to Docker group<br/>
`sudo usermod -aG docker ${USER}`

apply gorup memebership<br/>
`su - ${USER}`
<br/>
<br/>
<br/>

### installing docker compose<br/>
`sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose`

set permission for docker compose<br/>
`sudo chmod +x /usr/local/bin/docker-compose`

<br/>
<br/>
<br/>

### Clone laravel repo<br/>
`cd ~`
`git clone https://github.com/laravel/laravel.git laravel-app`

Move into the laravel-app directory<br/>
`cd ~/laravel-app`

 set permissions on the project directory to non-root user<br/>
` sudo chown -R olusola:olusola ~/laravel-app`

<br/>
<br/>
<br/>

Determine the user UID to be used in the docker compose<br/>
`cat /etc/passwd`

### creating Docker-compose<br/>
`nano ~/laravel-app/docker-compose.yml`

```
version: '3'
services:
  
  #Setup PHP Service
  app:
    build:
      args:
        user: olusola
        uid: 1001
      context: .
      dockerfile: Dockerfile
    image: olusola/php
    container_name: app
    restart: unless-stopped
    tty: true
    environment:
      SERVICE_NAME: app
      SERVICE_TAGS: dev
    working_dir: /var/www
    volumes:
      - ./:/var/www
      - ./php/local.ini:/usr/local/etc/php/conf.d/local.ini
    networks:
      - olusola-network

  #Setup Nginx Service
  webserver:
    image: nginx:alpine
    container_name: webserver
    restart: unless-stopped
    tty: true
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./:/var/www
      - ./nginx/conf.d/:/etc/nginx/conf.d/
      - ./nginx-logs:/var/log/nginx
    networks:
      - olusola-network

  #Setup MySQL Service
  db:
    image: mysql:5.7.22
    container_name: db
    restart: unless-stopped
    tty: true
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: ${DB_DATABASE}
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
      MYSQL_PASSWORD: ${DB_PASSWORD}
      MYSQL_USER: ${DB_USERNAME}
    volumes:
      - dbdata:/var/lib/mysql/
      - ./mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - olusola-network

#Docker Networks
networks:
  olusola-network:
    driver: bridge
#Volumes
volumes:
  dbdata:
    driver: local
```
<br/>
<br/>
<br/>

### creating Docker file<br/>
`nano ~/laravel-app/Dockerfile`

```
FROM php:8.0.2-fpm

# Arguments defined in docker-compose.yml
ARG user
ARG uid

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

# Set working directory
WORKDIR /var/www

USER $user

```


<br/>
<br/>
<br/>

configure PHP, NGINX and MYSQL<br/>

#### PHP<br/>
`mkdir ~/laravel-app/php`

create local.ini file<br/>
`nano ~/laravel-app/php/local.ini`

Add below<br/>
```
upload_max_filesize=40M
post_max_size=40M
```

#### NGINX<br/>
make the nginx/conf.d/ directory<br/>
`mkdir -p ~/laravel-app/nginx/conf.d`

create the app.conf configuration file<br/>
`nano ~/laravel-app/nginx/conf.d/app.conf`

add below<br/>

```
server {
    listen 80;
    index index.php index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /var/www/public;
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```
#### MYSQL<br/>
create the mysql directory<br/>
`mkdir ~/laravel-app/mysql`

make the my.cnf file<br/>
`nano ~/laravel-app/mysql/my.cnf`

add below in my.cnf<br/>
```
[mysqld]
general_log = 1
general_log_file = /var/lib/mysql/general.log
```

Making a copy of the.env.example<br/>
`cp .env.example .env`

Edit .env<br/>
`nano .env`

Updating below on .env<br/>
```
DB_CONNECTION=mysql
DB_HOST=*******
DB_PORT=************
DB_DATABASE=*************
DB_USERNAME=*************
DB_PASSWORD=*************
``` 
<br/>
<br/>
<br/>
Run below to build<br/>
`docker-compose build app` <br/>
`docker-compose up -d`<br/>

Run composer install to install the application dependencies<br/>
`docker-compose exec app composer install` <br/>

Encrypt session and data using <br/>
`docker-compose exec app php artisan key:generate`


VM is is `20.106.74.42`

![image](https://user-images.githubusercontent.com/5302985/218298407-5852ac82-b972-4e24-a7d6-1854a9381490.png)
