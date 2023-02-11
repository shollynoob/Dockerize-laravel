# Dockerize-laravel

### Create user and add to sudo group

`sudo su`
`adduser olusola`
`usermod -aG sudo olusola`
`su olusola`
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

### Close laravel repo<br/>
`cd ~`
`git clone https://github.com/laravel/laravel.git laravel-app`

Move into the laravel-app directory<br/>
`cd ~/laravel-app`

 set permissions on the project directory to non-root user<br/>
` sudo chown -R olusola:olusola ~/laravel-app`

<br/>
<br/>
<br/>

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
        uid: 1000
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
   
#Define Volumes
volumes:
  dbdata:
    driver: local
```
