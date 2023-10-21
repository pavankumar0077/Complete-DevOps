1) What is CI CD ?
2) Shake out scripts using selenium ?
3) Braching Stetagey ?
4) How many environments in your project ?
5) Difference Git pull and Git fetch ?
6) What is Git slash ?
7) CherryPick in Git ?
8) What the plugins you have used in jenkins ?
9) Remote trigger in jenkins ?
10) CI atomica in jenkins ?
11) Tomcat logs ?
12) Architecture of Linux Virtual Machine ?
13) hostname location in linux -- /etc/hosts
14) nsl or nsf switch file name in linux ?
15) Jail concept in Linux >
16) Create user with some specific location  -- useradd
17) ex2 and ext3
18) How ansible works, architecture
19) Who can do password less authentication in Ansible ?
20) What is the minimum requirement for ansible or pre-requesties of ansible like cpu, python is needed
21) How can you execture ansible file or command
22) if we have multiple inventory files -- what is the precedence of the files ? mention location or path with the file
23) Difference between dockerization and virtualization ?
24) Namespace in docker ?
25) C group in docker ?
26) Difference with 'P' and 'p' in docker ?
27) What are the services using in AWS and why ?
28) How you connect with ec2 instance ?
29) Difference between policies and roles in AWS
30) How to you access S3 from EC2 instances ?
### =====================================================================
1) What is I node in linux file system ?
2) what is Prom QL in promethus ?
3) How to define runnable service in user defined service named my service in linux env ?

### =====================================================================
1) what exactly is a Linux kernel
- a Linux kernel it's mostly like this um itself it's a program I would say uh which basically controls all the hardware and the software and in between that it creates a bridge so Linux kernel is mostly responsible for all the system calls and all the hardware monitoring 
-  And it connects all the all to the um ports and everything so basically Linux kernel interacts between your Hardware input to these software level import operating system it creates a bridge and it communicates within both.

2) Can we edit Linux Kernel
- absolutely like we usually do now Linux kernel replacement in all of our servers like we migrate will sometimes upgrade these servers kernels 

- From 4.5 to 5.2 recently we have upgraded Amazon Linux too so definitely it can be editable but it makes you a binary like the packages are actually in binary so the packages needs to be unassembled before if you want to see this whole source code, it's a open source so anybody can edit and they can create their own kernel

3) what is the role of system library in Linux system
- libraries are like var lib it's basically libraries are the required files to run in the system applications like internal application like system Hardware system on file systems and all the demand processes and all wherever it's all the services are running so system

4) consider a scenario in which you have insufficient Ram in your system and you need more RAM to process your uh application whatever you are running okay yeah so what would be the how will you troubleshoot this ?
- absolutely so first I would need the info how much the minimum and maximum RAM that the application need if 

- Let's say a application needs a only 4 GB of RAM and my system has maximum of 4GB Ram allocated so in that time I would see direct like two options either** provision a server with more than two 4GB Ram** or swaps space add as a temporary Ram so which can basically run the program then you can allocate the disk space to create that run so it might work but the performance won't be that much 

- like a physical run because the physical gram will have a speed up from like in eager aspect uh images basically so either increase in the Subspace or I look at a new Ram program you talk about swap space and swaps change the Heap space heap memory to allocate much more RAM um to run that application in Java Applications we usually allocate this keep space

5) what do you understand by Linux loader ?
- so loaders are basically it's a loader it can be like a Target uh where it loads from the operating system 

- Is scaling uh when you start the OS uh your server it checks for them your targets like your inner targets and your Mount targets everything and it takes for the operating system so the loaders are responsible to load your kernel into the target mounted Target level so there are like six number of Target points where you get it should be loaded like it should be single or a multi boot option or in GUI kind of 

- Thing so loaders are responsible at that time to load into particular level up turn levels

6) On 1st January 2022 okay and you have been using it for two years Yeah and suddenly you are trying to deploy something uh in the Linux system there is an error file system is full what does it mean and how will you Rectify it?
- so our file system get filled uh with all the server files right so we have to clear up some files or uh how do we know that which files are occupying the highest file utilize sometimes most of the time 

- The reasons uh it would be due to the logs um it might not have been rotated uh either it would be filled up because of the high logs as generated on the server and it is Regional CTL files are huge volumes it could be because of that and we need to find out using df -h and see which mounted points are basically filled up it could be xda1 XDA to kind of like 

- That so our mounted points are basically ending XDA volumes so when you put which volumes are filled up and go to that volume check for the highest utilization files and either flush it out or just check for any temporary files or if it could be because of some inodes so it might make a clear up some my nodes you fill up that space after doing that

