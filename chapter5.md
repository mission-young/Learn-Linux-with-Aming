第五章 初步认识Linux

# [跟阿铭学linux 2 documentation](index.md)

## 第五章 初步认识Linux

&#65533;  [第四章 安装Linux操作系统](chapter4.md)  ::   [Contents](index.md)  ::   [第六章Linux系统的远程登陆](chapter6.md)  &#65533;

# 第五章 初步认识Linux

安装完CentOS后，相信您会迫不及待的要登陆进系统里看看这神秘的Linux到底是个什么东西？如果您安装了图形界面，相信您进入系统后会不知所措，总是想点击鼠标的右键去刷新桌面，阿铭最早也是这样的，毕竟用了很久的windows，突然换个操作系统不习惯了。有的朋友也许没有安装图形界面，输入用户名(root)以及密码(123456)后进去，发现更不知所错了。没有关系，随着阿铭逐渐深入的讲解，您会学到越来越多的知识。

## CentOS6是如何启动的

Linux的启动其实和windows的启动过程很类似，不过windows我们是无法看到启动信息的，而Linux启动时我们会看到许多启动信息，例如某个服务是否启动。Linux系统的启动过程大体上可分为五部分：内核的引导、运行init、系统初始化、建立终端、用户登录系统。

1. **内核引导**

当计算机打开电源后，首先是BIOS开机自检，按照BIOS中设置的启动设备（通常是硬盘）来启动。紧接着由启动设备上的grub程序开始引导Linux，当引导程序成功完成引导任务后，Linux从它们手中接管了CPU的控制权，然后CPU就开始执行Linux的核心映象代码，开始了Linux启动过程。也就是所谓的内核引导开始了，在内核引导过程中其实是很复杂的，我们就当它是一个黑匣子，反正是Linux内核做了一些列工作，最后内核调用加载了init程序，至此内核引导的工作就完成了。交给了下一个主角init.

1. **运行init**

init 进程是系统所有进程的起点，您可以把它比拟成系统所有进程的老祖宗，没有这个进程，系统中任何进程都不会启动。init
最主要的功能就是准备软件执行的环境，包括系统的主机名、网络设定、语言、文件系统格式及其他服务的启动等。 而所有的动作都会通过
init的配置文件/etc/inittab来规划，而inittab 内还有一个很重要的设定内容，那就是默认的 runlevel
(开机运行级别)。先来看看运行级别Run level,Linux就是通过设定run
level来规定系统使用不同的服务来启动，让Linux的使用环境不同。我们来看看这个inittab文件里面的支持级别。

    # inittab is only used by upstart for the default runlevel.
    #
    # ADDING OTHER CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
    #
    # System initialization is started by /etc/init/rcS.conf
    #
    # Individual runlevels are started by /etc/init/rc.conf
    #
    # Ctrl-Alt-Delete is handled by /etc/init/control-alt-delete.conf
    #
    # Terminal gettys are handled by /etc/init/tty.conf and /etc/init/serial.conf,
    # with configuration in /etc/sysconfig/init.
    #
    # For information on how to write upstart event handlers, or how
    # upstart works, see init(5), init(8), and initctl(8).
    #
    # Default runlevel. The runlevels used are:
    #   0 - halt (Do NOT set initdefault to this)
    #   1 - Single user mode
    #   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
    #   3 - Full multiuser mode
    #   4 - unused
    #   5 - X11
    #   6 - reboot (Do NOT set initdefault to this)
    #
    id:3:initdefault:

inittab配置文件格式和之前老版本CentOS5或者更老版本比有很大改动。Runlevels共七个级别，0表示关机，1表示单用户，2表示没有网络的命令行级别，3命令行级别（大多服务器都用这个级别），4为保留级别，5为图形化级别，6为重启。这个文件中除了最后一行外，其他都为注释行，也就是说最后一行才是关键，它用来指定服务器跑哪个级别，这里除了可以设置2,3,5外其他级别都不能设置。在该文件的前面部分，可以看到很多行都提及到某个配置文件，而所有配置文件都是在/etc/init/目录下。

1. **系统初始化**

