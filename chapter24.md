# [跟阿铭学linux 2 documentation](index.md)

## 第二十四章 配置SAMBA服务器

«  [第二十三章 配置Tomcat](chapter23.md)   ::   [Contents](index.md)   ::   [第二十五章 MySQL replication(主从)配置](chapter25.md)  »

# 第二十四章 配置Samba服务器

以前我们在windows上共享文件的话，只需右击要共享的文件夹然后选择共享相关的选项设置即可。然而如何实现windows和linux的文件共享呢？这就涉及到了samba服务了，这个软件配置起来也不难，使用也非常简单。

## samba配置文件smb.conf

安装系统的时候大多会默认安装samba，如果没有安装，在CentOS上只需要运行这个命令安装即可:

    yum install -y samba samba-client

Samba的配置文件为/etc/samba/smb.conf，通过修改这个配置文件来完成我们的各种需求。打开这个配置文件，你会发现很多内容都用 # 或者 ; 注视掉了。先看一下未被注释掉的部分:

    [global]
            workgroup = MYGROUP
            server string = Samba Server Version %v
            security = user
            passdb backend = tdbsam
            load printers = yes
            cups options = raw
    [homes]
            comment = Home Directories
            browseable = no
            writable = yes
    [printers]
            comment = All Printers
            path = /var/spool/samba
            browseable = no
            guest ok = no
            writable = no
            printable = yes

主要有以上三个部分：[global], [homes], [printers]

[global] 定义全局的配置，workgroup用来定义工作组，相信如果您安装过windows的系统，你会对这个workgroup不陌生。一般情况下，需要我们把这里的MYGROUP改成WORKGROUP（windows默认的工作组名字）。

security = user #这里指定samba的安全等级。关于安全等级有四种：

share：用户不需要账户及密码即可登录samba服务器

user：由提供服务的samba服务器负责检查账户及密码（默认）

server：检查账户及密码的工作由另一台windows或samba服务器负责

domain：指定windows域控制服务器来验证用户的账户及密码。

passdb backend = tdbsam # passdb backend（用户后台），samba有三种用户后台：smbpasswd, tdbsam和ldapsam.

smbpasswd：该方式是使用smb工具smbpasswd给系统用户（真实用户或者虚拟用户）设置一个Samba密码，客户端就用此密码访问Samba资源。smbpasswd在/etc/samba中，有时需要手工创建该文件。

tdbsam：使用数据库文件创建用户数据库。数据库文件叫passdb.tdb，在/etc/samba中。passdb.tdb用户数据库可使用 smbpasswd -a 创建Samba用户，要创建的Samba用户必须先是系统用户。也可使用pdbedit创建Samba账户。pdbedit参数很多，列出几个主要的：

pdbedit -a username：新建Samba账户。

pdbedit -x username：删除Samba账户。

pdbedit -L：列出Samba用户列表，读取passdb.tdb数据库文件。

pdbedit -Lv：列出Samba用户列表详细信息。

pdbedit -c “[D]” -u username：暂停该Samba用户账号。

pdbedit -c “[]” -u username：恢复该Samba用户账号。

ldapsam：基于LDAP账户管理方式验证用户。首先要建立LDAP服务，设置 “passdb backend = ldapsam:ldap://LDAP Server”

load printers 和 cups options 两个参数用来设置打印机相关。

除了这些参数外，还有几个参数需要你了解：

netbios name = MYSERVER # 设置出现在网上邻居中的主机名

hosts allow = 127. 192.168.12. 192.168.13. # 用来设置允许的主机，如果在前面加 ”;” 则表示允许所有主机

log file = /var/log/samba/%m.log #定义samba的日志，这里的%m是上面的netbios name

max log size = 50 # 指定日志的最大容量，单位是K

[homes] 该部分内容共享用户自己的家目录，也就是说，当用户登录到samba服务器上时实际上是进入到了该用户的家目录，用户登陆后，共享名不是homes而是用户自己的标识符，对于单纯的文件共享的环境来说，这部分可以注视掉。

