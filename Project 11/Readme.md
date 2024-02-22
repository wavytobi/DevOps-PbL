# ANSIBLE CONFIGURATION MANAGEMENT-AUTOMATE

### Ansible client as a jump server (Bastion host)

A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface. On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![alt text](Images/220224181-be4dc5f9-344a-4606-92b2-a52b47e5aca4.png)

### Task 
- Install and configure Ansible client to act as a jump Server/Bastion host
- Create a simple Ansible playbook to automate servers configuration

## INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

![alt text](<Images/Screenshot 2024-02-18 at 2.03.11 PM.png>)

2. In your GitHub account create a new repository and name it ansible-config-mgt.

3. Install Ansible `sudo apt update ``sudo apt install ansible`

![alt text](<Images/Screenshot 2024-02-18 at 2.06.37 PM.png>)

![alt text](<Images/Created new repo ansible configmgt1.png>)

![alt text](Images/Installingansible.png)

4. Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.
   
- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

- Configure Webhook in GitHub and set webhook to trigger ansible build.
  
- Configure a Post-build job to save all (**) files, like you did it in Project 9.

![alt text](<Images/Unlock jenkins.png>)

![alt text](<Images/get jenkins password.png>)

![alt text](<Images/suggested plugins.png>)

![alt text](<Images/configure a post build job to save all .png>)

Test your setup by making some change in README.MD file in main branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

`ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

![alt text](<Images/Test setup 2.png>)

Note: Trigger Jenkins project execution only for /main (master) branch.

Now your setup will look like this:

![alt text](Images/220224757-1e0a1467-bf27-4c28-803a-e6e94a45311b.png)

## Step 2- Prepare your development environment using Visual Studio Code

6. First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC)

7. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

8. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

9. git clone

![alt text](<Images/Screenshot 2024-02-18 at 2.56.03 PM.png>)

## BEGIN ANSIBLE DEVELOPMENT

10. In your ansible-config-mgt Github repository, created a new branch that will be used for development of a new feature.

![alt text](<Images/Create new branch.png>)

![alt text](<Images/Branch created.png>)

11. Checkout the newly created feature branch to your local machine and start building your code and directory structure

12. Create a directory and name it playbooks – it will be used to store all your playbook files.

13. Create a directory and name it inventory – it will be used to keep your hosts organised.

14.Within the playbooks folder, create your first playbook, and name it common.yml

15. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

![alt text](<Images/ansible configg.png>)

## Step 4 – Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video: • For Windows users – ssh-agent on windows • For Linux users – ssh-agent on linux

`eval `ssh-agent -s`
`ssh-add <path-to-private-key>`

Confirm the key has been added with the command below, you should see the name of your key ssh-add -l

Now, ssh into your Jenkins-Ansible server using `ssh-agent ssh -A ubuntu@public-ip`

![alt text](<Images/ssh add -l.png>)

![alt text](<Images/ssh into your jenkins-ansible.png>)

![alt text](<Images/ssh agent mac.png>)

![alt text](<Images/ssh ubuntu@ip.png>)

Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

Update your inventory/dev.yml file with this snippet of code:

![alt text](<Images/update inventory file.png>)

### CREATE A COMMON PLAYBOOK

Step5 -Create a common playbook

It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev. In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:

![alt text](<Images/create common olaybooks.png>)

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

  Feel free to update this playbook with following tasks:

- Create a directory and a file inside it
- Change timezone on all servers
- Run some shell script etc

### Step 6 – Update GIT with the latest code

Now all of your directories and files live on your machine and you need to push changes made locally to GitHub. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle". Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub: 16. use git commands to add, commit and push your branch to GitHub.

`git status`

`git add <selected files>`

`git commit -m "commit message"`

Create a Pull request (PR)

Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

![alt text](<Images/Create pull request.png>)

![alt text](<Images/seconf PR.png>)

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to

`/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.`

![alt text](<Images/jenkins job.png>)

![alt text](<Images/ansible automation.png>)

### RUN FIRST ANSIBLE TEST

Step 7- Run first ansible test

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

`ansible-playbook -i inventory/dev.yml playbooks/common.yml`

![alt text](<Images/Ansible test.png>)

Your updated with Ansible architecture now looks like this:

![alt text](Images/220228156-2d17da01-31f0-42f9-8b42-5286a347f6e0.png)