系统初始化，就是去执行/etc/init/下的各个配置文件。inittab配置文件中有这么一行 “System initialization is
started by /etc/init/rcS.conf” 也就是说系统初始化会先执行/etc/init/rcS.conf 而该配置文件中又有一行 “exec
/etc/rc.d/rc.sysinit”
所以，重心又转移到了这个rc.sysinit文件上，它会做如下工作：激活交换分区，检查磁盘，加载硬件模块以及其它一些需要优先执行任务。当rc.sysinit程序执行完毕后，将返回init继续下一步，又到了/etc/init/rc.conf,
在这个配置文件里，最关键的一行为 “exec /etc/rc.d/rc $RUNLEVEL”
而$RUNLEVEL是在/etc/inittab中定义的(最下面的那一行)，以阿铭的/etc/inittab为例，表示$RUNLEVE=3, 所以此时会执行
“/etc/rc.d/rc 3” 此时实际上是把/etc/rc.d/rc3.d/
下的脚本都给执行了，随后/etc/rc.d/rc.local也会被执行，通常我们会把开机启动执行的命令放到这个脚本下。服务执行完，系统初始化也就完成了。接下来该建立终端了。

1. **建立终端**

建立终端是由配置文件/etc/init/tty.conf,
/etc/init/serial.conf和/etc/sysconfig/init等配置文件来完成的。在2、3、4、5的运行级别中都将以respawn方式运行mingetty程序，mingetty程序能打开终端、设置模式。同时它会显示一个文本登录界面，这个界面就是我们经常看到的登录界面，在这个登录界面中会提示用户输入用户名，而用户输入的用户将作为参数传给login程序来验证用户身份。

1. **用户登陆系统**

对于运行级别为5的图形方式用户来说，他们的登录是通过一个图形化的登录界面。登录成功后可以直接进入KDE、Gnome等窗口管理器。而本文主要讲的还是文本方式登录的情况：当我们看到mingetty的登录界面时，我们就可以输入用户名和密码来登录系统了。

Linux的账号验证程序是login，login会接收mingetty传来的用户名作为用户名参数。然后login会对用户名进行分析：如果用户名不是root，且存在
“/etc/nologin” 文件，login将输出nologin文件的内容，然后退出。这通常用来系统维护时防止非root用户登录。只有
“/etc/securetty” 中登记了的终端才允许root用户登录，如果不存在这个文件，则root可以在任何终端上登录。”/etc/usertty”
文件用于对用户作出附加访问限制，如果不存在这个文件，则没有其他限制。

在分析完用户名后，login将搜索 “/etc/passwd” 以及 “/etc/shadow”
来验证密码以及设置账户的其它信息，比如：主目录是什么、使用何种shell。如果没有指定主目录，将默认为根目录；如果没有指定shell，将默认为
“/bin/bash”。

login程序成功后，会向对应的终端在输出最近一次登录的信息(在 “/var/log/lastlog” 中有记录)，并检查用户是否有新邮件(在
“/usr/spool/mail/” 的对应用户名目录下)。然后开始设置各种环境变量：对于bash来说，系统首先寻找 “/etc/profile”
脚本文件，并执行它；然后如果用户的主目录中存在 .bash_profile
文件，就执行它，在这些文件中又可能调用了其它配置文件，所有的配置文件执行后后，各种环境变量也设好了，这时会出现大家熟悉的命令行提示符，到此整个启动过程就结束了。

## 图形界面与命令行界面切换

Linux预设提供了六个命令窗口终端机让我们来登录。默认我们登录的就是第一个窗口，也就是tty1，这个六个窗口分别为tty1,tty2 …
tty6，您可以按下Ctrl + Alt + F1 ~ F6 来切换它们。如果您安装了图形界面，默认情况下是进入图形界面的，此时您就可以按Ctrl + Alt
+ F1 ~ F6来进入其中一个命令窗口界面。当您进入命令窗口界面后再返回图形界面只要按下Ctrl + Alt + F7 就回来了。如果您用的vmware
虚拟机，命令窗口切换的快捷键为 Alt + Space + F1~F6. 如果您在图形界面下请按Alt + Shift + Ctrl + F1~F6
切换至命令窗口。

## 学会使用快捷键

1. Ctrl + C：这个是用来终止当前命令的快捷键，当然您也可以输入一大串字符，不想让它运行直接Ctrl + C，光标就会跳入下一行。

2. Tab：
  这个键是最有用的键了，也是阿铭敲击概率最高的一个键。因为当您打一个命令打一半时，它会帮您补全的。不光是命令，当您打一个目录时，同样可以补全，不信您试试。

3. Ctrl + D： 退出当前终端，同样您也可以输入exit。

4. Ctrl + Z： 暂停当前进程，比如您正运行一个命令，突然觉得有点问题想暂停一下，就可以使用这个快捷键。暂停后，可以使用fg 恢复它。

