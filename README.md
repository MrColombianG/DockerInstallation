# DockerInstallation
This is a Docker Installation with a wordpress tutorial installation.
Created by: Andres Vargas
Reference: https://linuxiac.com/wordpress-with-docker/

## Installing Docker 
I used Debian 11x.64 bit on VMWare Workstation Pro for this installation. This instructions should work on most linux distrubtions with minor details. 

Once you have your linux distrubtion installed and have logged into your VM, open up the terminal and begin to install packages. 

**Update apt (package index)** 
~~~
# sudo apt update 
~~~
**Installing Packages** 

Install Certificates: This allows apt to use a repositroy over HTTPS when downloading packages.
~~~
# sudo apt install ca-certificates curl gnupg lsb-release
~~~
If an error occurred use this format as some linux distros have different syntax.
~~~
# sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
~~~
Connect to installation packages: This adds Docker’s official GPG key 
~~~
# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | >sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
~~~
The following command will then set up a stable repository 
~~~
# echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
~~~
Update apt again and beign installing the container 
~~~
# sudo apt update
# sudo apt install docker-ce docker-ce-cli containered.io
~~~
Then add an admin user to the docker group: This will create your admin user
~~~
# sudo usermod -aG docker admin 
~~~
**Install Docker Compose**
~~~
# sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
~~~
Give compose the appropriate permissions
~~~
# sudo chmod +x /usr/local/bin/docker-compose
~~~
Use `docker-compose--version` to test and make sure docker is installed and is the right version. 

# Wordpress Install 
WordPress is a very popular online tool used to create webistes. We will be installing a WordPress website via a docker container.

**Create WordPress Directory**

We will create a new directory called worpress in '/srv' file location
~~~
# sudo mkdir -p /srv/wordpress 
~~~
The directory is now created, we will **cd** into the directory and utilize `nano` to create the new file in the **/srv** directory. 
 ~~~
# cd /srv/wordpress 
# sudo nano
~~~
There are two files you could use: **YAML** file or the **config** file. In Docker all resourses used to run a container are defined in the YAML file. Edit the file to contain these parameters. This installation we will be using the 'YAML' file 
~~~
version: '3'
services:
  mysql:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: my_password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: wordpress_password
    volumes:
      - mysql_data:/var/lib/mysql
  wordpress:
    image: wordpress:latest
    depends_on:
      - mysql
    ports:
      - 8080:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: wordpress_password
    volumes:
      - ./wp-content:/var/www/html/wp-content
volumes:
  mysql_data:
~~~
Once you exit the file save it as `docker-compose.yaml`
If you are truggling to put a docker run command into a docker-compose file this link https://www.composerize.com/ will provide an altnernative solution. 

This YAML script targets mysql and worpress. The wordpress ecosystem allows for space becuase WordPress is based in Apache. Therefore, this installation it will use port 80. The next process will be changing the port to 8080 on your local machine. 

**Run WordPress with DockerCompose** 

In the wordpress directory, build the local enviroment
~~~
# sudo docker-compose up -d
~~~
The scrpit will be activated which will pull in Docker images and will create the foler the `/srv/wordpress/wp-content`.

**Use web interface to finish installation**
Using any web browser within your VM, enter `http://localhost.8080` in the search bar and this will prompt you instructions to finish your worpress installation. 

Once set up your interface will the png in the docker github