[printers] 该部分内容设置打印机共享。

## samba实践

注意：在试验之前，请先检测selinux是否关闭，否则可能会试验不成功。关于如何关闭selinux请查看第十六章linux系统日常管理的linux的防火墙部分([http://study.lishiming.net/chapter16.md#id3](http://study.lishiming.net/chapter16.md#id3))

**1. 共享一个目录，任何人都可以访问，即不用输入密码即可访问，要求只读**

打开samba的配置文件/etc/samba/smb.conf 在[global]部分

把:

    MYGROUP

改成:

    WORKGROUP

把:

    security=user

修改为:

    security=share

然后在文件的最末尾处加入以下内容:

    [share]
            comment = share all
            path = /tmp/samba
            browseable = yes
            public = yes
            writable = no

创建测试目录:

    mkdir /tmp/samba
    chmod 777 /tmp/samba
    touch /tmp/samba/sharefiles
    echo "111111" > /tmp/samba/sharefiles

启动samba服务:

    /etc/init.d/smb start

测试：

首先测试你配置的smb.conf是否正确，用下面的命令:

    testparm

您应该会看到一个警告:WARNING: The security=share option is deprecated, 不过影响不大，无需管它。如果没有错误，则在你的windows机器上的浏览器中输入:

    file://IP/share

看是否能访问到sharefiles

**2. 共享一个目录，使用用户名和密码登录后才可以访问，要求可以读写**

打开samba的配置文件/etc/samba/smb.conf

[global] 部分内容如下:

    [global]
            workgroup = WORKGROUP
            server string = Samba Server Version %v
            security = user
            passdb backend = tdbsam
            load printers = yes
            cups options = raw

还需要加入以下内容:

    [myshare]
            comment = share for users
            path = /samba
            browseable = yes
            writable = yes
            public = no

保存配置文件，创建目录:

    mkdir /samba
    chmod 777 /samba

然后添加用户。因为在[globa]中 “passdb backend = tdbsam”, 所以要使用 pdbedit 来增加用户，注意添加的用户必须在系统中存在，所以需要先创建系统账号:

    useradd user1
    useradd user2

然后添加user1为samba账号:

    pdbedit -a user1

再添加user2为samba账号:

    pdbedit -a user2

我们可以列出samba所有账号:

    pdbedit-L

重启samba服务:

    service smb restart

测试：

打开IE浏览器输入:

    file://IP/myshare/

然后输入用户名和密码

**3. 使用linux访问samba服务器**

Samba服务在linux下同样可以访问。前提是您的linux安装了samba-client软件包。安装完后就可以使用smbclient命令了。具体语法为:

    smbclient //IP/共享名  -U 用户名

如:

    [root@localhost]# smbclient //10.0.4.67/myshare/ -U user1
    Password:
    Domain=[WORKGROUP] OS=[Unix] Server=[Samba 3.6.9-151.el6]
    smb: \>

出现如上所示的界面。可以打一个 ”?” 列出所有可以使用的命令。常用的有cd, ls, rm, pwd, tar, mkdir, chown, get, put等等，使用 help + 命令可以打印该命令如何使用，其中get是下载，put是上传。

另外的方式就是通过mount挂载了，如:

    mount -t cifs //10.0.4.67/myshare /mnt -o username=user1,password=123456

格式就是这样，要指定 -t cifs //IP/共享名 本地挂载点 -o后面跟username 和 password 挂载完后就可以像使用本地的目录一样使用共享的目录了，注意共享名后面不能有斜杠。

阿铭建议您最好再扩展学习一下: [http://www.lishiming.net/thread-5448-1-1.md](http://www.lishiming.net/thread-5448-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com/) 和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

«  [第二十三章 配置Tomcat](chapter23.md)   ::   [Contents](index.md)   ::   [第二十五章 MySQL replication(主从)配置](chapter25.md)  »

© Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1.
