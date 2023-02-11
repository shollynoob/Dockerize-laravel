# Dockerize-laravel

#### Create user and add to sudo group

`sudo su`
`adduser olusola`
`usermod -aG sudo olusola`
`su olusola`


#### Installing Docker

`sudo apt update`

install a few prerequisite packages which let apt use packages over HTTPS:<br/>
`sudo apt install apt-transport-https ca-certificates curl software-properties-common`

add the GPG key for the official Docker repository
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

Add the Docker repository to APT sources:
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"`

Ensure docker is install from docker repo insted of default ubuntu
`apt-cache policy docker-ce`

install Docker
`sudo apt install docker-ce -y`

#### Executing the Docker Command Without Sudo

add username to Docker group
`sudo usermod -aG docker ${USER}`

apply gorup memebership
`su - ${USER}`
