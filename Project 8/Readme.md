# Automating Loadbalancer configuration with Shell scripting


## Automate the Deployment of Webservers

## Deploying and Configuring the webserver


### Automate the Deployment of Webservers

Automation is in the heart of what DevOps engineer do, Automation helps us speed the deployment of services and reduce the chance of making errors in our day to day activity.


### Deploying and Configuring the Webservers

The process is as folllows,

**Step 1**: Provision an Ec2 instance running on ubuntu

**Step 2**: Open port 8000 to allow traffic from anywhere using the security group.

**Step 3**: Connect to the webserver via terminal using SSH Client.

![Alt text](<Images/EC2 for automating loadbalancer.png>)

**Step 4**: Open a file, and paste Script below and close the file using the command below:
`sudo vi install.sh`

`#!/bin/bash

####################################################################################################################
####### This automates the installation and configuring of apache webserver to listen on port 8000
####### Usage: Call the script and pass in the Public_IP of your EC2 instance as the first argument as shown below:
######## ./install_configure_apache.sh 127.0.0.1
####################################################################################################################

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure

PUBLIC_IP=$1

[ -z "${PUBLIC_IP}" ] && echo "Please pass the public IP of your EC2 instance as an argument to the script" && exit 1

sudo apt update -y &&  sudo apt install apache2 -y

sudo systemctl status apache2

if [[ $? -eq 0 ]]; then
    sudo chmod 777 /etc/apache2/ports.conf
    echo "Listen 8000" >> /etc/apache2/ports.conf
    sudo chmod 777 -R /etc/apache2/

    sudo sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8000>/' /etc/apache2/sites-available/000-default.conf

fi
sudo chmod 777 -R /var/www/
echo "<!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: "${PUBLIC_IP}"</p>
        </body>
        </html>" > /var/www/html/index.html

sudo systemctl restart apache2`


**Step 5**: I changed the permission on the file to make it excutable using the command below:
`sudo chmod +x install.sh`

**Step 6**: Ran the shell script using the command below, after i made sure i read the instructions in the script to learn how to use it.
`./install.sh 107.20.40.251`


### Deploying and Configuring Nginx Load Balancer

Steps to Run the shell script

**Step 1**: On your terminal, Open a file nginx using the command below:
` sudo vi nginx.sh`

**Step 2**: Copy and paste the script inside the file


#!/bin/bash

######################################################################################################################
#####This automates the configuration of Nginx to act as a load balancer
#####Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
#####the webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
#####./configure_nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
#####./configure_nginx_loadbalancer.sh 127.0.0.1 192.2.4.6:8000  192.32.5.8:8000
############################################################################################################# 

PUBLIC_IP=$1
firstWebserver=$2
secondWebserver=$3

[ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

[ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

[ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure


sudo apt update -y && sudo apt install nginx -y
sudo systemctl status nginx

if [[ $? -eq 0 ]]; then
    sudo touch /etc/nginx/conf.d/loadbalancer.conf

    sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
    sudo chmod 777 -R /etc/nginx/

    
    echo " upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server  "${firstWebserver}"; # public IP and port for webserser 1
            server "${secondWebserver}"; # public IP and port for webserver 2

            }

           server {
            listen 80;
            server_name "${PUBLIC_IP}";

            location / {
                proxy_pass http://backend_servers;   
            }
    } " > /etc/nginx/conf.d/loadbalancer.conf
fi

sudo nginx -t

sudo systemctl restart nginx

**Step 3**: Close the file using `esc shift + :wqa!`

**Step 4**: I changed the file permission to make it  executable using the command below:
`sudo chmod +x nginx.sh`

**Step 5**:Run the script with the command below:
./nginx.sh 34.228.77.37 107.20.40.251:8000 54.226.36.233:8000

Webserver 1

![Alt text](Images/webserver1.png)

Webserver 2

![Alt text](<Images/webserver 2.png>)

Load Balancer

![Alt text](<Images/Load balancer .png>)