5. Ctrl + L： 清屏，使光标移动到第一行。

## 学会查询帮助文档 — man

这个man 通常是用来看一个命令的帮助文档的。格式为 ” man 命令 ” 例如输入命令：

    man ls

则会显示如下结果：

    LS(1)                            User Commands                           LS(1)

    NAME
        ls - list directory contents

    SYNOPSIS
        ls [OPTION]... [FILE]...

    DESCRIPTION
        List  information  about  the FILEs (the current directory by default).
        Sort entries alphabetically if none of -cftuvSUX nor --sort.

        Mandatory arguments to long options are  mandatory  for  short  options
        too.

        -a, --all
               do not ignore entries starting with .

        -A, --almost-all
               do not list implied . and ..

        --author
               with -l, print the author of each file

这样可以查看 “ls” 这个命令的帮助文档，进入后按 ‘q’ 键退出。

## Linux 系统目录结构

登录系统后，在当前命令窗口下输入:

    ls /

您会看到

    [root@localhost ~]# ls /
    bin   dev  home  lost+found  mnt  proc  sbin     srv  tmp  var
    boot  etc  lib   media       opt  root  selinux  sys  usr

在讲目录结构之前，阿铭先介绍一下这个 “ls” 命令是干什么的， “ls” 其实就是英文单词 ‘list’
的缩写，它的作用是列出指定目录或者文件，刚刚在上一节中提到的 “man ls” 可以查看其具体的使用方法。对于 “ls”
这个最常用的命令，阿铭举几个简单例子让您快速掌握。

Example:

    [root@localhost ~]# ls
    anaconda-ks.cfg  install.log  install.log.syslog

    [root@localhost ~]# ls -a
    .   anaconda-ks.cfg  .bash_profile  .cshrc       install.log.syslog
    ..  .bash_logout     .bashrc        install.log  .tcshrc

    [root@localhost ~]# ls -l
    总用量 36
    -rw-------. 1 root root   980 5月   7 18:00 anaconda-ks.cfg
    -rw-r--r--. 1 root root 19305 5月   7 18:00 install.log
    -rw-r--r--. 1 root root  5890 5月   7 17:58 install.log.syslog

    [root@localhost ~]# ls install.log
    install.log

    [root@localhost ~]# ls /var/
    account  crash  empty  lib    lock  mail  opt       run    tmp
    cache    db     games  local  log   nis   preserve  spool  yp

**讲解**

