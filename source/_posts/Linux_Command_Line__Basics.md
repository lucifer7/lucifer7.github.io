---
title: Linux Command Line Basics
date: 2017-08-17
tags: Linux
categories: 学习记录
---

# Linux Command Line Basics
## System Directory Structure
Case sensitive  
Root path: / 

<!-- more -->
 
| Path | Detail |
|----|:---:|
| /bin | executable commands(for all users) |
| /sbin | super bin (for root) |
| /boot | startup, kernel |
| /dev | devices (files) |
| /etc | config files, pure text files |
| /home | user home dirctory |
| /root | root user home |
| /lib | library files |
| /opt | large software |
| /proc | process |
| /sys | hardware info |
| /tmp | temporary |
| /usr | user application |
| /var | variable, e.g log |
| /media | media |
| /mnt | mount |

## File Operations
### Create New File
    $ > newfile / >> new file
    $ touch newfile
    $ cat > file

### Delete
    $ rm -rf *

### Mofify
Modify access time:
    
    $ touch filename

Modify content:

    $ vi
    $ vim
    $ nano

### Move, Copy, Rename
    $ mv src dest
    $ cp src dest

## File Mode/Permission
### Permission Groups
u: user  
g: group  
o: other  
a: all users

### Permission Types
r: read, 4  
w: write, 2  
x: execute, 1  

### Permissions for Directory
r: read structure
w: modify structure
x: access directory(as working directory)

### Command
    $ chmod/ll
    $ lsattr/chattr
    $ umask 

## Directory Operations
### Change Directory
    $ cd
    $ pwd

Special notation:  
. present directory  
.. parent directory  
~ home of present user  
~user home of this user  
/ root  
\- last working directory  

### Create path
    $ mkdir [-P] path

With -P option, create parent path if not exist. 

### Remove Directory
    $ rmdir [-P] path

## Command Operations
View previous commands(of present user):  
    
    $ history

Execute previous command:  
    
    $ !num
    $ !!

Search command:  
    
    Ctrl + R
