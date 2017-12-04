# [跟阿铭学linux 2 documentation](index.md)

## 第二十三章 配置TOMCAT

«  [第二十二章 配置Squid服务](chapter22.md)   ::   [Contents](index.md)   ::   [第二十四章 配置Samba服务器](chapter24.md)  »

# 第二十三章 配置Tomcat

目前有很多网站使用jsp的程序编写，所以解析jsp的程序就必须要有相关的软件来完成。Tomcat就是用来解析jsp程序的一个软件，Tomcat是Apache 软件基金会（Apache Software Foundation）的Jakarta项目中的一个核心项目，由Apache、Sun和其他一些公司及个人共同开发而成。因为Tomcat技术先进、性能稳定，而且免费，因而深受Java爱好者的喜爱并得到了部分软件开发商的认可，成为目前比较流行的Web 应用服务器。

Tomcat是一个轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP程序的首选。对于一个初学者来说，可以这样认为，当在一台机器上配置好Apache服务器，可利用它响应对HTML 页面的访问请求。实际上Tomcat 部分是Apache服务器的扩展，但它是独立运行的，所以当你运行tomcat时，它实际上作为一个与Apache 独立的进程单独运行的。

## 安装tomcat

Tomcat的安装分为两个步骤：安装JDK和安装Tomcat.

JDK(Java Development Kit)是Sun Microsystems针对Java开发员的产品。自从Java推出以来，JDK已经成为使用最广泛的Java SDK. JDK是整个Java的核心，包括了Java运行环境，Java工具和Java基础的类库。所以要想运行jsp的程序必须要有JDK的支持，理所当然安装Tomcat的前提是安装好JDK.

**安装JDK**

下载jdk-6u23-linux-i586.bin

    cd /usr/local/src/
    wget http://www.lishiming.net/data/attachment/forum/jdk-6u23-linux-i586.bin

您也可以从官方网站([http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.md](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.md))下载其他版本。

    chmod a+x jdk-6u23-linux-i586.bin
    ./jdk-6u23-linux-i586.bin

它会自动把文件解压出来，到最后会提示 “Press Enter to continue.....”， 只需要按一下回车就可以了。

    mv  jdk1.6.0_23  /usr/local/

**设置环境变量**

    vim/etc/profile

在末尾输入以下内容:

    JAVA_HOME=/usr/local/jdk1.6.0_23/
    JAVA_BIN=/usr/local/jdk1.6.0_23/bin
    JRE_HOME=/usr/local/jdk1.6.0_23/jre
    PATH=$PATH:/usr/local/jdk1.6.0_23/bin:/usr/local/jdk1.6.0_23/jre/bin
    CLASSPATH=/usr/local/jdk1.6.0_23/jre/lib:/usr/local/jdk1.6.0_23/lib:/usr/local/jdk1.6.0_23/jre/lib/charsets.jar
    export  JAVA_HOME  JAVA_BIN JRE_HOME  PATH  CLASSPATH

保存文件后，使其生效:

    source/etc/profile

检测是否设置正确:

    java-version

如果显示如下内容，则配置正确:

    java version "1.6.0_23"
    Java(TM) SE Runtime Environment (build 1.6.0_23-b05)
    Java HotSpot(TM) Client VM (build 19.0-b09, mixed mode, sharing)

**安装Tomcat**

上面介绍了那么多内容，仅仅是在为安装tomcat做准备工作而已，现在才是安装tomcat.

    cd /usr/local/src/
    wget http://www.lishiming.net/data/attachment/forum/apache-tomcat-7.0.14.tar.gz

如果觉得这个版本不适合，可以到官方网站([http://tomcat.apache.org/](http://tomcat.apache.org/))下载。

    tar zxvf apache-tomcat-7.0.14.tar.gz
    mv apache-tomcat-7.0.14 /usr/local/tomcat
    cp -p /usr/local/tomcat/bin/catalina.sh /etc/init.d/tomcat
    vim /etc/init.d/tomcat

在第二行加入以下内容:

    # chkconfig: 112 63 37
    # description: tomcat server init script
    # Source Function Library
    . /etc/init.d/functions

    JAVA_HOME=/usr/local/jdk1.6.0_23/
    CATALINA_HOME=/usr/local/tomcat

保存文件后，执行以下操作:

    chmod 755 /etc/init.d/tomcat
    chkconfig --add tomcat
    chkconfig tomcat on

启动tomcat:

    service tomcat start

查看是否启动成功:

    ps aux |grep tomcat

如果有进程的话，请在浏览器中输入http://IP:8080/ 你会看到tomcat的主界面。

## 配置tomcat

**1. 配置tomcat服务的访问端口**

tomcat默认启动的是8080，如果你想修改为80，则需要修改server.xml文件:

    vim/usr/local/tomcat/conf/server.xml

找到:

    <Connector port="8080" protocol="HTTP/1.1"

修改为:

    <Connector port="80" protocol="HTTP/1.1"

**2. 配置新的虚拟主机**

    cd /usr/local/tomcat/conf/
    vim server.xml

找到</Host>下一行插入新的<Host>内容如下:

    <Host name="www.123.cn" appBase="/data/tomcatweb"
        unpackWARs="false" autoDeploy="true"
        xmlValidation="false" xmlNamespaceAware="false">
        <Context path="" docBase="./" debug="0" reloadable="true" crossContext="true"/>
    </Host>

保存后，重启tomcat:

    service tomcat stop
    service tomcat start

## 测试tomcat

先创建tomcat的测试文件:

    vim /data/tomcatweb/111.jsp

加入如下内容:

    <html><body><center>
        Now time is: <%=new java.util.Date()%>
    </center></body></html>

保存后，使用curl测试:

    [root@localhost ~]# curl -xlocalhost:80 www.123.cn/111.jsp

看看运行结果是否是:

    <html><body><center>
        Now time is: Thu Jun 13 15:26:03 CST 2013
    </center></body></html>

如果是这样的结果，说明tomcat搭建成功。另外，您也可以在您的真机上，绑定hosts, 用IE来测试它。

阿铭建议您最好再扩展学习一下: [http://www.lishiming.net/thread-5447-1-1.md](http://www.lishiming.net/thread-5447-1-1.md)

教程答疑: [请移步这里](http://www.lishiming.net/forum-40-1.md).

欢迎您加入 [阿铭学院](http://www.aminglinux.com/) 和阿铭一起学习Linux，让阿铭成为您Linux生涯中永远的朋友吧！

«  [第二十二章 配置Squid服务](chapter22.md)   ::   [Contents](index.md)   ::   [第二十四章 配置Samba服务器](chapter24.md)  »

© Copyright 2013, lishiming.net. Created using [Sphinx](http://sphinx-doc.org/) 1.2b1.
