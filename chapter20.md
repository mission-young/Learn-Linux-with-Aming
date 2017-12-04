第二十章 NFS服务配置

# [跟阿铭学linux 2 documentation](index.md)

## 第二十章 NFS服务配置

&#65533;  [第十九章 学会使用简单的MySQL操作](chapter19.md)
  ::   [Contents](index.md)
  ::   [第二十一章
配置FTP服务](chapter21.md)  &#65533;

# 第二十章 NFS服务配置

NFS会经常用到，用于在网络上共享存储。这样讲，您对NFS可能不太了解，阿铭举一个例子来说明一下NFS是用来做什么的。假如有三台机器A, B,
C，它们需要访问同一个目录，目录中都是图片，传统的做法是把这些图片分别放到A, B, C.
但是使用NFS只需要放到A上，然后A共享给B和C即可。访问的时候，B和C是通过网络的方式去访问A上的那个目录的。

## 服务端配置NFS

CentOS上使用NFS服务，需要安装两个包(nfs-utils和rpcbind),
不过当使用yum安装nfs-utils时会把rpcbind一起安装上:

    [root@localhost ~]# yum install -y nfs-utils

在之前的CentOS版本中，是需要安装portmap包的，从CentOS6开始，就改为rpmbind了。NFS配置起来还是蛮简单的，只需要编辑配置文件/etc/exports即可。下面阿铭就先创建一个简单的NFS服务器。

首先是修改配置文件，默认该文件为空，现在编辑它:

    [root@localhost ~]# vim /etc/exports

写入如下内容:

    /home/ 192.168.137.0/24(rw,sync,all_squash,anonuid=501,anongid=501)

这个配置文件就这样简单一行。共分为三部分，第一部分就是本地要共享出去的目录，第二部分为允许访问的主机（可以是一个IP也可以是一个IP段）第三部分就是小括号里面的，为一些权限选项。关于第三部分，阿铭简单介绍一下：

rw ：读写；

ro ：只读；

sync ：同步模式，内存中数据时时写入磁盘；

async ：不同步，把内存中数据定期写入磁盘中；

no_root_squash ：加上这个选项后，root用户就会对共享的目录拥有至高的权限控制，就像是对本机的目录操作一样。不安全，不建议使用；

root_squash：和上面的选项对应，root用户对共享目录的权限不高，只有普通用户的权限，即限制了root；

all_squash：不管使用NFS的用户是谁，他的身份都会被限定成为一个指定的普通用户身份；

anonuid/anongid ：要和root_squash
以及all_squash一同使用，用于指定使用NFS的用户限定后的uid和gid，前提是本机的/etc/passwd中存在这个uid和gid。

介绍了上面的相关的权限选项后，再来分析一下阿铭刚刚配置的那个/etc/exports文件。其中要共享的目录为/home，信任的主机为192.168.137.0/24这个网段，权限为读写，同步，限定所有使用者，并且限定的uid和gid都为501。

编辑好配置文件后，就该启动NFS服务了:

    [root@localhost ~]# /etc/init.d/rpcbind start;  /etc/init.d/nfs start

在启动nfs服务之前，需要先启动rpcbind服务，之前CentOS老版本中并不是rpcbind, 而是叫做portmap.

## 客户端上挂载nfs

客户端在挂载NFS之前，我们需要先看一看服务端都共享了哪些目录，这需要使用showmount命令，但是这个命令是nfs-utils这个包所带的，所以同样需要安装nfs-utils:

    [root@localhost ~]# yum install -y nfs-utils

现在可以看看服务器端都共享了哪些目录了:

    [root@localhost ~]# showmount -e 192.168.137.10
    Export list for 192.168.137.10:
    /home 192.168.137.0/24

可以看到刚才我们在服务端配置的nfs共享信息。 showmount-e
加IP就可以查看NFS的共享情况，上例中，就可以看到192.168.137.10的共享目录为/home，信任主机为192.168.137.0/24这个网段。

下面在客户端上挂载服务端的nfs:

    [root@localhost ~]# mount -t nfs 192.168.137.10:/home/ /mnt/
    [root@localhost ~]# df -h
    文件系统              容量  已用  可用 已用%% 挂载点
    /dev/sda3              14G  6.4G  6.7G  50% /
    tmpfs                 160M     0  160M   0% /dev/shm
    /dev/sda1              97M   27M   66M  29% /boot
    /dev/sdb5             989M   19M  920M   3% /home
    192.168.137.10:/home/
                          989M   19M  920M   3% /mnt

用 df-h 命令可以看到多出来一个/mnt分区，它就是NFS共享的目录了。

在这一章节里，使用的命令不多，另外还有一个常用的命令那就是exportfs，它的常用选项为[-aruv].

-a ：全部挂载或者卸载；

-r ：重新挂载；

-u ：卸载某一个目录；

-v ：显示共享的目录；

使用exportfs命令，当改变/etc/exports配置文件后，不用重启nfs服务直接用这个exportfs即可。接下来阿铭做一个实验，先改一下服务端的配置文件:

    [root@localhost ~]# vim /etc/exports

增加一行:

    /tmp/ 192.168.137.0/24(rw,sync,no_root_squash)

然后服务端上执行命令:

    [root@localhost ~]# exportfs -arv
    exporting 192.168.137.0/24:/tmp
    exporting 192.168.137.0/24:/home

在之前的命令中用到了mount命令来挂载nfs，其实mount这个nfs服务还是有些说法的。首先是用-t nfs
来指定挂载的类型为nfs。另外在使用nfs时，常用一个选项就是 -o nolock 了，即在挂载nfs服务时，不加锁。 在客户端上执行:

    [root@localhost ~]# mkdir /test
    [root@localhost ~]# mount -t nfs -o nolock 192.168.137.10:/tmp/ /test/

我们还可以把要挂载的nfs目录写到client上的/etc/fstab文件中，挂载时只需要执行 mount-a 即可。在 /etc/fstab里加一行:

    192.168.137.10:/tmp/            /test        nfs       nolock  0 0

因为刚刚挂载过，所以先卸载:

    [root@localhost ~]# umount  /test/

然后执行:

    [root@localhost ~]# mount -a

这样也可以挂载上，而且以后开机会自动挂载上。

关于NFS部分就讲这么多，内容并不多，相信您很快就能掌握！

阿铭建议您最好再扩展学习一下: [http://www.lishiming.net/thread-5444-1-1.md](http://www.lishiming.net/thread-5444-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com)
和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

&#65533;  [第十九章 学会使用简单的MySQL操作](chapter19.md)
  ::   [Contents](index.md)
  ::   [第二十一章
配置FTP服务](chapter21.md)  &#65533;

&copy; Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1.
