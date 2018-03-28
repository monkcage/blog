---
layout: post
title: Docker + gerrit + jenkins CI环境搭建
category: CI
tags: [CI,gerrit,jenkins,docker]
---


## Docker环境搭建
    由于Docker对windows支持不是特别好，所以选择在Linux上配置docker环境。
### 下载
    Docker[https://www.docker.com/]
    使用‘lsb_release -a’命令查看系统版本，下载对应的docker版本
    
### 安装
    $ sudo dpkg -i docker-version.deb
    安装过程中可能会出现insserv相关的警告，这个警告不会影响docker的正常使用。
    
### 测试
    官网推荐使用docker run hello-world来测试docker是否安装成功！但是执行这个命令时要从docker-hub上下载下来然后再运行的，所以在没有网络的情况下
    是会失败的(因为笔者所在公司限制，访问外网比较麻烦)。如果没有网络可以选择下面的方法：

* 查找docker可执行程序
  - sudo find / -name dockerd
* 查看docker进程
  - ps -ef | grep docker
  - 如果没有找到进程，运行上步找到的可执行程序
  
## Docker镜像制作
* Tomcat
    Tomcat[http://tomcat.apache.org/]
    下载时要选择正确的安装包！ 有些包需要从新的编译后才能运行，所以选择已经编译好的包(编译好的包解压后包含了jar文件)
* Jenkins[https://jenkins.io/]
* JDK[http://www.oracle.com/technetwork/java/javase/downloads/index.html]
* Ubuntu镜像
  - https://hub.docker.com/explore/[https://hub.docker.com/explore/]
  - https://partner-images.canonical.com/core/[https://partner-images.canonical.com/core/]
### 基础镜像制作
    $ docker import ubuntu-version.tar.gz  jenkins/ubuntu
    $ docker images
### JDK Tomcat等环境
* Java
    cp /path/to/jdk-version.tar.gz .
    tar -zxvf jdk-version.tar.gz
    mv jdk-version jdk
* Tomcat
    cp /path/to/apache-tomcat-version.tar.gz .
    tar -zxvf apache-tomcat-version.tar.gz
    mv apache-tomcat-version tomcat
* Jenkins
    cp /path/to/jenkins-version.war /path/to/tomcat/webapps
 
### Dockfile编写
    FROM jenkins/ubuntu
    
    MAINTAINER your-name your-email
    
    ENV DEBIAN_FRONTEND noninteractive
    
    ADD /path/to/jdk    jdk
    ADD /path/to/tomcat tomcat
    
    ENV JAVA_HOME     /jdk
    ENV JRE_HOME      /jdk/jre
    ENV TOMCAT_HOME   /tomcat
    ENV CATALINA_HOME /tomcat
    
    VOLUME ["/tomcat/webapps"]
    
    EXPOSE 8008
    
    #CMD ["/tomcat/bin/catalina.sh", "run"]
    ENTRYPOINT ["/tomcat/bin/catalina.sh", "run"]
    
### 镜像制作
    cd /path/to/Dockerfile
    sudo docker build -t jenkins .
* 启动镜像
  - sudo docker run -d -p 8008:8008 jenkins
  
### Docker备份迁移
* 查看正在运行的容器
  - docker ps
* 选择要备份的容器，然后创建快照
  - sudo docker commit -p container-id jenkins-bk
* 查看镜像(快照)并导出
  - sudo docker images
  - sudo docker save -o jenkins.tar.gz jenkins-bk
* 恢复备份
  - docker load -i jenkins.tar.gz
  - docker images
  - docker run -d -p 8008:8008 jenkins-bk
  
## 问题
* windows下，在浏览器中输入localhost:8008无法访问tomcat
    因为在windows下，使用的是docker client，而docker daemon是运行在虚拟机下的。Windows每次启动docker bash是都会开启
    一个默认(default)的虚拟机，而docker daemon就运行在此虚拟机中。使用docker-machine ps 可以看到虚拟机的ip地址。在liulan
    器中输入此ip和端口号便能访问到tomcat了。
    
    
    
