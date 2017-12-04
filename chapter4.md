第四章 安装Linux操作系统

# [跟阿铭学linux 2 documentation](index.md)

## 第四章 安装Linux操作系统

&#65533;  [第三章 对Linux系统管理员的建议](chapter3.md)  ::   [Contents](index.md)  ::   [第五章 初步认识Linux](chapter5.md)  &#65533;

# 第四章 安装Linux操作系统

目前我们安装Linux操作系统是为了更好的了解和学习Linux操作系统。如果您有条件，最好是把Linux安装在一台真正的计算机上，如果您的电脑不允许您安装成Linux，那也没有关系。阿铭教您使用虚拟机来安装Linux操作系统。

## 安装虚拟机

当前流行的虚拟机有好多种，阿铭用的是vmware虚拟机。目前最新版本已经到版本v9.0了，但阿铭一直在使用v6.0.1,
如果您不想去下载最新版，那直接使用阿铭提供的链接下载v6.0.1吧，下载地址（含注册机）：[点这里](http://pan.baidu.com/share/link?shareid=591457&amp;uk=235144927)
下载后安装一下vmware6.0.1, 安装完后需要输入注册码才可以使用，阿铭提供的注册机很好用，直接生成一个即可。

如果您觉得vmware不好用，您也可以选择VirtualBox，它是完全免费的，[官方下载地址](https://www.virtualbox.org/wiki/Downloads) 另外也阿铭提供一个下载地址: [这里](http://pan.baidu.com/share/link?shareid=3356065328&amp;uk=235144927)

## 下载Linux操作系统镜像文件

安装虚拟机的好处就是，可以直接把镜像文件当成光盘使用。阿铭在前面提到，在后续章节中做的所有实验都是依据CentOS来做的，所以您也需要下载一个32位的
[[1]](#id13) CentOS的镜像。下载地址：[http://mirrors.163.com/centos/6.5/isos/i386/CentOS-6.5-i386-bin-DVD1.iso](http://mirrors.163.com/centos/6.5/isos/i386/CentOS-6.5-i386-bin-DVD1.iso)。

## 安装CentOS (图形化安装）

前面的准备工作可能需要很久，因为CentOS的镜像文件太大了。下载好后，就可以跟着阿铭一起安装CentOS了。在这一版教程里，阿铭不再插入图片了，因为在后面阿铭会录制视频来帮助大家更好的学习，这也许会对您的学习造成一点点障碍，但阿铭尽可能的讲清楚给大家，如果您实在不明白就请加一下QQ群（148412746），到群里咨询阿铭吧。阿铭先介绍如何图形化安装CentOS，然后介绍以文本模式的方式安装CentOS.

1. **创建安装Linux的虚拟机**

打开vmware workstation软件，在右侧的Home标签中点 “New Virtual Machine” –> 点 “下一步” –>
选择Custom 继续点 “下一步” –> 继续点 “下一步” –> 选择 “Linux” –> 点 “下一步” –> Location
这里选择一个空间大的盘符，比如阿铭选择 “E:CentOS” 点 “下一步” –>Processors 数量选择One, 继续点 “下一步” –>
内存建议修改为1024M或者更大，前提是您的真机内存足够大(不能太小，否则图形安装界面出不来）然后点 “下一步” –> Network
Connection 保持默认，点 “下一步” –>继续点 “下一步” –> 继续 “下一步” –> Vitual disk type 选择
“IDE”, 点 “下一步” –> Disk size 改为16.0 （如果您的计算机磁盘空间太小，那就改小一点，但不要低于5G）点 “下一步”–>
最后点 “完成”

1. **配置虚拟光驱**

此时还不能安装CentOS，还需要配置一下刚刚建立的虚拟机。点 “Edit virtual machine settings” –>
选择Floppy那一项，点下面的 “Remove”, 因为用不到软盘了，所以直接删除掉; CD-ROM 这一项是关键，需要在 “Use ISO image:”
前面点一下，然后点Browse, 选择您刚刚下载的CentOS镜像文件，最后点ok.

1. **开始安装CentOS**

点 “Start this vitrual machine” –>
鼠标点一下虚拟机的界面,进入虚拟机，否则不能进行后续操作（要想退出，需要同时按Alt + Ctrl），然后选择 “Install system with
basic video driver” 回车 –> Disk Found
这是问您是否需要检测镜像文件，因为是从官方站点下载的镜像，肯定没有问题，所以我们选择 “skip” –> 点 “Next” –>
这一步是让我们选择在接下来的安装过程中需要使用的语言，我们选择 “chinese(simplified)(中文（简体）)”，点 “Next”
(到这里如果您发现，和阿铭的不一样，那肯定是您的虚拟机不支持图形显示，那么请直接跳到下一小节参考如何文本模式安装吧) –>
键盘这一项我们保持默认，即美国英式键盘, 点 “下一步” –> 使用的设备保持默认，点 “下一步” –> 如果弹出一个窗口提示
“处理驱动器时出错”，则需要我们选择 “重新初始化(R)”, 然后点 “下一步” –> 看一下时区是不是 “亚洲/上海”，如果是则点
“下一步”，否则就选择 “亚洲/上海”，点 “下一步” –> 输入根密码（root账户的密码），
您一定要记住哦，否则您进不了系统，反正是做实验，阿铭建议您输入123456, 点 “下一步” –> 接下来会问我们
“进行哪种类型的安装”，我们选择最后一项 “创建自定义布局”，即我们自定义分区，点 “下一步”

1. **分区**

此时就该分区 [[2]](#id14)
了，阿铭的虚拟机总共有一块16G的磁盘，我会这样划分：(1) /boot分区分100M (2) swap分区分2G (3) 剩余划分给
/分区。下面看看阿铭如何操作吧，先点 “创建”, 然后选择 “标准分区”, 再点 “生成”, 挂载点选/boot, 大小改为100, 最后点
“确定”，这样就创建成功了一个/boot分区 –>
依此类推，创建swap分区，创建时先不用选择挂载点，直接选择文件系统类型为swap，然后大小改为2048, 最后点 “确定” –> 最后创建一个/
分区，挂载点选择 /, 大小不管，其他大小选项选择 “使用全部可用空间”, 最后点 “确定”，这样就按照阿铭先前构想的方案创建好分区了。然后点 “下一步”。
此时弹出警告，格式化会删除掉磁盘上的所有数据，我们选择 “格式化”, 然后又一次弹出警告，点击 “将修改写入磁盘(W)”

1. **选择安装包**

格式化完磁盘后，出现引导装载程序的选项，保持默认点击 “下一步” –> 目前的安装方案会最小化安装CentOS,
为了让大家更能详细了解安装过程，所以阿铭带大家选择 “现在自定义”，点 “下一步” –> “基本系统”这一项，在右侧选择 “基本”，其他不用勾选
–> web服务、开发、弹性存储、数据库、服务器、系统管理、虚拟化、负载平衡器、高可用这几项一个都不用选 –>
桌面这一项阿铭也不会选择任何一项，但如果您对图形界面好奇，或者想用一用Linux的图形界面，那您就把右侧的几个项全部勾选了吧 –> 应用程序这一项把
“Tex支持”勾选上 –> 语言支持这一项默认就已经把 “中文支持” 勾选上了，所以我们也不用修改它，接着点
“下一步”，接下来就是漫长的等待安装系统了。安装时间的长短取决您勾选的包的多少以及您的电脑的性能，阿铭的虚拟机安装了8分钟就搞定了。等安装完所有的包后会提示我们，需要
“重新引导”，重启后登陆root账户，进入CentOS系统。

## 安装CentOS (文本模式安装）

首先是安装虚拟机、配置虚拟机，步骤和上一小节的一样，不同的是光驱启动，进入安装界面后，我们不再选择 “Install system with basic
video driver” 而是选择第一行 “Install or upgrade an existing system”. 回车后出现 Disk
Found提示，问我们是否要检测镜像文件，我们选择 “skip”.

- 等会弹出欢迎页： Welcome to CentOS! 直接回车。

- 选择语言，因为使用的是文本模式，选择中文也没有用，所以直接回车。

- 键盘也保持默认，直接回车。

- 此时出现警告，提示说磁盘设备可能需要重新初始化，选择 “Re-initialize”.

- Time Zone Selection 这一项是时区设置选项，System clock uses UTC 前面的 *
  要去掉（按空格即可以去掉），因为中国的时区是CST，然后按 “Tab” 键光标移动到下方，按向下箭头找到 “Asia/Shanghai”, 然后按
  “Tab” 选择OK 回车。

- Root Password 这一栏是给root用户设置密码。阿铭建议您输入一个简单的容易记住的，不然等会忘记是什么就麻烦了。比如阿铭设置的密码是
  ‘123456’, 输入完后按 “Tab” 再次输入一遍，然后按 “Tab” 选中OK回车。

- 此时会提示，说密码太弱了，让我们设置一个复杂的，这也是出于安全考虑友善提示用户的，不过咱们是做实验，无所谓了。所以选择 “Use Anyway”
  回车。

- 弹出 “Partition Type” (分区类型) 窗口， 分为三种模式：Use entire drive 意思是说使用全部的磁盘；
  Replace existing Linux system 意思是替换现有的Linux系统； Use free space 意思是使用剩余空间。
  由于我们是全新安装，所以无所谓选择哪一项。 阿铭选择的是第一项 “Use entire drive”.
  下面的一行是让我们选择哪个磁盘，如果您的机器上有多块磁盘，这里会显示多行，阿铭的虚拟机只有一个磁盘，所以使用 “Tab” 选择Ok回车。

- 弹出提示说，将要把存储配置写入磁盘里。我们选择 “Write changes to disk” 回车。

- 剩下的就是自动安装了，大概3分钟左右就完成了安装。

也许您会问，那分区、自定义软件包的步骤跑哪里去了？是呀，阿铭一开始也有疑问，当我查了好久资料后，才知道，原来是官方为了某个bug而屏蔽了自定义分区和自定义安装包的功能。所以，想自定义分区的朋友，只能通过图形化安装喽！

第四章扩展学习: [http://www.lishiming.net/thread-5386-1-1.md](http://www.lishiming.net/thread-5386-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com)
和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

---
[[1]](#id7)讲到这里，阿铭有一个小知识点需要向您分享一下，首先要声明这个知识点是需要您掌握的。Linux操作系统或者windows、apple
      等操作系统都有两种版本: i386 和 X86_64,
      这两种版本的区别在于，前者是32位后者是64位。不知道您有没有遇到过这样的问题，32位windows操作系统内存如果安装4G或者更高它是不识别的，只认到3G多，而64位操作系统没有这个影响。同样的，Linux操作系统也是有这样的问题，阿铭提供的下载地址是32位的，因为我们是在做实验，分配1G内存足够了。如果您要是在大于4G的计算机上安装Linux操作系统，那么就需要安装64位的操作系统。[[2]](#id9)关于Linux的分区，往往Linux系统管理员都有些讲究，肯定不能像阿铭在虚拟机上这样简单分区。以后的您也许会做系统管理员，所以如何分区是需要您掌握的。阿铭就给大家一个万能的分区方案吧：(1)
      /boot分区给100M; (2)
      swap分区大小为内存的2倍，如果您的机器内存非常大（大于8G时）给16G就够了，给多了也是浪费磁盘资源，8G或者8G以下就给内存大小的2倍；(3)/usr/分区20G;
      (4) / 分区给20G;
(5)剩下的给/data/分区或者随便您自定义一个挂载点。

&#65533;  [第三章 对Linux系统管理员的建议](chapter3.md)
  ::   [Contents](index.md)
  ::   [第五章
初步认识Linux](chapter5.md)  &#65533;

&copy; Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1.