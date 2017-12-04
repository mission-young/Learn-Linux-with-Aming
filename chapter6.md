第六章 Linux系统的远程登陆

# [跟阿铭学linux 2 documentation](index.md)

## 第六章 Linux系统的远程登陆

&#65533;  [第五章 初步认识Linux](chapter5.md)  ::   [Contents](index.md)  ::   [第七章 Linux文件与目录管理](chapter7.md)  &#65533;

# 第六章 Linux系统的远程登陆

Linux大多应用于服务器，而服务器不可能像PC一样放在办公室，它们是放在IDC机房的，所以阿铭平时登录Linux系统都是通过远程登录的。Linux系统中是通过ssh服务实现的远程登录功能。默认sshd服务开启了22端口，而且当我们安装完系统时，这个服务已经安装，并且是开机启动的。所以不需要我们额外配置什么就能直接远程登录Linux系统。sshd服务的配置文件为
/etc/ssh/sshd_config，您可以修改这个配置文件来实现您想要的sshd服务。比如您可以更改启动端口为11587.

如果您是windows的操作系统，则Linux远程登录需要在我们的机器上额外安装一个终端软件。目前比较常见的终端登录软件有SecureCRT,
Putty, SSH Secure
Shell等，很多朋友喜欢用SecureCRT因为它的功能是很强大的，而阿铭喜欢用Putty，只是因为它的小巧以及非常漂亮的颜色显示。不管您使用哪一个客户端软件，最终的目的只有一个，就是远程登录到Linux服务器上。这些软件网上有很多免费版的，您可以下载一个试着玩玩。

您不妨跟着阿铭一起来用一用Putty这个小巧的工具。

## 下载Putty

阿铭建议您到Putty的官方站点去下载英文版原版的putt.
网上曾经报过，某个中文版的Putt被别有用心的黑客给动了手脚，给植了后门。所以，阿铭提醒各位，以后不管下载什么软件尽量去官方站点下载。阿铭给出下载地址： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.md](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.md)
连接远程Linux服务器的工具只需要下载putty.exe即可 [http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe](http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe)
下载后直接双击运行就可以了不需要安装。

## 给您的Linux配置IP

要想远程连接Linux服务器，首先需要知道服务器的IP。因为阿铭用的虚拟机，而且虚拟机所跑的真机是自动获得的ip，所以虚拟机也可以自动获得ip。如果您是一步一步跟阿铭装的Linux那么您的Linux目前肯定是没有IP的，下面阿铭教您几种配置IP的方法：

1. **自动获取IP**

只有一种情况可以自动获取IP地址，那就是您的Linux所在的网络环境中有DHCP服务。[[1]](#id8) 总之，只要您的真机可以自动获取IP，那么安装在虚拟机的Linux同样也可以自动获取IP.
方法很简单，只需要运行一个命令。

    [root@localhost ~]# dhclient

运行这条命令后，会出现一大堆信息，您不用关心是什么。然后运行 ‘ifconfig’ 命令查看IP是什么：

    [root@localhost ~]# ifconfig
    eth0      Link encap:Ethernet  HWaddr 00:0C:29:D9:F0:52
              inet addr:10.72.137.85  Bcast:10.72.137.255  Mask:255.255.255.0
              inet6 addr: fe80::20c:29ff:fed9:f052/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:27135 errors:0 dropped:0 overruns:0 frame:0
              TX packets:53 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000
              RX bytes:3488498 (3.3 MiB)  TX bytes:7550 (7.3 KiB)
              Interrupt:18 Base address:0x1080

    lo        Link encap:Local Loopback
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:16436  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0
              RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)

通过这个命令可以查看系统有几块网卡和网卡的IP，阿铭的系统eth0的IP是 10.72.137.85.
如果您的Linux有多块网卡，那么在Linux中它会显示成eth1, eth2 依此类推。

1. **手动配置IP**

