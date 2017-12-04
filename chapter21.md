第二十一章 配置FTP服务

# [跟阿铭学linux 2 documentation](index.md)

## 第二十一章 配置FTP服务

&#65533;  [第二十章 NFS服务配置](chapter20.md)
  ::   [Contents](index.md)
  ::   [第二十二章
配置Squid服务](chapter22.md)  &#65533;

# 第二十一章 配置FTP服务

也许您对FTP不陌生，但是您是否了解FTP到底是个什么玩意？ FTP 是File Transfer
Protocol（文件传输协议）的英文简称，而中文简称为 “文传协议”
用于Internet上的控制文件的双向传输。同时，它也是一个应用程序（Application）。用户可以通过它把自己的PC机与世界各地所有运行FTP协议的服务器相连，访问服务器上的大量程序和信息。FTP的主要作用，就是让用户连接上一个远程计算机（这些计算机上运行着FTP服务器程序）查看远程计算机有哪些文件，然后把文件从远程计算机上拷到本地计算机，或把本地计算机的文件送到远程计算机去。FTP用的比NFS更多，所以请您一定要熟练配置它。

其实在CentOS或者RedHat Linux上有自带的ftp软件叫做vsftp,
但阿铭介绍的并不是它，如果您有兴趣可以和阿铭交流，阿铭本章使用pure-ftpd搭建ftp服务器，因为这个软件比vsftp配置起来更加灵活和安全。

## 安装pure-ftpd

**1. 下载软件**

pure-ftpd 官网是 [http://www.pureftpd.org/project/pure-ftpd](http://www.pureftpd.org/project/pure-ftpd)
当前最新版本为1.0.36, 但阿铭不建议使用最新版本，最新版有可能有一些小bug.

    [root@localhost ~]# cd /usr/local/src/
    [root@localhost src]# wget http://download.pureftpd.org/pub/pure-ftpd/releases/pure-ftpd-1.0.32.tar.bz2

**2. 安装pure-ftpd**

    [root@localhost src]# tar jxf pure-ftpd-1.0.32.tar.bz2
    [root@localhost src]# cd pure-ftpd-1.0.32
    [root@localhost pure-ftpd-1.0.32]# ./configure \
    --prefix=/usr/local/pureftpd \
    --without-inetd \
    --with-altlog \
    --with-puredb \
    --with-throttling \
    --with-peruserlimits  \
    --with-tls
    [root@localhost pure-ftpd-1.0.32]# make && make install

## 配置pure-ftpd

**1. 修改配置文件**

pure-ftpd 编译安装很快就完成了，而且极少有出现错误的时候，下面就该配置它了:

    [root@localhost pure-ftpd-1.0.32]# cd configuration-file
    [root@localhost pure-ftpd-1.0.32]# mkdir -p /usr/local/pureftpd/etc/
    [root@localhost configuration-file]# cp pure-ftpd.conf    /usr/local/pureftpd/etc/pure-ftpd.conf
    [root@localhost configuration-file]# cp pure-config.pl    /usr/local/pureftpd/sbin/pure-config.pl
    [root@localhost configuration-file]# chmod 755    /usr/local/pureftpd/sbin/pure-config.pl

在启动pure-ftpd之前需要先修改配置文件，配置文件为/usr/local/pureftpd/etc/pure-ftpd.conf,
您可以打开看一下，里面内容很多，如果英文好，可以好好研究一番，下面是阿铭的配置文件，如果您嫌麻烦，直接拷贝过去即可:

    ChrootEveryone              yes
    BrokenClientsCompatibility  no
    MaxClientsNumber            50
    Daemonize                   yes
    MaxClientsPerIP             8
    VerboseLog                  no
    DisplayDotFiles             yes
    AnonymousOnly               no
    NoAnonymous                 no
    SyslogFacility              ftp
    DontResolve                 yes
    MaxIdleTime                 15
    PureDB                        /usr/local/pureftpd/etc/pureftpd.pdb
    LimitRecursion              3136 8
    AnonymousCanCreateDirs      no
    MaxLoad                     4
    AntiWarez                   yes
    Umask                       133:022
    MinUID                      100
    AllowUserFXP                no
    AllowAnonymousFXP           no
    ProhibitDotFilesWrite       no
    ProhibitDotFilesRead        no
    AutoRename                  no
    AnonymousCantUpload         no
    PIDFile                     /usr/local/pureftpd/var/run/pure-ftpd.pid
    MaxDiskUsage               99
    CustomerProof              yes

**2. 启动pure-ftpd**

    [root@localhost ~]# /usr/local/pureftpd/sbin/pure-config.pl /usr/local/pureftpd/etc/pure-ftpd.conf

如果是启动成功，会显示一行长长的以Running开头的信息，否则那就是错误信息，如果您解决不了，请到阿铭论坛([http://www.lishiming.net/forum-40-1.md](http://www.lishiming.net/forum-40-1.md))获取帮助吧。

**3. 建立账号**

    [root@localhost ~]# mkdir /data/www/
    [root@localhost ~]# useradd www
    [root@localhost ~]# chown -R www:www /data/www/
    [root@localhost ~]# /usr/local/pureftpd/bin/pure-pw useradd ftp_user1  -uwww -d /data/www/
    Password:
    Enter it again:

其中，-u将虚拟用户ftp_user1与系统用户www关联在一起，也就是说使用ftp_user1账号登陆ftp后，会以www的身份来读取文件或下载文件。-d
后边的目录为ftp_user1账户的家目录，这样可以使ftp_user1只能访问其家目录/data/www/.
到这里还未完成，还有最关键的一步，就是创建用户信息数据库文件:

    [root@localhost ~]#  /usr/local/pureftpd/bin/pure-pw mkdb

pure-pw还可以列出当前的ftp账号，当然也可以删除某个账号, 我们再创建一个账号:

    [root@localhost ~]#  /usr/local/pureftpd/bin/pure-pw  useradd ftp_user2 -uwww -d /tmp
    [root@localhost ~]#  /usr/local/pureftpd/bin/pure-pw mkdb

列出当前账号:

    [root@localhost ~]# /usr/local/pureftpd/bin/pure-pw list

删除账号的命令为:

    [root@localhost ~]#  /usr/local/pureftpd/bin/pure-pw  userdel ftp_user2

## 测试pure-ftpd

测试需要使用的工具叫做lftp, 先安装一下它:

    [root@localhost ~]# yum install -y lftp

测试:

    [root@localhost ~]# touch /data/www/123.txt
    [root@localhost ~]# lftp ftp_user1@127.0.0.1
    口令:
    lftp ftp_user1@127.0.0.1:~> ls
    drwxr-xr-x    2 514        www              4096 Jun 12 11:14 .
    drwxr-xr-x    2 514        www              4096 Jun 12 11:14 ..
    -rw-r--r--    1 514        www                 0 Jun 12 11:14 123.txt

登陆后，使用 ls
命令可以列出当前目录都有什么文件。

阿铭建议您最好再扩展学习一下: [http://www.lishiming.net/thread-5445-1-1.md](http://www.lishiming.net/thread-5445-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com)
和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

&#65533;  [第二十章 NFS服务配置](chapter20.md)
  ::   [Contents](index.md)
  ::   [第二十二章
配置Squid服务](chapter22.md)  &#65533;

&copy; Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1. 
