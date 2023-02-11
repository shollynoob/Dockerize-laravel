# Dockerize-laravel

### Create user and add to sudo group

`sudo su`
`adduser olusola`
`usermod -aG sudo olusola`
`su olusola`
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






### Executing the Docker Command Without Sudo<br/>

add username to Docker group<br/>
`sudo usermod -aG docker ${USER}`

apply gorup memebership<br/>
`su - ${USER}`
<br/>






### installing docker compose<br/>
`sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose`
