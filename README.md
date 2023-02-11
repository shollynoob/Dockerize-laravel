# Dockerize-laravel

### Create user and add to sudo group

`sudo su`
`adduser olusola`
`usermod -aG sudo olusola`
`su olusola`

![image](https://user-images.githubusercontent.com/5302985/218268484-f4a86fcb-e5be-4fd9-9611-a0293b3dbc1a.png)

### Installing Docker

`sudo apt update`

install a few prerequisite packages which let apt use packages over HTTPS:
`sudo apt install apt-transport-https ca-certificates curl software-properties-common`
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
