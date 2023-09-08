![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/12df894b-683f-4de1-b080-9b68d22d1a13)# CONFIGURATION MANAGEMENT

How configuration is managment on servers(Infrastrucuture) 
1) Upgrade
2) Secure patches
3) Installations

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/bb7091c5-ded5-4869-9d6c-9da364b5e2ee)


1) Before devops or ansible we have sysadmin do some imp stuff for us like above 3 points.
Let say for example you have 100's of server with different OS. Then logging into all the instance and making installation is
not that easy.

2) They used write some scripts like shell script, power shell, different OS have different servers. still installation is very
hard to handle.

# NOTE: The above points are on perm servers.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/bf177a38-c449-431e-a5b3-089be1a5d5cf)

**Now we will see that servers moved to CLOUD.
Still problem is same and even become harder.**

# TO ADDRESS THIS PROBLEM, CONFIGURATION MANAGMENT CONCEPT COME TO SOLVE THIS ISSUE
Tools for configuration management
--
1) puppet
2) chef
3) Ansible
4) Salt

# ANSIBLE IS BETTER THAN OTHER TOOLS
ANSIBLE IS PART OF REDHAT
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4b4bb969-78a4-47c6-baeb-182727f3c5dc)

WHY ANSIBLE ? BETTER THAN OTHERS
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1fb6c83b-752c-4d64-ab89-294d0004ef3a)

PUPPET (p)
ANISBLE (a) 
1) Installing pkgs -- Devoops engieer using (a) write this ansible playbooks and the configurations to 10 EC2 instance **USING PUSH MODEL**
2) (a) Useless AGENTNESS MODEL. Just give names of the server on inventory file. and password less auth enable
3) Dynamic inventory -- ansible will update dynamically the inventory.
4) IF any new server is created then ansible will configure it llike dynamically
5) Ansible is easy with windows and linux very good modules to use windows server, linux server.
6) Ansible is very simple, only YAML manifest, puppet uses puppet language.
7) We can write our own anisble modules using PYTHON SHARE TO ANSIBLE GALAXY.
8) ANSIBLE GALAXY TO SHARE THE ANSIBLE MODULES

Problem with anisble
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a97c39c4-2668-4369-8572-4edf55e6b74f)

1) windows server to mangement with anisble it is not that easy
2) Debugging has not do good with ansible
3) Performance -- Issues with parrallel executing, 10k of server at a time may get issues

Interview questions
--
1) What is the programming language ANSIBLE used ?
A) PYTHON is the programming language that ANSIBLE used, you are write your own ansible modules. We generally uses YAML for the config-mag

2) DOes Ansible supports linux & windows or only linux
A) It supports both linux and windows, For LINUX it uses protocal called SSH, FOR Windows it uses protocal called (Win RM)

3) Difference btw Puppet/Chef or Why you choosen Ansible over other tools ?
A) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a97c39c4-2668-4369-8572-4edf55e6b74f)

4) Is ansible pull or push machesism
A) Ansible is push machesiam --

5) What programming language uses ansible
A) YAML manifest is used to write ansible playbooks

6) Does ansible supports all the cloud providers are not
A) For ANSIBLE it does not matter which cloud providers. only matters our public ip, ssh enabled, allow to access to host