1. 不加任何选项也不跟目录 [[1]](#id11) 名或者文件名


> 会列出当前目录下的文件和目录，不包含隐藏文件。 [[2]](#id12)

1. 加 “-a” 选项不加目录名或者文件名

> 会列出当前目录下所有文件和目录，含有隐藏文件。

1. 加 “-l” 选项不加目录名或者文件名

> 会列出当前目录下除隐藏文件外的所有文件和目录的详细信息，包含其权限、所属主、所属组、以及文件创建日期和时间。

1. 后面不加选项只跟文件名

> 会列出该文件，其实这样没有什么意思，通常都是加上一个 “-l” 选项，用来查看该文件的详细信息。

1. 后面不加选项只跟目录名

> 会列出指定目录下的文件和目录

好了，关于”ls”
阿铭就讲这几个例子，当然它的可用选项还有很多哦，不过阿铭只给介绍最常用的。因为阿铭不想一股脑灌输给您太多知识点，那样没有什么用，但您也不用担心学不全，阿铭讲的知识点足够您日常工作和学习中用的了。如果实在是遇到不懂的选项，直接用
“man” 来查帮助文档吧。下面咱们回到先前的话题，接着讨论Linux的目录结构。

- /bin bin是Binary的缩写。这个目录存放着最经常使用的命令。

- /boot这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。

- /dev dev是Device(设备)的缩写。该目录下存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。

- /etc这个目录用来存放所有的系统管理所需要的配置文件和子目录。

- /home用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

- /lib这个目录里存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库。

- /lost+found这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

- /media Linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux会把识别的设备挂载到这个目录下。

- /mnt系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。

- /opt 这是给主机额外安装软件所摆放的目录。比如您安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

- /proc这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。这个目录的内容不在硬盘上而是在内存里，我们也可以直接修改里面的某些文件，比如可以通过下面的命令来屏蔽主机的ping命令，使别人无法ping您的机器：


>     echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all

- /root该目录为系统管理员，也称作超级权限者的用户主目录。

- /sbin s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。

- /selinux
  这个目录是Redhat/CentOS所特有的目录，Selinux是一个安全机制，类似于windows的防火墙，但是这套机制比较复杂，这个目录就是存放selinux相关的文件的。


- /srv 该目录存放一些服务启动之后需要提取的数据。

- /sys 这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs
  ，sysfs文件系统集成了下面3种文件系统的信息：针对进程信息的proc文件系统、针对设备的devfs文件系统以及针对伪终端的devpts文件系统。该文件系统是内核设备树的一个直观反映。当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统种被创建。


- /tmp这个目录是用来存放一些临时文件的。

- /usr 这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似与windows下的program files目录。

- /usr/bin 系统用户使用的应用程序。

- /usr/sbin 超级用户使用的比较高级的管理程序和系统守护程序。

- /usr/src 内核源代码默认的放置目录。

- /var这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。

在Linux系统中，有几个目录是比较重要的，平时需要注意不要误删除或者随意更改内部文件。/etc：
上边也提到了，这个是系统中的配置文件，如果您更改了该目录下的某个文件可能会导致系统不能启动。/bin, /sbin, /usr/bin, /usr/sbin:
这是系统预设的执行文件的放置目录，比如 ls 就是在/bin/ 目录下的。值得提出的是，/bin, /usr/bin
是给系统用户使用的指令（除root外的通用账户），而/sbin, /usr/sbin 则是给root使用的指令。 /var：
这是一个非常重要的目录，系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在/var/log
目录下，另外mail的预设放置也是在这里。

## 如何正确关机、重启

其实，在Linux领域内大多用在服务器上，很少遇到关机的操作。毕竟服务器上跑一个服务是永无止境的，除非特殊情况下，不得已才会关机。

Linux和windows不同，在 Linux
底下，由于每个程序（或者说是服务）都是在在背景下执行的，因此，在您看不到的屏幕背后其实可能有相当多人同时在您的主机上面工作，例如浏览网页啦、传送信件啦以 FTP
传送档案啦等等的，如果您直接按下电源开关来关机时，则其它人的数据可能就此中断！那可就伤脑筋了！此外，最大的问题是，若不正常关机，则可能造成文件系统的毁损（因为来不及将数据回写到档案中，所以有些服务的档案会有问题！）。

如果您要关机，必须要保证当前系统中没有其他用户在线。可以下达 who 这个指令，而如果要看网络的联机状态，可以下达 netstat -a
这个指令，而要看背景执行的程序可以执行 ps -aux
这个指令。使用这些指令可以让您稍微了解主机目前的使用状态！（这些命令在以后的章节中会提及，现在只要了解即可！）

正确的关机流程为：sync –> shutdown –> reboot –> halt

- sync 将数据由内存同步到硬盘中。

- shutdown 关机指令，您可以man shutdown 来看一下帮助文档。例如您可以运行如下命令关机：

- shutdown -h 10 ‘ 这个命令告诉大家，计算机将在10分钟后关机，并且会显示在登陆用户的当前屏幕中。

- shutdown -h now 立马关机

- shutdown -h 20:25 系统会在今天20:25关机

- shutdown -h +10 十分钟后关机

- shutdown -r now 系统立马重启

- shutdown -r +10 系统十分钟后重启

- reboot 就是重启，等同于 shutdown -r now

- halt 关闭系统，等同于shutdown -h now 和 poweroff

最后总结一下，不管是重启系统还是关闭系统，首先要运行sync命令，把内存中的数据写到磁盘中。关机的命令有 shutdown -h now, halt,
poweroff 和 init 0 , 重启系统的命令有 shutdown -r now, reboot, init 6.

## 忘记root密码怎么办

以前阿铭忘记windows的管理员密码，由于不会用光盘清除密码最后只能重新安装系统。现在想想那是多么愚笨的一件事情。同样Linux系统您也会遇到忘记root密码的情况，如果遇到这样的情况怎么办呢？重新安装系统吗？当然不用！进入单用户模式更改一下root密码即可。如何进入呢。

1. **重启系统**

3秒钟内，按一下回车键。此时您会看到如下提示信息：

    GNU GRUB version 0.97 .......
    CentOS (2.6.32-358.el6.i686)

阿铭没有写完全提示信息，相信您肯定能进入到这一界面。此时CentOS (2.6.32-358.el6.i686)
这一行是高亮的，即我们选中的就是这一行，这行的意思是Linux版本为CentOS，后面小括号内是内核版本信息。 另外在这个界面里，我们还可以获取一些信息，输入
‘e’ 会在启动前编辑命令行； 输入 ‘a’ 会在启动前更改内核的一些参数； 输入 ‘c’ 则会进入命令行。而我们要做的是输入 ‘e’.

1. **进入单用户模式**

输入 ‘e’ 后，界面变了，显示如下信息：

    root (hd0,0)
    kernel /vmlinuxz-2.6.32-358.el6.i686 ro root=UUID=......（此处省略）
    initrd /initramfs-2.6.32-358.el6.i686.img

暂时您不用管这些都代表什么意思，您只要跟着阿铭做即可。按一下向下的箭头键，选中第二行，输入 ‘e’，出现如下提示：

    <_NO_DM rhgh quiet

您只需要在后面加一个 “single” 或者 “1” 或者 “s”

    <_NO_DM rhgh quiet single

然后先按回车然后按 ‘b’，启动后就进入单用户模式。

1. **修改root密码**

输入修改root密码的命令 ‘passwd’：

    [root@localhost /]# passwd
    Changeing password for user root.
    New password:
    Retry new password:
    passwd: all authentication tokens updated successfully.

修改后，重启系统

    [root@localhost /]# reboot

## 使用系统安装盘的救援模式

救援模式即rescue ，这个模式主要是应用于，系统无法进入的情况。如，grub损坏或者某一个配置文件修改出错。如何使用rescue模式呢？

1. **光盘启动**

阿铭做实验用的是vmware虚拟机，设置光盘启动的步骤也许和您的不一样，但道理是一样的，相信聪明的您一定不会在这里出问题。开机启动，按F2进入bios设置，按方向键选择
“Boot” 那一项，然后使用上下方向键和+/-号来移动，最终让CD-ROM Drive 挪动到最上面一行。最后按F10, 再按回车进入光盘启动界面。

1. **进入rescue模式**

光盘启动后，使用上下方向键选择 ‘Rescue installed system’ 回车

- 语言我们默认，直接回车

- 键盘类型，也默认，直接回车

- Rescue Method 也保持默认，因为我们使用的就是光驱里的光盘，回车

- 这一步问我吗是否在使用rescue模式的时候启用网络，这个根据实际情况，在这里阿铭选择NO(使用tab键) 回车

- 接下来这一步，提示我们Rescue 环境将会找到我们已经安装的Linux系统，并将其挂载到/mnt/sysimage 下，这一步阿铭将会选择
  ‘Continue’ 然后回车

- 回车后，将会看到一个小提示框，它告诉我们Linux系统挂载到了 “/mnt/sysimage” . 如果想获得root 环境，需要执行命令
  “chroot /mnt/sysimage” 继续回车

- 继续回车

- 此时又出现一个框，有三种模式可以选择：shell 模式会直接进入命令行，可以进行的操作有编辑文件、修改用户密码等； fakd 是诊断模式；
  reboot 会直接重启； 这一步阿铭选择第一个shell模式，然后回车

1. **进入root环境**

此时还不能操作Linux系统上的文件，因为目前还在光盘上的系统上，您有没有使用过windows
PE系统？其实目前我们所在的环境就类似于windows上的PE系统。要想修改原来Linux系统上的文件还需要执行一个命令：

    bash-4.1# chroot /mnt/sysimagesh-4.1#

您会发现命令行前后有一处变化：原来的 “bash-4.1” 变成了 “sh-4.1”,
此时我们才可以像在原来的Linux系统上做一些操作，比如更改root密码或者修改某个文件等等。

本章内容阿铭讲的有点罗嗦，另外建议您再扩展学习一下吧([http://www.lishiming.net/thread-5396-1-1.md](http://www.lishiming.net/thread-5396-1-1.md)).

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com)
和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

---
[[1]](#id5)Linux下的目录其实就是windows里的文件夹，但通常我们都叫做目录，而不叫文件夹，希望您也要改一下这个习惯。[[2]](#id6)Linux下的隐藏文件是通过文件名来控制的，所有的隐藏文件的文件名都是以 . 开头的，比如 .1.txt
      这样1.txt就是隐藏文件了。

&#65533;  [第四章 安装Linux操作系统](chapter4.md)
  ::   [Contents](index.md)
  ::   [第六章
Linux系统的远程登陆](chapter6.md)  &#65533;

&copy; Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1. 