如果您的虚拟机不能自动获取IP，那么只能手动配置，配置方法为：

    [root@localhost ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0

使用vi 命令打开 “/etc/sysconfig/network-scripts/ifcfg-eth0” 这个配置文件。关于命令 vi
阿铭会在后续章节详细介绍，暂时您只要了解这个命令是用来编辑文件的即可。输入上述命令后回车，打开了该配置文件。使用方向键的向下箭头让光标移动到最后面一行，然后按字母键
‘o’，进入编辑模式，增加如下内容：

    IPADDR=10.72.137.85
    NETMASK=255.255.255.0
    GATEWAY=10.72.137.1

请注意，由于阿铭不知道您的网络具体环境，所以也不晓得您应该配置什么样的IP，请不要直接照搬阿铭给出的例子，这样配置肯定是不行的，请配置成和您的真机（windows）在同一个网段的IP。关于netmask以及gateway的概念请自行在网上查询，这是关于网络技术的基础知识。另外还需要把光标移动到
“ONBOOT=no” 这一行，改为:

    ONBOOT=yes

“BOOTPROTO=dhcp” 改为:

    BOOTPROTO=none

之后按一下键盘左上角的 “ESC”键，然后输入 :wq , 它会显示在屏幕的左下方，然后按回车，这样就保存该配置文件了。之后，需要重启一下网络服务：

    [root@localhost ~]# service network restart
    正在关闭接口 eth0：                                        [确定]
    关闭环回接口：                                             [确定]
    弹出环回接口：                                             [确定]
    弹出界面 eth0：                                            [确定]

这样网络重启后，eth0 的IP就生效了。使用 “ifconfig eth0” 命令查看一下：

    [root@localhost ~]# ifconfig eth0
    eth0      Link encap:Ethernet  HWaddr 00:0C:29:D9:F0:52
              inet addr:10.72.137.85  Bcast:10.72.137.255  Mask:255.255.255.0
              inet6 addr: fe80::20c:29ff:fed9:f052/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:27135 errors:0 dropped:0 overruns:0 frame:0
              TX packets:53 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000
              RX bytes:3488498 (3.3 MiB)  TX bytes:7550 (7.3 KiB)
              Interrupt:18 Base address:0x1080

接下来请检测一下您配置的IP是否可以ping通。阿铭使用的windows7系统，所以使用cmd打开命令窗口，进行检测。打开cmd的快捷键是 windows+r.

    C:\Users\Administrator>ping 10.72.137.85

    正在 Ping 10.72.137.85 具有 32 字节的数据:
    来自 10.72.137.85 的回复: 字节=32 时间=1ms TTL=64
    来自 10.72.137.85 的回复: 字节=32 时间<1ms TTL=64
    来自 10.72.137.85 的回复: 字节=32 时间<1ms TTL=64
    来自 10.72.137.85 的回复: 字节=32 时间<1ms TTL=64

    10.72.137.85 的 Ping 统计信息:
        数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
    往返行程的估计时间(以毫秒为单位):
        最短 = 0ms，最长 = 1ms，平均 = 0ms

1. **利用vmware的NAT给Linux配置IP**

这一部分内容，阿铭曾经在论坛里写过一个帖子 [http://www.lishiming.net/thread-626-1-1.md](http://www.lishiming.net/thread-626-1-1.md)
如果您已经配置好IP且可以ping通，这一部分设置则不需要再做了，但有必要了解一下，也许有一天您会用到。这一部分配置适合这样的场景：您的办公网不能通过dhcp获得IP，或者您不想让您的Linux处在和办公网一个网段，而且也想让Linux上网。

- 设置虚拟机上的nat

> Edit –> Virtual Network setting –> NAT –> Vmnet 8 Gateway IP
>   address : 192.168.205.2 Netmask : 255.255.255.0 NAT service: Started –>
>   确定

- 修改虚拟机的网卡设置

> 双击虚拟机右下角的网卡小图标，鼠标移动过去后会显示 “Ethernet: ...” Device status 那两项都需要打对钩；
>   Network connection 需要选择最后一项(Custom:Specific virtual network) 选择Vmnet8(NAT)
>   最后点ok

- 到您的电脑上 [[2]](#id9)

> 右击”网上邻居” –> 属性 –> 右击 “VMware Network Adapter VMnet8” –> 属性 –>
>   双击 “Internet 协议（TCP/IP)” –> 手动设置IP为 192.168.205.1 子网掩码为 255.255.255.0 网关 和
>   dns 都设置为 192.168.205.2 –> 确定 –> 确定

- 设置您虚拟机IP

> 在您的Linux上编辑eth0的配置文件 vi  /etc/sysconfig/network-scripts/ifcfg-eth0 内容如下：
>
>
>     DEVICE=eth0
>     BOOTPROTO=none
>     HWADDR=00:0C:29:33:F7:3A
>     ONBOOT=yes
>     IPADDR=192.168.205.3
>     NETMASK=255.255.255.0
>     GATEWAY=192.168.205.2

- 设置DNS地址

> 运行命令 vi  /etc/resolv.conf 内容如下：
>
>
>     nameserver 192.168.205.2

- 重启网络服务

> 运行命令 service
>   network  restart

如果您遇到类似这样的问题：重启网络服务后，发现/etc/resolv.conf中设置的DNS地址消失了，您可以参考这个帖子解决您的问题：[http://www.lishiming.net/thread-5548-1-1.md](http://www.lishiming.net/thread-5548-1-1.md)

## 用putty登陆您的Linux

上一小节阿铭带您设置IP，就是给这一部分做铺垫，没有IP是没有办法远程连接Linux的。双击先前下载的putty.exe文件，这个小工具特别小巧仅仅有几百K，但是您可不要小看它，功能可是不少呢，而且这个工具的帮助文档够您看好几天的了，关键是全都是英文。如果您的英文能力差一些也没有关系，相信随着您用Linux越来越多，您的英文能力也会越来越强。

- 填写远程Linux基本信息

> Host Name (or IP address) 这一栏填写您在上一小节刚刚配置的IP，阿铭的Linux IP为
>   “10.72.137.85”.
>
> Port 这一栏保持默认不变。
>
> Connection type 也保持默认。
>
> Saved Sessions
> 这里自定义一个名字，主要用来区分主机，因为将来您的主机会很多，写个简单的名字即方便记忆又能快速查找。

- 定义字符集

> 计算机里最烦人的就是字符集了，尤其是Linux，搞不好就会乱码。阿铭在第三章教您安装CentOS时已经安装了中文语言支持，所以安装好的系统是支持中文的，在putty这里设置也要支持中文。点一下左侧的
>   “Window” –> “Translation”, 看右侧的 “Character set translation on received
>   data”, 选择UTF-8. 之后再点一下左侧的 “Session”, 然后点右侧的 “save”.

- 远程连接您的Linux

> 保存session后，点最下方的 “Open”.
>   初次登陆时，都会弹出一个友情提示，它的意思是要打开的Linux还未在本机登记，问我们是否要信任它。如果是可信任的，则点 ‘是’ 登记该主机，否则点 ‘否’
>   或者 ‘取消’，我们当然要点 ‘是’. 之后弹出登陆提示：
>
>
>     login as: root
>     root@10.72.137.85's password:
>     Last login: Wed May  8 08:02:17 2013 from 10.72.137.89
>
>
>
> 输入用户名以及密码后，就登陆Linux系统了。登陆后会提示最后一次登陆系统的时间以及从哪里登陆。

## 使用密钥认证机制远程登录Linux

SSH服务支持一种安全认证机制，即密钥认证。所谓的密钥认证，实际上是使用一对加密字符串，一个称为公钥(publickey)，
任何人都可以看到其内容，用于加密；另一个称为密钥(privatekey)，只有拥有者才能看到，用于解密。通过公钥加密过的密文使用密钥可以轻松解密，但根据公钥来猜测密钥却十分困难。
ssh的密钥认证就是使用了这一特性。服务器和客户端都各自拥有自己的公钥和密钥。如何使用密钥认证登录linux服务器呢？

1. **下载生成密钥工具**

在本章前面阿铭提供的putty下载地址里，您一定看到了很多可以下载的东西，不过阿铭只让您下载了一个putty.exe.
因为当时只用到这一个工具，其实完整的putty程序包含很多个小工具的，所以这次阿铭建议您直接下载个完整包 [http://the.earth.li/~sgtatham/putty/latest/x86/putty.zip](http://the.earth.li/~sgtatham/putty/latest/x86/putty.zip)
下载后解压，其中puyttygen.exe就是咱们这一小节中所要用到的密钥生成工具。

1. **生成密钥对**

关于密钥的工作原理，如果您感兴趣可以到网上查一查，阿铭不想介绍太多无关知识点，不过，了解一下也没有什么不好。双击puttygen.exe, 右下角
“Number of bits in a generated key” 把 “1024” 改成 “2048”, 然后点 “Generate”,
这样就开始生成密钥了，请来回动一下鼠标，这样才可以快速生成密钥对，大约十几秒后就完成了。 “Key comment:”
这里可以保持不变也可以自定义，其实就是对该密钥的简单介绍； “Kye passphrase:”
这里用来给您的密钥设置密码，这样安全一些，当然也可以留空，阿铭建议您设置一个密码；”Confirm passphrase:”
这里再输入一遍刚刚您设置的密码。

1. **保存私钥**

点 “Save private key”, 选择一个存放路径，定义一个名字，点 “保存”。请保存到一个比较安全的地方，谨防丢掉或被别人看到。

1. **复制公钥到Linux**

回到刚才生成密钥的窗口，在 “Key” 的下方有一段长长的字符串，这一串就是公钥的内容了，把整个公钥字符串复制下来。然后粘贴到您的Linux的 /root/.ssh/authorized_keys
文件里。下面请跟着阿铭一起来做操作：

    [root@localhost ~]# mkdir /root/.ssh
    [root@localhost ~]# chmod 700 /root/.ssh

首先创建/root/.ssh 目录，因为这个目录默认是不存在的，然后是更改权限。 关于 mkdir 和 chmod
两个命令，阿铭会在后续章节详细介绍，暂时您只要知道是用来创建目录和更改权限的就行了。然后是把公钥内容粘贴进 /root/.ssh/authorized_keys
文件。

    [root@localhost ~]# vi /root/.ssh/authorized_keys

回车后，按一下 ‘i’ 进入编辑模式，然后直接点击鼠标右键就粘贴了，这是putty工具非常方便的一个功能。粘贴后，按一下 ‘Esc’ 键，然后输入 :wq 回车保存退出该文件。

1. **关闭Selinux**

如果不关闭selinux, [[3]](#id10)
使用密钥登陆会提示 “Server refused our key”, 关闭方法：

    [root@localhost ~]# setenforce 0

这个只是暂时命令行关闭selinux, 下次重启Linux后selinux还会开启。永久关闭selinux的方法是：

    [root@localhost ~]# vi /etc/selinux/config

回车后，把光标移动到 “SELINUX=enforcing” 按一下 i 键，进入编辑模式，修改为

    SELINUX=disabled

按 “Esc”, 输入 :wq
回车，然后重启系统

1. **设置putty通过密钥登陆**

打开putty.exe点一下您保存好的session，然后点右侧的 “Load”, 在左侧靠下面点一下 “SSH” 前面的 + 然后选择 “Auth”, 看右侧 “Private
key file for authentication:” 下面的长条框里目前为空，点一下 “Browse”,
找到我们刚刚保存好的私钥，点”打开”。此时这个长条框里就有了私钥的地址，当然您也可以自行编辑这个路径。然后再回到左侧，点一下最上面的 “Session”,
在右侧再点一下 “Save”.

1. **使用密钥验证登陆Linux**

保存好后session， 点一下右下方的 “Open”. 出现登陆界面，您会发现和原来的登陆提示内容有所不同了。

    login as: root
    Authenticating with public key "rsa-key-20130509"
    Passphrase for key "rsa-key-20130509":
    Last login: Thu May  9 16:17:13 2013 from 10.72.137.43
    [root@localhost ~]#

现在不再输入root密码，而是需要输入密钥的密码，如果您先前在生产密钥的时候没有设置密码，您输入root后会直接登陆系统。

第六章扩展学习: [http://www.lishiming.net/thread-5401-1-1.md](http://www.lishiming.net/thread-5401-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com)
和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

---
[[1]](#id2)DHCP服务，是自动分配IP的服务，我们平时所在的办公室网络环境里，都有DHCP服务。另外家用的路由器像Tplink 或者 dlink
      都有DHCP服务的功能。[[2]](#id3)这一部分阿铭是在windows XP上配置的，windows7 的配置道理也是一样的。[[3]](#id5)selinux是Redhat、CentOS特有的安全机制，这个东西很复杂，我们从来都不要开启它，因为selinux开启后，会产生诸多莫名其妙的bug.
      在后续章节中，阿铭会详细介绍它的。

&#65533;  [第五章 初步认识Linux](chapter5.md)  ::   [Contents](index.md)  ::   [第七章 Linux文件与目录管理](chapter7.md)  &#65533;

&copy; Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1.