WE HAVE 2 SERVERS, ONE IS ANSIBLE-SERVER and other is normal ubunut-server
1) First we need to get password auth from ubunut to anisble server. We can do ssh-copy-id <ip>,But there are other ways to do in simple way..
2) Now, go to ubunut and ```ssh-key``` to get publi and private keys
3) Private key is used to login into the using machine, Never share with anybody. ONly share public key
4) Now copy the public key of ubunut-server and then go to  ansible-server do create ```ssh-keygen`` there as well.
5) Now the copied ubunut-server public should be pasted in the ansible-server -- authorized key. (present in ssh dir). Remove the key present and paste the copied key.
6) ssh the ubunut-server from ansible server
7) any ansible files are the ansible playbook
8) we can also run ansible commands for simple tasks are called ansible adhoc commands
9) ```ansible -i inventory all -m "shell" -a "touch demo1"``` Run this command in the ansible server or in place of all used specific IP if it only one instance. And in the ubunutu-server file will be created with name demo1
10) For performing only 1 or 2 commands then use ansible adhoc commands instead of playbook

11) Difference between ansible adhoc commands vs ansible playbook
A) adhoc commands are only for 1 or 2 task, and playbook is for multiple commands

12) ```ansible -i inventory all -m "shell" -a "touch demo1"``` Here -m standards for modules and -a standards for what is the command that you want to execute.
13)  ```ansible -i inventory all -m "shell" -a "nproc"``` or  ```ansible -i inventory all -m "shell" -a "df Like this we can use all shell commands, We also have other modules too like copy, etc.
14) ``` https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html ```, ```https://docs.ansible.com/ansible/2.9/modules/shell_module.html#shell-module```,``` https://docs.ansible.com/ansible/2.9/modules/vsphere_copy_module.html#vsphere-copy-module ``` Check docs and example and use it when required.
15) For example we have a requirement that do this for one team(DB), So in the inventory file we have to mention the ips
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/54d209d0-9964-4c81-9806-61f2441d141f)
and when we want to work on it use the names like webserver, dbserver like ``` ansible -i inventory webservers -m "shell" -a "df"```

16) What you group the server in ansible or how you execute certain no.of tasks for certain server in ansible
A) In ansible everthing is configured in inventory file, all the servers name ans server details like ips in inventory file. We can do grouping of the servers in inventory file.And we have tell ansible to execute adhoc commands or playbooks on this type of servers like webservers or dbservers

How to write ansible playbook
--
1) **Senario is we want to install Nginx, and start nginx**
2) nano fist-playbook.yaml file --> We can write one playbook or multiple playbooks in a file.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0d2b2b43-b641-4302-8cfc-262b04335ea8)

```
--- indicates yaml file
hosts -- single ip or all ips present in inventory file
become is used to which type of user we have use, Like most of the case sudo should be used to install any pkg, So we use root. we can also use become_user and use any user
tasks:
  - name: Install nginx
    shell: apt install ngi
    apt: (Provided by ansible)
      name: nginx
      state: present
   - name: Strat nginx
     shell: systemctl start niginx
      or
      service:
          name: nginx
          state: started
```
3) To run the playbook   ```ansible-playbook -i inventory first-playbook.yaml```
4) Here if we don't mention the inventory file it will take the default one present in /etc/ansible/inventory. Best practice is place the inventory file where you are running the commands

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/178fba09-c0df-4a52-b20a-0785f4318c4f)

5) If we want to undertand when we execute ansible what is happend then use ```ansible-playbook vvv -i inventory first-playbook.yaml```
Simple vvv more v's more info, it is verbosity like debug

SENARIO 2
--
1) **Senario 2, Creaet 3 ec2 instance on AWS, ``` For this we use Terraform ```
and configure 1 of those EC2 instances as master ``` (ANSIBLE)```
and confiture 2 other ec2 instance as worker.** ``` (ANSIBLE) ```

2) Here we can also create instances using ANSIBLE, but Terraform is specific tool available to so that. Any INFRA can be created using TERRAFORM.

ANSIBLE ROLES
--
1) If we want to achieve the above task, then we have kubernetes control panle, data plane and to start and etc the playbook get a lot of lines and even we have variable etc. config files, erros, varibales and etc, we need to handle all these things.
2) SO AVOID THIS ANSIBLE COME UP WITH **ANSIBLE ROLES**.
3) **ANSIBLE ROLES** are the effecient way of writing ansible playbooks that will only improve our effeciency to write complex playbooks

How to use ROLES
--
1) ``` ansible-galaxy role init kubernetes ```  it will create a kubernetes folder and inside that we have all the required folders.
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/99f6e7f0-396f-44bd-8d50-3bf57ef77969)
3) Using this files and folders we can structure the playbook.
4) ``` https://github.com/ansible/ansible-examples ``` refer github repo
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b3c9edcd-b76c-4b69-9686-8eddbd99bb51)
6) meta folder is used to details fo the entrire playbook, license info
7) default -- to store from variables
8) tests -- same like unit tests
9) handlers -- to hand exceptions, like mail notification and etc
10) files -- To store some files like index.html, and etc to from here to another machine
11) template -- basic templating. jinja 2 


