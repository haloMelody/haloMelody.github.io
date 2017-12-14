---
title: 学习笔记-dubbo监控和注册中心
date: 2017-08-13 11:51:33
toc: true
reward: true
comments: true
copyright: true
categories: 学习笔记
tags:
	- Linux
	- Dubbo
	- JDK
	- Zookeeper

---

-  ## 内容简介

	> 此篇博客记录了dubbo使用过程中的一些问题，涉及到dubbo的介绍和简单安装（dubbo-admin）,并且由于会涉及到注册中心zookeeper，以及需要的JDK环境，所以在文章中都做了简单的记录，希望对路过的你有帮助^_^.


---

<!-- more -->

-  ## dubbo监控中心

	### 1.简介

	> 可以对dubbo服务进行监控和管理

	### 2.安装

	- Linux上传tomcat，解压，保证可以正常启动

	- 将对应dubbo-admin.war部署到tomcat/webapps下，命名为dubbo-admin.war(或者ROOT.war,影响到访问的路径)，启动tomcat

	- 访问 http://192.168.XX.XXX:8080/dubbo-admin/ ，用户名：root 	密码：root

	- 如果监控中心和注册中心在同一台服务器上，可以不需要任何配置。默认的是同一台服务器

	- 如果不在同一台服务器，需要修改配置文件：/root/tomcat/webapps/dubbo-admin/WEB-INF/dubbo.properties,其中可以设置zookeeper集群，具体细节可参网上其他资料

	- 验证。需要注意dubbo2.5.4之前的版本与JDK1.8不兼容

	---

---

-  ## zookeeper注册中心

	### 1.简介

	> dubbo官方推荐使用zookeeper注册中心,注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小。使用dubbo-2.3.3以上版本，建议使用zookeeper注册中心。Zookeeper是Apacahe Hadoop的子项目，是一个树型的目录服务，支持变更推送，适合作为Dubbo服务的注册中心，工业强度较高，可用于生产环境，并推荐使用.

	#### 2.Zookeeper的安装

	- 依赖Java环境，JDK的安装:上传JDK安装包，解压，配置环境变量(vi /etc/profile), check(java -version)

	- 上传zookeeper安装包，解压

	- 进入zookeeper目录，创建data目录

	- 把zoo_sample.cfg改名为zoo.cfg，编辑
		```
		# 记录数据信息
		dataDir=/usr/java/zookeeper-3.4.6/data
		# 记录日志信息
        dataLogDir=/usr/java/zookeeper-3.4.6/log
        server.0=192.168.71.100:2888:3888(若是配置集群可多个)
		```
	- data目录中新建 myid 文件 输入对应server.n 中的n这个数值，比如此处输入0,对应一台机器的服务器IP端口

	- zookeeper操作命令：
		```
		./zkServer.sh status -- 查看状态
		./zkServer.sh start -- 启动
		./zkServer.sh stop	-- 停止
		```
	- 与外界连接时需要关闭防火墙：
		```
		service iptables stop -- 关闭防火墙
    	chkconfig iptables off -- 开机不启动防火墙
		```

	---


---

- ## 结语

> 简单记录了涉及到的工具的安装过程，若有疑问可参考网上其他详细操作步骤，此处仅作为记录，以供以后回忆所用



---
