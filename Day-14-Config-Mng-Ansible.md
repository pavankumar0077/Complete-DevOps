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
