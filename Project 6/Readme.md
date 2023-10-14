# Understanding Client Server Architecture & Implementing a Client server Architecture using MySQL(DBMS)


 ## For understanding client server Architecture, we are going an example;

 To see a client server communication we are going to use a `curl` command.
`curl -Iv www.propitixhomes.com`

In this example the terminal will be the client while `www.propitixhomes.com` will be server.
see the the response from the remote server in the below output.

![Alt text](<Images/understanding client server architecture.png>)


## Implementing a Client server Architecture using MySQL(DBMS)

1. Create and configure two linuxs based virtual servers (EC2 instances in AWS)
 - Server A name - `mysql server`
 - Server B name - `mysql client`

   ![Alt text](<Images/Create 2 Ec2.png>)

2. On `mysql server` linux server install MYSQL server software.

3. On `mysql client` linux server instal MYSQL client software.

4. MySql uses TCP 3306 by default, so i add to edit the inbound rules in 'mysql server' security groups.

![Alt text](<Images/MySQL port 3306.png>)

5. I need to configure my MYSQL server to allow remote access from host.
`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf `

Replace 127.0.0.1 to 0.0.0.0

![Alt text](<Images/Ediit Bind -address.png>)

6. From `mysql client` linux server connect remotely to `mysql server` Database Engine without using `SSH` You must use the `mysql` utility to perform this action.

7.Check that you have successfully connected to a remote MYSQL serve and can perform SQL queries;
Show databases;

The image below shows that have deployes a fully dunctional MySQL-Server set up.

![Alt text](<Images/Implementation of client server architecture.png>)





