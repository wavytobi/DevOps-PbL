# Implementing Loadblancers with Nginx.

 ## Introduction to load Balancing and Nginx.

 **Load balancing** means distributing the work or tasks amongst several computers or servers so that not one computer gets overloaded with too much task. It keeps the operation running smoothly and ensures that websites and apps work quickly and don't get too slow. It's like teamwork for the computers.

 **Nginx** is a versatile software, it can serve as a web server, reverse proxy, load balancer.


 ## Setting Up a Basic Load Balancer

 We are going to be provisioning two Ec2 instances running on ubuntu, and install Apache and Nginx in them, we will open port 8000 to allow traffic from anywhere,finally update the default page of the webserver  to display their public IP address. 

 Next I will provision another EC2 Instance still on ubuntu, this time I will install Nginx and configure it to act as a load balancer disturbing traffic across the webservers. 

1. Step 1: Create 2 Ec2 instance for the Apache webserver and Nginx Load Balancer.

2. Step 2: Opened port 8000 for Apache webserver, For the load balancing port 80 
   
   ![Alt text](<Images/Apache Web server and Load balancer.png>)

   ![Alt text](<Images/Edit port 8000.png>)

3. Step 3: Its time to install Apache webserver, but before that I need to connect to the webserver 
   `sudo apt update -y &&  sudo apt install apache2 -y`

  Verify that apache is running by using the command;
  `sudo systemctl status apache2`

4. step 4: Configure Apache to serve a page showing its public IP:

   I will start by configuring Apache webserver to serve content on `port 8000` instead of its default which is `port 80`.
   then i will create a new `index.html`

- **Configuring Apache to serve content on port 8000:**
   
   1. Using Vi editor, Opened /etc//apache2/ports.conf

   2. Added a new listen directive for port 8000, then save your file with ESC and :wq.

   3. Next opened the file /etc/apache2/sites-available/0000-default.conf and change the port 80 on the virtual host to 8000.

   ![Alt text](<Images/Apache webser  port 8000 conf1.png>)

   ![Alt text](<Images/Apache webserver  virtual host port 8000 .png>)

   4. Restart Apache to load the new configuration with the command;
     `sudo systemctl restart apache2`

- **Creating the new index.html file:**

   1. Opened the the new index.html
   `sudo vi index.html`

   2. Before I pasted the html file, I used the public IP from the aws EC2 Instance webserver.
           <!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: YOUR_PUBLIC_IP</p>
        </body>
        </html>

  3. Change file ownership of the index.html file withe the command;
     `sudo chown www-data:www-data ./index.html`

- **Overriding the Default html file of Apache Webserver;**

  1. I replaced the default html file with our new html using the command
     `sudo cp -f ./index.html /var/www/html/index.html`

  2. Restart the webserver to load the new configuration using the commmand
     `sudo systemctl restart apache2`

  3. A page like this should be displayed.

![Alt text](<Images/Apache webserver Ec2 instances.png>)












