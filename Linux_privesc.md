
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
### Kernel Exploits
```bash
Ubuntu 16.04.4 kernel priv esc - https://vulners.com/zdt/1337DAY-ID-30003
Screen Version 4.5.0 
```
