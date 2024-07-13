## ANSIBLE DYNAMIC ASSIGNMENT

This project is aimed at achieving configuration with the include module, its a continuation of project 11 and 12. In project 12 we explored static assignment where we used the export module to ahieve the configuration but in this dynamic assignment include module is used which is the major difference between static and dynamic assignments.

`import =static assignment
`
`include =dynamic asignment`

in export (static assignments) when executing site.yml,playbook, ansible will process all the playbooks refereced during the time it is parsing the statements. what this means is that during the actual execution, if any statement changes, such statements will not be considered, hence its static

while include (dynamic assignments) all statemets are processed only during execution of thr playbook. that is any changes tothe statements encountered during execution will be used.

**NOTE:**

Static assigments are more reliable and stable than dynamic assignments hence they are most recommended. however dynamic assignments can be used for enviroment specific variables.

### Step one: I created a dynamic assignment branch and new folder called dynamic assignment and a file called env-vars.yml

![alt text](images/1.png)

![alt text](images/2.png)

updaye site.yml

![alt text](images/3.png)

used the Ansible galaxy community to install the roles necessary, mysql etc after editing the roles changes where committied to github acct and branch merged to the main and pull to the production server or enviroment and the playbook was ran inventory/dev.yml aganst playbook/site.yml

![alt text](images/4.png)

![alt text](images/5.png)

![alt text](images/6.png)

![alt text](images/7.png)

![alt text](images/8.png)

![alt text](images/9.png)

![alt text](images/10.png)

![alt text](<images/11 (1).png>)

![alt text](images/12.png)

![alt text](images/13.png)

![alt text](images/14.png)

### Setting nginx and apache loadbalancers

On defaults/main new configuration setting made and on static assignments folrders created file for apache and nginx and then ran the play book to install and configure nginx as a load balancer and repeat the same for apache

![alt text](images/17.png)

![alt text](images/18.png)

![alt text](images/19.png)