第十一章 文档的压缩与打包

# [跟阿铭学linux 2 documentation](index.md)

## 第十一章 文档的压缩与打包

&#65533;  [第十章 文本编辑工具vim](chapter10.md)
  ::   [Contents](index.md)
  ::   [第十二章
安装RPM包或者安装源码包](chapter12.md)  &#65533;

# 第十一章 文档的压缩与打包

在windows下我们接触最多的压缩文件就是.rar格式的了。但在linux下这样的格式是不能识别的，它有自己所特有的压缩工具。但有一种文件在windows和linux下都能使用那就是.zip格式的文件了。压缩的好处不用阿铭介绍相信您也晓得吧，它不仅能节省磁盘空间而且在传输的时候还能节省网络带宽呢。

在linux下最常见的压缩文件通常都是以.tar.gz 为结尾的，除此之外还有.tar, .gz, .bz2,
.zip等等。以前也介绍过linux系统中的后缀名其实要不要无所谓，但是对于压缩文件来讲必须要带上。这是为了判断压缩文件是由哪种压缩工具所压缩，而后才能去正确的解压缩这个文件。以下介绍常见的后缀名所对应的压缩工具。

.gz gzip 压缩工具压缩的文件

.bz2 bzip2 压缩工具压缩的文件

.tar tar 打包程序打包的文件(tar并没有压缩功能，只是把一个目录合并成一个文件)

.tar.gz 可以理解为先用tar打包，然后再gzip压缩

.tar.bz2 同上，先用tar打包，然后再bzip2压缩

## gzip压缩工具

