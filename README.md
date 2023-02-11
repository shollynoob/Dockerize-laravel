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
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
