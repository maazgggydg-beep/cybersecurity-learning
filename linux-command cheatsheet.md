# Linux Command Cheat Sheet (Cybersecurity Focus)

This document contains essential Linux commands with explanations.
It is built and expanded as part of my hands-on cybersecurity learning.

---

## 1. Navigation & File System

pwd  
→ Show current working directory

ls  
→ List files in directory

ls -la  
→ List all files with permissions and hidden files

cd folder  
→ Change directory

cd ..  
→ Move one directory back

cd ~  
→ Go to home directory

tree  
→ Show directory structure (if installed)

---

## 2. File & Directory Management

touch file.txt  
→ Create empty file

mkdir folder  
→ Create directory

mkdir -p a/b/c  
→ Create nested directories

rm file.txt  
→ Delete file

rm -r folder  
→ Delete directory recursively

rm -rf folder  
→ Force delete (DANGEROUS)

cp file1 file2  
→ Copy file

cp -r dir1 dir2  
→ Copy directory

mv old new  
→ Move or rename file

stat file.txt  
→ File metadata

file file.txt  
→ Detect file type

---

## 3. Viewing & Reading Files

cat file.txt  
→ Print file content

less file.txt  
→ Scroll file content

more file.txt  
→ Basic scrolling

head file.txt  
→ First 10 lines

tail file.txt  
→ Last 10 lines

tail -f log.txt  
→ Live log monitoring

nl file.txt  
→ Show line numbers

strings binary  
→ Extract readable strings (security useful)

---

## 4. Searching & Filtering

grep "text" file  
→ Search text in file

grep -i "text" file  
→ Case-insensitive search

grep -r "text" folder  
→ Recursive search

find / -name file.txt  
→ Find file by name

find . -type f  
→ Find files only

locate file  
→ Fast search (needs updatedb)

which command  
→ Show command path

whereis command  
→ Locate binaries

---

## 5. Permissions & Ownership

ls -l  
→ View permissions

chmod +x script.sh  
→ Make executable

chmod 644 file  
→ Set permissions

chown user file  
→ Change owner

chown user:group file  
→ Change owner & group

id  
→ User ID info

umask  
→ Default permissions

---

## 6. Users & Groups

whoami  
→ Current user

who  
→ Logged-in users

w  
→ Active users + processes

adduser user  
→ Add user

passwd user  
→ Change password

groups  
→ User groups

su  
→ Switch user

sudo command  
→ Run as root

---

## 7. Process Management

ps  
→ Running processes

ps aux  
→ Detailed process list

top  
→ Live process view

htop  
→ Advanced process viewer

kill PID  
→ Kill process

kill -9 PID  
→ Force kill

pkill name  
→ Kill by name

bg  
→ Resume in background

fg  
→ Resume in foreground

jobs  
→ Show background jobs

---

## 8. Disk & Storage

df -h  
→ Disk usage

du -sh folder  
→ Folder size

lsblk  
→ Block devices

mount  
→ Mounted drives

umount device  
→ Unmount

fdisk -l  
→ Disk partitions

---

## 9. Networking (Security-Relevant)

ip a  
→ Network interfaces

ip r  
→ Routing table

ifconfig  
→ Interface config (legacy)

ping domain  
→ Connectivity test

traceroute domain  
→ Route path

netstat -tuln  
→ Open ports (legacy)

ss -tuln  
→ Open ports (modern)

curl url  
→ HTTP request

wget url  
→ Download file

nc host port  
→ Netcat (VERY IMPORTANT)

nmap target  
→ Network scanning

---

## 10. Archives & Compression

tar -cvf file.tar folder  
→ Create archive

tar -xvf file.tar  
→ Extract archive

gzip file  
→ Compress

gunzip file.gz  
→ Decompress

zip -r file.zip folder  
→ Zip folder

unzip file.zip  
→ Unzip

---

## 11. Package Management

apt update  
→ Update package list

apt upgrade  
→ Upgrade system

apt install pkg  
→ Install package

apt remove pkg  
→ Remove package

dpkg -i file.deb  
→ Install deb file

---

## 12. Environment & System Info

env  
→ Environment variables

export VAR=value  
→ Set variable

uname -a  
→ Kernel info

hostname  
→ System name

uptime  
→ System runtime

free -h  
→ Memory usage

vmstat  
→ Memory stats

---

## 13. Redirection & Pipes (CRITICAL)

command > file  
→ Output to file

command >> file  
→ Append output

command < file  
→ Input from file

command1 | command2  
→ Pipe output

2> error.txt  
→ Redirect errors

&> all.txt  
→ Redirect all output

---

## 14. Bash Shortcuts

CTRL + C  
→ Stop process

CTRL + Z  
→ Pause process

CTRL + R  
→ Search history

!!  
→ Repeat last command

!n  
→ Run command number n

history  
→ Command history

---

## Notes
This cheat sheet focuses on commands relevant to cybersecurity, system administration, and daily Linux usage.
It is expanded progressively as I gain deeper understanding.