语法： gzip[-d#]filename其中#为1-9的数字

“-d” : 解压缩时使用

“-#” : 压缩等级，1压缩最差，9压缩最好，6为默认

    [root@localhost ~]# [ -d test ] && rm -rf test
    [root@localhost ~]# mkdir test
    [root@localhost ~]# mv test.txt test
    [root@localhost ~]# cd test
    [root@localhost test]# ls
    test.txt
    [root@localhost test]# gzip test.txt
    [root@localhost test]# ls
    test.txt.gz

您对第一条命令也许很陌生，其实这是两条命令，[ ] 内是一个判断，”-d test” 判断test目录是否存在，’&&’
为一个连接命令符号，当前面的命令执行成功后，后面的命令才执行。关于这两块内容阿铭会在后面详细讲解。gzip
后面直接跟文件名，就在当前目录下把该文件压缩了，而原文件也会消失。

    [root@localhost test]# gzip -d test.txt.gz
    [root@localhost test]# ls
    test.txt

“gzip -d” 后面跟压缩文件，会解压压缩文件。gzip 是不支持压缩目录的。

    [root@localhost ~]# gzip test
    gzip: test is a directory -- ignored
    [root@localhost ~]# ls test
    test/  test1  test2/ test3
    [root@localhost ~]# ls test
    test.txt

至于 “-#” 选项，平时很少用，使用默认压缩级别足够了。

## bzip2压缩工具

语法： bzip2[-dz]filename

bzip2 只有两个选项需要您掌握。

“-d” : 解压缩

“-z” : 压缩

压缩时，可以加 “-z” 也可以不加，都可以压缩文件，”-d” 则为解压的选项：

    [root@localhost ~]# cd test
    [root@localhost test]# bzip2 test.txt
    [root@localhost test]# ls
    test.txt.bz2
    [root@localhost test]# bzip2 -d test.txt.bz2
    [root@localhost test]# bzip2 -z test.txt
    [root@localhost test]# ls
    test.txt.bz2

bzip2 同样也不可以压缩目录：

    [root@localhost test]# cd ..
    [root@localhost ~]# bzip2 test
    bzip2: Input file test is a directory.

## tar压缩工具

tar 本身为一个打包工具，可以把目录打包成一个文件，它的好处是它把所有文件整合成一个大文件整体，方便拷贝或者移动。

语法：tar[-zjxcvfpP]filename tar
命令有多个选项，其中不常用的阿铭做了标注。

“-z” : 同时用gzip压缩

“-j” : 同时用bzip2压缩

“-x” : 解包或者解压缩

“-t” : 查看tar包里面的文件

“-c” : 建立一个tar包或者压缩文件包

“-v” : 可视化

“-f” : 后面跟文件名，压缩时跟 “-f 文件名”，意思是压缩后的文件名为filename, 解压时跟 “-f 文件名”，意思是解压filename.
请注意，如果是多个参数组合的情况下带有 “-f”，请把 “-f” 写到最后面。

“-p” : 使用原文件的属性，压缩前什么属性压缩后还什么属性。（不常用）

“-P” : 可以使用绝对路径。（不常用）

--excludefilename : 在打包或者压缩时，不要将filename文件包括在内。（不常用）

    [root@localhost ~]# cd test
    [root@localhost test]# mkdir test111
    [root@localhost test]# touch test111/test2.txt
    [root@localhost test]# echo "nihao" > !$
    echo "nihao" > test111/test2.txt
    [root@localhost test]# ls
    test111  test.txt.bz2
    [root@localhost test]# tar -cvf test111.tar test111
    test111/
    test111/test2.txt
    [root@localhost test]# ls
    test111  test111.tar  test.txt.bz2

首先在test目录下建立test111目录，然后在test111目录下建立test2.txt, 并写入 “nihao”
到test2.txt中，接着是用tar把test111打包成test111.tar. 请记住 “-f” 参数后跟的是打包后的文件名,
然后再是要打包的目录或者文件。tar 打包后，原文件不会消失，而依旧存在。在上例中，阿铭使用一个特殊的符号 ”!$”
它表示上一条命令的最后一个参数，比如在本例中，它表示”test111/test2.txt”. tar 不仅可以打包目录也可以打包文件，打包的时候也可以不加
“-v” 选项表示，表示不可视化。

    [root@localhost test]# rm -f test111.tar
    [root@localhost test]# tar -cf test.tar test111 test.txt.bz2
    [root@localhost test]# ls
    test111  test.tar  test.txt.bz2

删除原来的test111目录，然后解包test.tar，不管是打包还是解包，原来的文件是不会删除的，而且它会覆盖当前已经存在的文件或者目录。

    [root@localhost test]# rm -rf test111
    [root@localhost test]# ls
    test.tar  test.txt.bz2
    [root@localhost test]# tar -xvf test.tar
    test111/
    test111/test2.txt
    test.txt.bz2

**打包的同时使用gzip压缩**

tar命令非常好用的一个功能就是可以在打包的时候直接压缩，它支持gzip压缩和bzip2压缩。

    [root@localhost test]# tar -czvf  test111.tar.gz test111
    test111/
    test111/test2.txt
    [root@localhost test]# ls
    test111  test111.tar.gz   test.tar  test.txt.bz2

“-tf” 可以查看包或者压缩包的文件列表：

    [root@localhost test]# tar -tf test111.tar.gz
    test111/
    test111/test2.txt
    [root@localhost test]# tar -tf test.tar
    test111/
    test111/test2.txt
    test.txt.bz2

“-zxvf” 用来解压.tar.gz的压缩包

    [root@localhost test]# rm -rf test111
    [root@localhost test]# ls
    test111.tar.gz  test.tar  test.txt.bz2
    [root@localhost test]# tar -zxvf test111.tar.gz
    test111/
    test111/test2.txt
    [root@localhost test]# ls
    test111  test111.tar.gz  test.tar  test.txt.bz2

**打包的同时使用bzip2压缩**

和gzip压缩不同的是，这里使用 “-cjvf” 选项来压缩

    [root@localhost test]# tar -cjvf test111.tar.bz2 test111
    test111/
    test111/test2.txt
    [root@localhost test]# ls
    test111  test111.tar.bz2  test111.tar.gz  test.tar  test.txt.bz2

同样可以使用 “-tf” 来查看压缩包文件列表

    [root@localhost test]# tar -tf test111.tar.bz2
    test111/
    test111/test2.txt

解压.tar.bz2 的压缩包也很简单

    [root@localhost test]# tar -jxvf test111.tar.bz2
    test111/
    test111/test2.txt

下面介绍一下 --exclude
这个选项的使用，因为在日常的管理工作中您也许会用到它。

    [root@localhost test]# tar -cvf test111.tar --exclude test3.txt test111
    test111/
    test111/test4.txt
    test111/test2.txt
    test111/test5/

请注意上条命令中，test111.tar 是放到了 --exclude 选项的前面。除了可以排除文件，也可以排除目录：

    [root@localhost test]# rm -f test111.tar
    [root@localhost test]# tar -cvf test111.tar --exclude test5 test111
    test111/
    test111/test4.txt
    test111/test3.txt
    test111/test2.txt

阿铭建议您最好再扩展学习一下: [http://www.lishiming.net/thread-5435-1-1.md](http://www.lishiming.net/thread-5435-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com)
和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

&#65533;  [第十章 文本编辑工具vim](chapter10.md)
  ::   [Contents](index.md)
  ::   [第十二章
安装RPM包或者安装源码包](chapter12.md)  &#65533;

&copy; Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1.
