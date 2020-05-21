  # DOCKER_PROJECT_Nextcloud

I have created a Docker Project using a cloud called NextCloud and MySQL Database on top of RedHat8 Linux OS. Here I am  going to show you the whole set-up step by step. 


## 1) Download the RedHat 8 Linux 
## 2) Download the Docker tool

First of all configure the yum command in linux and download the software by:
        
    yum install docker-ce --nobest 
and start the docker by command 

    systemctl start docker.
 ## 3) Download the Required Images
Download The NextCloud and MySQL image from https://hub.docker.com using by following command.
    
    docker pull nextcloud:latest
    docker pull mysql:5.7 

 ## 4) MySQL setup

Write a command 

    docker -i -t -e MYSQL_ROOT_PASSWORD=(your_password) -e MYSQL_USER=(username)
		-e MYSQL_PASSWORD=(your_password) -e MYSQL_DATABASE=     (any_database_name) 
		                       --name dbos mysql:5.7
 ## 5) MySQL client
If you want to verify that your database folder has been created or not, install MySQL client software using yum install mysql. After installation run this command : 
   
    mysql -h 172.17.0.0/16 (your MySQL container IP) -u (username) -p
 
 ## 6) Docker-Compose
Install a docker-compose software from https://docs.docker.com/compose/install. 
Make a compose file using 

    mkdir mycompose. 

You can create/edit your docker-compose file using 
    
    vim docker-compose.yml
The file name should always be docker-compose.yml

This is my .yml file :

      
![docker-compose1](https://github.com/sanket3122/DOCKER_PROJECT_Nextcloud/blob/master/docker-compose1.PNG)

You can start/stop the service in just a single command using docker-compose up and docker-compose stop. 
   #### Version :
Each version has different syntax. This version supports docker-compose fine. 
   #### Services : 
In service, which containers we want to run.
   #### Containers :
dbos and cloudos are the name of docker container. Docker-compose will automatically create the specified name containers.
   ####  Image : 
Specify the image we want to use.
   #### Restart :
By chance any of the container stops, docker-compose will restart it again.
   #### Volumes : 
All our data will be permanent if we mount a volume to the folders where NextCloud and MySQL stores data. The data will remain permanent if any of the container terminates. For that you have to create volumes first. 
   #### Depends_on : 
NextCloud uses database server. We have to specify which database container it should depend on.
   #### Ports : 
To expose our container to outside world by using PAT.
   
   ## 7) Troubleshooting the errors
Linux firewall won't allow to connect to MySQL database server and to outside world. Hence following commands should be implemented first in order to connect to server.
- [x] selinux 0
- [x] iptables -F
- [x] iptables -P FORWARD ACCEPT
- [x] firewall-cmd --zone=trusted --change-interface=docker0 --permanent 
(If there are any other networks for docker add them too like br-xxxxx)
- [x] firewall-cmd --zone=trusted --add-masquerade --permanent
- [x] firewall-cmd --add-port=3306/tcp
- [x] firewall-cmd --reload
- [x] systemctl restart docker
- [x] Change your network settings to Bridge Adapter
         
   That's it. You can start/stop your services in just a single go.  
            :+1: NOTE  : - It only works on other devices if you have a same network connectivity (LAN). You can use any of the images like WordPress, Joomla, Drupal, Owncloud, etc and Database images like MariaDB, Oracle, etc.
