![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/461fa275-a8af-44cc-b38c-2bfdbec22bef)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/71ede497-e494-484d-9af4-c5affd24d5c5)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/88de957d-4b46-4a41-8d4a-de3cb239397c)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e4f4542a-1385-48fa-907f-7eba2104c6b9)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/39cbf548-eb00-4a1b-b536-3983b125f8c2)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/478c45a7-502e-4e96-8ab3-287dd51d3e5a)

## ===================== LINUX ====================== 

1) Which files stores the user min UID, max UID, password expiration settings, password
encryption methods methodsing used etc.?
```
    cat /etc/passwd -- to check usernames
    cat /etc/login.defs   -- user info, password and etc
```
2) How do you make a file copied to a new user account automatically upon user account
creation? (when new user is created how all files will be created)
```
    cd /etc/skel/
```
3) Lis the fileds in /etc/passwd ?
```
    cat /etc/passwd
```
```
    username  password-encryption  user-id  group-id command home-path user-shell
```
4) How to lock user account ?
```
    usermod -L <user-name>

    passwd -S <user-name>
```
5) Sometimes we forgive password or our account is locked due to incurrect password, How to unlock it ?
```
    usermod -U <user-name>
```
6) How to disable user login via terminals ?
```
    cat /etc/passwd

    check for shell -- /bin/bash

    chnage the shell to sbin - no login

    usermod -s /sbin/nologin <user-name>

    cat /etc/passwd

    if user tries to login using any terminal we get error
```
7) Whenever a user tries to login via terminal, system would throw up the error "The account is currently not available", otherwise,
via GUI when user enters password, it looks to be logging in, however, comes back to the login prompt. How could this issue be fixed?
```
    Error because : if the user is set to /sbin/fale -- in /etc/passwd

    chnage the shell to /bin/bash to work
```
8) How do you make a new user to reset his password upon his first login ?
```
    chage -l <user-name>

    chage -d 0 <tech-name>
```
9) Create users home directory in /home1 directory instead of default /home directory. This gets 
applicabie to any new users who gets created i.e the home directory of that user should be
/homed/‹UserName>
```
    cat /etc/default/useradd

    set home path

    if you want to change for particular to one user

    useradd -d /home100/<user-name> <user-name>
```
10) How to check if a user account has been locked ?
```
    passwd -S <user-name>

    cat /etc/shadow
```
11) How to find out the shadow password encryption method being used in Linux? How co
be changed (example: trom md5 to sha512)?
```
    grep -i crypt /etc/login/defs
```
12) What are the possible causes when a user failed to login into a Linux system (physical/remote
carsolch despite rowling proper credentials?
```
    password locked
    shell disable
```
13) Which command to be used to check the shell being used ?
```
    echo $0
    echo #SHELL
```
14) hOW TO CHEck the last command executed successfully or not ?
```
    echo $?
    it should be 0
```
15) Where are the log files stored usually in Linux ?
```
    cd /var/log
```
16) How to check if the syslog service is running ?
```
    any service is running how to check ...

    systemctl status rsyslog.service
    systemctl status jenkins

    in old systems systemctl will not work
    /etc/init.d/httpd status
```
17) By default, log files are set to get rotated on weekly basis, how to make this gets rotated on
monthly basis?
```
    cat /etc/logrotate.conf

    change weekly to monthly, size and rotate 1 is like how many backup files we should have

    logrotate -f /etc/logrotate.conf  -- to reflect changes
```
18) How do you check the boot message (Kernel ring buffer) ?
```
    dmesg

    dmesg | grep ens33

    cat /var/log/dmesg
```
19) What does /var/log/wtmp and /var/log/btmp files indicates and what do they store?
```
    User login, logout, bad attempts and etc stored in these files

    /log/wtmp -- successfully login of user

    last -- command

    /log/btmp -- bad attempst

    lastb

    cat secure -- user logs and etc
```
20) what does 'ivh' represents in rpm -vh <pkg-name> command ?
```
    rpm -ivh pkgname
    i install
    v - verbose
    h - hashmode -- printing
```
21)  How to find to which package the "Is" commands belongs to (to find out package responsible for this command)?
```
    Error - command not found

    coreutils pkg

    rpm -qf /bin/ls
```
22) How to find out the configuration files installed by a package (take into consideration of the 
"coreutils" package)?+++62
```
    rpm -qc coretils
    rpm -d coretils
```
23) How do you find out all the pakcages installed on a RHEL system(server)?
```
    cat /root/install/log

    rpm -qa
```
24) How to create a local yum repository which would make use of the mounted linux ISO image under /media?

