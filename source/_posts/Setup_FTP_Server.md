---
title: Setup FTP Server on Centos 6
date: 2017-07-26
tags: S/FTP
categories: VM配置
---

# Set FTP Server
Env: CentOS 6

## Step
### Install vsftpd(server) and ftp(client)
    [root@localhost ~]# sudo yum install -y vsftpd
    [root@localhost ~]# sudo yum install -y ftp

<!-- more -->

### Configuration
vsftpd configuration file is /etc/vsftpd/vsftpd.conf, make sure options are set as follow:

    anonymous_enable=NO
    local_enable=YES
    # not allowed to visit other part of server
    chroot_local_user=YES
    
### Startup
    [root@localhost ~]# service vsftpd restart

# Extend to FTPS
vsftpd may use SSL/TLS and serves as FTPS server.
## Step
### Create certificate and key file
    [root@localhost ~]# mkdir /etc/ssl/private
    [root@localhost ~]# openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem

### Config key file to vsftpd
Open /etc/vsftpd/vsftpd.conf, set certificate file options:
    
    # SSL configuration
    # RSA
    rsa_cert_file=/etc/ssl/private/vsftpd.pem
    rsa_private_key_file=/etc/ssl/private/vsftpd.pem
    
### Enable SSL/TLS 
    # SSL
    ssl_enable=YES
    allow_anon_ssl=NO
    force_local_data_ssl=YES
    force_local_logins_ssl=YES
    
    # TLS
    ssl_tlsv1=YES
    ssl_sslv2=NO
    ssl_sslv3=NO
    
    require_ssl_reuse=NO
    ssl_ciphers=HIGH

### Create user for access
Similiar like user creation in SFTP server setup.

## Java Client
**Apache Commons Net**  
[Apache Commons Net Site](https://commons.apache.org/proper/commons-net/)  
Use *FTPClient* to connect FTP server, and use *FTPSClient* to connect FTPS server.

**Apache Common VFS**:  
See SFTP server setup.
