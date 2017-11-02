---
title: Setup SFTP Server on Centos 6
date: 2017-07-26
tags: S/FTP
categories: VM配置
---

# Set SFTP Server
Env: Centos 6

## Step
### Add goup, user and permission
    # create a group for SFTP
    [root@dlp ~]# groupadd sftp_users 
    # apply to a user "mysftp" for SFTP only
    [root@dlp ~]# usermod -G sftp_users mysftp
	# usermod bin(require ?)
	[root@dlp ~]# usermod -s /bin/false mysftp
	# chrooted home directory
	[root@dlp ~]# chown root:root mysftp
	# set permissions for home directory
	[root@dlp ~]# chmod 755 mysftp

<!--more-->

### Update sshd config file
    [root@dlp ~]# vim /etc/ssh/sshd_config
    # line 132: comment out and add a line like below
    #Subsystem sftp /usr/libexec/openssh/sftp-server
    Subsystem sftp internal-sftp -f AUTH -l VERBOSE
    # add follows to the end
    Match Group sftp_users
      X11Forwarding no
      AllowTcpForwarding no
      ChrootDirectory %h
      ForceCommand internal-sftp
    [root@dlp ~]# /etc/rc.d/init.d/sshd restart 
	# and make sure below
	PasswordAuthentication yes
	UsePAM yes

### Check connection
	[root@localhost ~]# sftp mysftp@10.200.159.83
    Connecting to 10.200.159.83...
    mysftp@10.200.159.83's password: 
    sftp> ls
    downloads  uploads
    sftp> ll
    Invalid command.
    sftp> ls -l
    drwxr-xr-x2 004096 Jul 10 00:22 downloads
    drwxr-xr-x2 004096 Jul 10 00:22 uploads
    sftp> 

### Mind SELinux and chrooted SFTP

## Possible error
> SFTP Error - Couldn't read packet: Connection reset by peer

> Write failed: Broken pipe Couldn't read packet: Connection reset by peer

> sftp Couldn't get handle: Permission denied

Check configuration.

> Network error: Software caused connection abort - FTP - FileZilla

Do NOT chmod to 777/775

> Couldn't create directory: Permission denied
> (Cannot upload file/create dir)

    [root@dlp ~]# setsebool -P ssh_chroot_rw_homedirs on

## Java client
**jsch**:  
most common, [jsch site](http://www.jcraft.com/jsch/)  
**Apache Common VFS**:  
less doc and demo, based on jsch. If you need to handle multiple protocols, this is recommended.  
[Apache Commons Virtual File System Site](https://commons.apache.org/proper/commons-vfs/)  
[VFS Proxy](https://github.com/lucifer7/java-study/blob/master/src/main/java/util/VFSProxy.java)  
**sshj**  
...