25) Different ways that can be used to verify that a package got installed successfully via yum:
```
    echo $?

    rpm -qa | grep <pkg-name>
```
26) How to view the installed date of a package (consider the package sg3_utils)?
```
    rpm -qi coreutils<pkg-name>
    rpm -qi coreutils | grep less
```
27) How to check what files sytems are mounted and their read/write status ?
```
    cat /etc/fstab

    read/write status
    cat /etc/mtab
```
28) Where is grub.conf/grunb.cfg files stored in RHEL system?
```
    cd /boot/grub2/
```
29) How do you remount a file system read only on the fly(online) ?
```
    df -Th

    cat /etc/mtab | grep /boot

    now change to read only

    mount -o remount,ro /boot/
```
30) Commmand used to convert ext2 file system to ext3
```
    df -Th

    tune2fs -j /boot/
```
31) Find out list of acutal devices associated with a logical volume using LVS command
```
    lsblk

    lvs -o +segtype,devices
```
32)  How to set "rw" permissions on file for a user and disable for other users except root us
(exclusive permissions)?
```
    USE ACL - access control list

    su - 9Hiils <user-name> to swtich

    created a file text.txt

    Give permission to another user to access the file text.txt where is there in other user

    setfacl -m u:tech:rw /home/9Hiils/text/.txt --- Here tech is user-name

    getfactl /home/9Hiils/text.txt -- To check permissions to who you have given access
```
33) Different fields in /etc/fstab
```
    cat /etc/fstab

    device-name mount-point filesytem-type  dump-check sytem-check
```
34) How do you skip the initial fsck(file system check) on a file system while booting up?
```
    cat /etc/fstab

    change 0 0
```
35)  How to list all the files with SUID (Set User ID) bit set under the top level root directory and
ignore any errors/warnings in the process, and list the output in long list format?
```
    find / -type f -perm -4000 2>/dev/null | xargs ls -l ---- xargs are for long list
```
36) How to list all the files/folders with SUID/SGID/Sticky Bit (Set Group ID) bit set under the kop-
level root directory and ignore any errors/warnings in the process, and list the output in long list
format?
```
    find / -type f -perm -7000 2>/dev/null | xargs ls -l ---- xargs are for long list
````
37) What are the differences between hard & soft links in Linux file system ?

38) I've installed the latest kernel on the system successfully, however, my server still bentsareen 0)
the old kernel. How do you make the system to boot from the newly installed kernel?
```
    cat /boot/grub2/grub.cfg

    default --- kernel -- we need to mention the kernel name
```
39) hOW TO find out the sytem harware details such as manufacture, prod name etc ?
```
    cat /proc/meminfo

    cat /proc/cpuinfo

    dmidecode  -- manufacture details

    dmidecode -type system

    dmidecode -type bios

```
40) The option "Open in Terminal" is missing when user right clicks on terminal in GUI. How to fix
this?
```
    nautils /openterminal
```

## ================================= NETWORK ==========================

1) LAN - LOCAL AREA network -- Connected to the network and each other (EX: HOME)
2) IP -- specific location (logical address)
3) Switch -- connects devices within same network in Local area network
4) Router -- A router is a networking device that forwards data packages between "DIFFERENT" commputer networks from LAN to Outside internet
5) SUBNET - Sub network defines your Local area network Range
6) ANY network needs 3 thinngs -- IP, SUBNET, GATEWAY
7) Gateway -- IP address of the ROUTER 
8) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f6d5b1de-4129-4bbf-b916-636b60863273)
9) Wiki - Network address translation (NAT) is a method of remapping an
IP address while they are in transit across a traffic routing device.
10) fIREWALL - Set of passive rules to protect network from unauthorized access
11) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/150d10e0-c6b4-474a-9ca9-f872870cf117)
12) DMZ -- If any request comes from adhoc devices it passes to router and it sends to DMZ
13) a DMZ or demilitarized zone is a subnetwork that contains and exposes a DEVICE
to an untrusted, network such as the Internet.
14) Port Forwarding redirects a communication request from one address and port numbas
combination to another while the packets are traversing a network gateway.
such as a router or firewall

## =================================== NETWORK TROUBLESHOOTING Command ==========================
```
1- ifconfig
```
IF we want to see where all the network interfaces are available ``` cd /etc/sysconfig/network-scripts/ ```
ifdown and ifup is used to donw and up the network
```
2- ifup etho
```
ifdown lo (lo is look back)
```
3- ifdown etho
4- ping 8.8.8.8
```
ping -c 5 8.8.8.8
```
5- traceroute 8.8.8.8
```
particular routes, gateway and all the hops
```
6- netstat
```
netstat -tunlp == to check open ports, routes, gateways 
```
7- dig www.google.com
```
We get CNAME, server info and etc
```
8- nslookup
```
nslookup www.google.com
canonical name, name and address
```
9- route
```
route -n == gateway ip address
```
10- host (host -t mx ServerGyan.com)
```
it will find all associated ip with our domain
```
11- arp -e
```
arp -e === Hardware info
```
12- ethtool
```
It prints the info of interface of lan card
```
13- hostname
```
hostname -I
```
14- пmap
```
sudo install nmap -y
ip -r 
namep -A <ip-address> == From local machine we can check the remote server ip address like what are the ports are using there
```
15- ip
```
ip r -- ip adddress source and etc info
```
16- ss
```
socket status 
ss -ant == open listening on this ip address, ports and etc
```
17- tracepath
```
tracepath 8.8.8.8 == how it is reaching the ip address all the steps
```
18- ipref
```
sudo apt install iperf
iperf -c <particular-ip-address> 
```
19 tcpdump
```
tcpdump == all the traffic we are receving
tcpdump -i eth0
```
20- telnet
```
sudo apt install telnet
Connectivity check from one OS to other OS
```





