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

7) What do you understand by Linux kernel?
- Linux kernel is something you can say, it's the heart of operating systems. It's something you can say whenever you are inputting something on a Linux system, so it is a kernel who interprets between us and like the player, you can say, whatever you are trying to do, get executed. 

8) when we talk about a process and a daemon, what is the difference between both of them?
- Process is something like it's a simple process, like which are, so let's consider some applications are running also. That's a one process, whichever in like whenever any application, there are a few processes which work in a bit calm. So that is something we call as a process. And daemon is something you can say, 
I'm not sure on the exact part, but it's just something you can say, a group of processes which will get loaded while your OS gets started.

9) Have you heard about LVM partition? Yeah, yeah. Okay, so how do you reduce or shrink the size of an LVM partition?
- If you see in X, like original 7 and if we are using XFS file system, so I don't think there will be an option to reduce the LVM. You need to destroy the logical volume groups and then recreate it, but the resize2fs command, it will use for that.

10) so we always have a root user in Linux Press, uh, I think, uh, "E," I think, uh, it's different on your console stand. Uh, we have to go, uh, the, uh, like a line which started with Linux exchange and then Rd dot track. We have to do, and then it will go to the, uh, you can say rescue mode over there. Uh, we have to, uh, like, uh, reset the root passwords.

11)  let's see, uh, I have created a file in Linux. Okay, um, initial file, touch execute.sh.

12) so I have just created the file. Put, uh, and this file starts my Tomcat. I have written everything whenever I'm trying to start it, it's not happening. Uh, what would be wrong over here? So when you say "sh," it should have execute permission. ?
-  First things, so, so first thing we can check, uh, by like LS or LL. You can see, you list down the files and see the permissions. And then, uh, after, if information is completely fine, then, uh, like, we can run the script against. And if it still is not working, then we check, we need to, uh, let's check the logs of prompt right away. It's not getting started.

13) so if I want to give the execute access, uh, what is the number that I give from chmod? So it says we give seven, so it's four for read, two for write, and one for execute. Okay, great, great.

14) what do we understand by grep command and how do we use it? Can you give an example?
- Okay, grep is something you can say if you want to grab some pattern or something. If it is something that matches a pattern or something, then we use it. I can give an example like, let's consider we have a number of processes in our Linux systems, and we have to create the process which has Java. There is one pack, so what we can do is, ps -ef | grep -i then the pattern, whatever pattern you have to search. So it will just list down the processes which have that particular pattern.

15) Let's say I have a server that is running in Linux. Okay, let's say Tomcat server. So I have started it, it's not starting, and not even going down. It's somewhere stuck in the middle. How do you figure out what to do and how will you do it? What would your next step?
- Okay, so in such cases, then our first thing what we can do is, like, we can find out that particular process ID, and then we can kill that process and restart it. Okay, what is the command for killing it forcefully? to kill we use kill -9 Okay, and if I don't want to, I just want to kill it, not forcefully. Do we have any other number? I don't recall it currently, but yeah, we have.

16) what do we understand by inode? Okay, 
so whenever we create file systems, we assign inodes to it. So every file which is stored in that particular file system got unique numbers. Okay,

17) there is something on a shadow password, right? What does it mean and how is it enabled passwords? 
