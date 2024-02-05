# WEB SOLUTION WITH WORDPRESS (PROJECT-9) ##

In this project we will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. 

WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

Project 6 consists of two parts:

* Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

* Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.
  
__Three-tier Architecture__

Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

![image](./images/3-tier.PNG)

* __Presentation Layer (PL):__ This is the user interface such as the client server or browser on your laptop.
* __Business Layer (BL):__ This is the backend program that implements business logic. Application or Webserver.
* __Data Access or Management Layer (DAL):__ This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server.

In this project, we will showcase Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as _gdisk_ and _LVM_ respectively.

__Our 3-Tier Setup__
1. A Laptop or PC to serve as a client.
1. An EC2 Linux Server as a web server (This is where you will install WordPress).
1. An EC2 Linux server as a database (DB) server.

We will use __RedHat OS(centos)__ for this project.

__LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.__

After logging into our Aws console, we go to __EC2__ and click on _"volume"_ under __Elastic block store(EBS)__.

Learn How to Add EBS Volume to an EC2 instance [here](https://www.youtube.com/watch?v=HPXnXkBzIHw)

Click on __"create volume"__

![image](./images/create-ebs1.PNG)

then  change size to __10GiB__ and click on __create volume__ afterwards.

![image](./images/create-ebs2.PNG)

We do the same process to create three EBS volumes.

![image](./images/create-ebs3.PNG)

Next, we create an EC2 instance that will serve as __web server__.

We set this up by doing the following:

* create an account on [AWS](https://aws.amazon.com/). 
* we create an EC2 instance by selecting __“REDHAT ENTERPRISE LINUX 9”__ from Amazon Machine Image(AMI)(free tier). We will select a Redhat AMI since we are using a centos and not ubuntu. 
* we select “t2.micro(free tier eligible)”.
* Select our keypair.
* then go to the security group and select “a security group” review and launch.

 How to create an aws free tier account. click [here](https://www.youtube.com/watch?v=xxKuB9kJoYM&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=7)

This launches us into the instance as shown in the screenshot:

![image](./images/create-instance.PNG)

we then attach the EBS volumes we created to the webserver Ec2 instance.

![image](./images/attach-ebs.PNG)

then edit the __"instance info"__ with the __"instance id"__ and __"device name info"__ to __"/dev/xvdf", "/dev/xvdg" and /dev/xvdh"__ for the three volumes.

![image](images/attach-ebs2.PNG)

then click on attach. do this for the three Ebs volumes created.

Open the linux terminal, connect to the instance.

![image](./images/connect-redhat-instance.PNG)

Open the __'/etc/hostname'__

```$ vi /etc/hostname```

replace the __"IP address"__ with __"webserver"__.

click on __"ESC :wq + ENTER__

Then run the command 

```$ hostname webserver```

Then disconnect and connect for changes to take effect.
This process is used to identify the terminal as web server so as to differentiate it from the database server.

Use ```lsblk``` command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

![image](./images/lsblk.PNG)

Use ```df -h``` command to see all mounts and free space on your server.

Use __gdisk__ utility to create a single partition on each of the 3 disks

```$ sudo gdisk /dev/xvdf```

type __"?"__ to display the available options. Then __"p"__ _"which represents print the partition table"_.

![image](./images/partition.PNG)

From the options displayed, __"n"__ represents _"add a new partition"_.

Type __"n"__
 then __"p"__ to display the new partiton table.

![image](./images/partition1.PNG)

Type __"w"__ to write table to disk 

![image](./images/partition2.PNG)

Repeat the process for the the three disks i.e /dev/xvdf, /dev/xvdg. /dev/xvdh.


Use ```lsblk``` utility to view the newly configured partition on each of the 3 disks.

![image](./images/lsblk-after-partition.PNG)

Install ```lvm2``` package using 

```$ sudo yum install lvm2```

![image](./images/lvm2-install.PNG)

Run 

```$ sudo lvmdiskscan```  to check for available partitions.

![image](./images/lvmdiskscan.PNG)

Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.

```$ sudo pvcreate /dev/xvdf1```

```$ sudo pvcreate /dev/xvdg1```

```$ sudo pvcreate /dev/xvdh1```

![image](./images/pvcreate.PNG)

Verify that your Physical volume has been created successfully by running 

```$ sudo pvs```

![image](./images/sudo-pvs.PNG)

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG __webdata-vg__

```$ sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1```

Verify that your VG has been created successfully by running 

```$ sudo vgs```

![image](./images/vgcreate-sudo-vgs.PNG)

Use lvcreate utility to create 2 logical volumes. __apps-lv (Use half of the PV size)__, and __logs-lv (Use the remaining space of the PV size)__.

The __apps-lv__ will be used to store data for the Website while, __logs-lv__ will be used to store data for logs.

```$ sudo lvcreate -n apps-lv -L 14G webdata-vg```

```$ sudo lvcreate -n logs-lv -L 14G webdata-vg```

![image](./images/lvcreate.PNG)

Verify that your Logical Volume has been created successfully by running 

```$ sudo lvs```

![image](./images/sudo-lvs.PNG)

Verify the entire setup

```$ sudo vgdisplay -v #view complete setup - VG, PV, and LV```

```$ sudo lsblk```

![image](./images/verify-setup1.PNG)
![image](./images/verify-setup2.PNG)

Use __"mkfs.ext4"__ to format the logical volumes with __"ext4"__ filesystem

```$ sudo mkfs -t ext4 /dev/webdata-vg/apps-lv```

```$ sudo mkfs -t ext4 /dev/webdata-vg/logs-lv```

![image](./images/mkfs-ext4.PNG)

Create __"/var/www/html"__ directory to store website files

```$ sudo mkdir -p /var/www/html```

Create /home/recovery/logs to store backup of log data

```$ sudo mkdir -p /home/recovery/logs```

Mount __"/var/www/html"__ on __"apps-lv"__ logical volume

```$ sudo mount /dev/webdata-vg/apps-lv /var/www/html/```

Use rsync utility to backup all the files in the log directory __"/var/log"__ into __"/home/recovery/logs"__ (This is required before mounting the file system).

```$ sudo rsync -av /var/log/. /home/recovery/logs/```

![image](./images/mkd.PNG)

Mount __"/var/log"__ on __"logs-lv"__ logical volume. (all the existing data on __"/var/log"__ will be deleted.

```$ sudo mount /dev/webdata-vg/logs-lv /var/log```

Restore log files back into __"/var/log"__ directory.

```$ sudo rsync -av /home/recovery/logs/. /var/log```

![image](./images/mount-rsync.PNG)

Update __"/etc/fstab file"__ so that the mount configuration will persist after restart of the server.

__UPDATE THE _"/ETC/FSTAB"_ FILE__.

The UUID of the device will be used to update the __/etc/fstab__ file;

```$ sudo blkid```

![image](./images/sudo-blkid.PNG)

Open the __"/etc/fstab"__

```$ sudo vi /etc/fstab```

Update __/etc/fstab__ in this format using your own UUID and rememeber to remove the leading and ending quotes.

![image](./images/etc-fstab.PNG)

Test the configuration by running this command. There will be no errors if everything is ok.

```$ sudo mount -a```

Reload the __daemon__

```$ sudo systemctl daemon-reload```


Verify your setup by running 

```df -h```

![image](./images/df-h.PNG)

__PREPARE THE DATABASE SERVER__.

Launch a second RedHat EC2 instance that will have a role – __‘DB Server’__
Repeat the same steps as for the Web Server, but instead of __apps-lv__ create __db-lv__ and mount it to __/db__ directory instead of __/var/www/html/__.

The __apps-lv__ will be used to store data for the Website while, __logs-lv__ will be used to store data for logs.

```$ sudo lvcreate -n db-lv -L 14G webdata-vg```

```$ sudo lvcreate -n logs-lv -L 14G webdata-vg```

Verify that your Logical Volume has been created successfully by running 

```$ sudo lvs```

![image](./images/db-lvcreate-sudo-lvs.PNG)

Verify the entire setup

```$ sudo vgdisplay -v #view complete setup - VG, PV, and LV```

![image](./images/db-verify-disk1.PNG)

```$ sudo lsblk```

![image](./images/db-verify-disk2.PNG)

Use __mkfs.ext4__ to format the logical volumes with __ext4__ filesystem.

```$ sudo mkfs -t ext4 /dev/webdata-vg/db-lv```

```$ sudo mkfs -t ext4 /dev/webdata-vg/logs-lv```

![image](./images/db-mkfs.PNG)

Create __db__ directory to store database files

```$ sudo mkdir db```

Create /home/recovery/logs to store backup of log data

```$ sudo mkdir -p /home/recovery/logs```

Mount __db/__ on __db-lv__ logical volume

```$ sudo mount /dev/webdata-vg/db-lv db/```

Use rsync utility to backup all the files in the log directory __/var/log__ into __/home/recovery/logs__ (This is required before mounting the file system).

```$ sudo rsync -av /var/log/. /home/recovery/logs/```

Mount __/var/log__ on __logs-lv__ logical volume. (all the existing data on /var/log will be deleted.)

```$ sudo mount /dev/webdata-vg/logs-lv /var/log```

Restore log files back into __/var/log__ directory

```$ sudo rsync -av /home/recovery/logs/. /var/log```

![image](./images/db-mount-rsync.PNG)

The UUID of the device will be used to update the __/etc/fstab__ file;

```$ sudo blkid```

![image](./images/db-sudo-blkid.PNG)


Open the __"/etc/fstab"__ file.

```$ sudo vi /etc/fstab```

Update __"/etc/fstab"__ in this format using your own UUID and rememeber to remove the leading and ending quotes.

![image](./images/db-etc-fstab.PNG)

Test the configuration for errors.

```$ sudo mount -a```

Reload __daemon__

```$ sudo systemctl daemon-reload```

Verify your setup by running 

```$ df -h```

![image](./images/db-df-h.PNG)

__Install WordPress on your Web Server EC2__

Update the repository

```$ sudo yum -y update```

![image](./images/yum-update.PNG)

Install wget, Apache and it’s dependencies

```$ sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json```

![image](./images/install-http-wget.PNG)

Start Apache

```$ sudo systemctl start httpd```

Enable Apache

```$ sudo systemctl enable httpd```

Verify Apache status

```$ sudo systemctl status httpd```

![image](./images/start-httpd.PNG)

To install PHP and it’s depemdencies

```$ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm```

```$ sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm```

```$ sudo yum module list php```

```$ sudo yum module reset php```

```$ sudo yum module enable php:remi-7.4```

```$ sudo yum install php php-opcache php-gd php-curl php-mysqlnd```


![image](./images/install-php1.PNG)
![image](./images/install-php2.PNG)
![image](./images/install-php3.PNG)
![image](./images/install-php4.PNG)
![image](./images/install-php5.PNG)

Start __php__ and set policies

```$ sudo systemctl start php-fpm```

Enable __php__

```$ sudo systemctl enable php-fpm```

verify __php__ status

```$ sudo systemctl status php-fpm```

```$ sudo setsebool -P httpd_execmem 1```

Restart Apache

```$ sudo systemctl restart httpd```

![image](./images/start-php-httpd.PNG)

Copy the IP address of the webserver to the browser to see that the apache is working properly

![image](./images/httpd-page.PNG)

Download wordpress and copy wordpress to __"var/www/html"__

Create directory __wordpress__ and _cd_ into the directory.

```$ mkdir wordpress```

```$ cd   wordpress```

Download the wordpress file

```$ sudo wget http://wordpress.org/latest.tar.gz```

Unzip the file

```$ sudo tar xzvf latest.tar.gz```

![image](./images/wordpress-unzip.PNG)

```$sudo rm -rf latest.tar.gz```


Copy __"wordpress/wp-config-sample.php"__ into __"wordpress/wp-config.php"__

__N/B__: __wordpress/wp-config.php"__ will be created.

```$ sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php```
  
Copy __wordpress__ into __"/var/www/html"__.

![image](./images/rm.PNG)

```$ sudo cp -R wordpress/* /var/www/html/```

![image](./images/cp-R-wordpress-var-www.PNG)

Configure SELinux Policies

```$ sudo chown -R apache:apache /var/www/html/```

```$ sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R```

```$ sudo setsebool -P httpd_can_network_connect=1```

 ```$ sudo setsebool -P httpd_can_network_connect_db 1```

![image](./images/selinux-config.PNG)

__Install MySQL on your DB Server EC2__

Update the repository

```$ sudo yum update -y```

![image](./images/db-yum-update.PNG)

Install __mysql-server__

```$ sudo yum install mysql-server```

![image](./images/db-install-mysql-server.PNG)

Verify that the service is up and running, if it is not running, restart the service and enable it so it will be running even after reboot:

```$ sudo systemctl restart mysqld```

```$ sudo systemctl enable mysqld```

```$ sudo systemctl status mysqld```

![image](./images/db-mysql-server-start.PNG)

__Configure DB to work with WordPress__

```$ sudo mysql```

```mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';```

```mysql> exit;```

```$ sudo mysql_secure_installation```

![image](./images/mysql-config1.PNG)
![image](./images/mysql-config2.PNG)

```$ sudo mysql -p```

```mysql> CREATE DATABASE `wordpress`;```

```mysql> CREATE USER 'myuser'@'%' IDENTIFIED WITH mysql_native_password BY 'S**la@@zaa';```

```mysql> GRANT ALL ON example_database.* TO 'myuser'@'%';```

```mysql> exit;```

```mysql -u example_user -p```

```mysql> SHOW DATABASES;```

![image](./images/mysql-config.PNG)

Open __MySQL port 3306 on DB Server EC2__. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as __/32__

![image](./images/mysql-inbound-on-db.PNG)

Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client.

```$ sudo yum install mysql```

![image](./images/web-install-mysql.PNG)

Connecting __webserver__ to __DBserver__ and Verify if you can successfully execute ```SHOW DATABASES;``` command and see a list of existing databases.

```$ sudo mysql -u myuser -p -h <DB-Server-Private-IP-address>```

![image](./images/connect-from-web-to-db.PNG)

Change permissions on the __"/var/www/html"__ directory so that apache can use wordpress.

![image](./images/apache-ownership-var-www-html.PNG)

Edit the __"/var/www/html/wp-config.php"__ to connect to dataabase.

![image](./images/wp-config-php.PNG)

Disable the apache configuration(i.e renaming to backup)

```$ mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup```

Open __"/etc/my.cnf"__ and edit the file. Add [mysqld] and __"bind-address=0.0.0.0"__.

```$ sudo vi /etc/my.cnf```

![image](./images/my-cnf.PNG)

__N/B:__ In setting up our mysql configuration, we put the access as __"%"__ whch shows we want to connect from anywhere. If we have set a particular IP address of webserver we will put in the same IP address in the __bind-address__.

Enable TCP __port 80__ in Inbound Rules configuration for your Web Server EC2 (enable from everywhere __0.0.0.0/0__ or from your workstation’s IP)

![image](./images/web-inbound-anywhere.PNG)

Try to access from your browser the link to your WordPress

```http://<Web-Server-Public-IP-Address>/wordpress/```
![Alt text](<Images/welcome to wordpress 0.png>)

![Alt text](<Images/welcome to wordpress 2.png>)

[Title](Images/wordpress-browser.PNG.webloc)

[Title](Images/wordpress-browser.PNG.webloc)

![Alt text](<Images/Wordpress 4.png>)


Congratulations

