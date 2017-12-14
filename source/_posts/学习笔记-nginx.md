---
title: 学习笔记-nginx
date: 2017-08-21 15:46:42
toc: true
reward: true
comments: true
copyright: true
categories: 学习笔记
tags:
	- Linux
	- nginx
	
---

-  ## 内容简介

	> 此篇文章简单的介绍了什么是nginx以及nginx的简单使用，nginx现在使用非常的广泛，值得去了解学习。此处只是简单的记录一下，更详细的操作步骤可以参考网上资料，仅供参考。希望对路过的你有帮助^_^


---

<!-- more -->

-  ## nginx介绍

	### 1.什么是nginx

	> Nginx是一款高性能的http 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。由俄罗斯的程序设计师Igor Sysoev所开发，官方测试nginx能够支支撑5万并发链接，并且cpu、内存等资源消耗却非常低，运行非常稳定。

	### 2.应用场景

	> 1、http服务器。Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。
	2、虚拟主机。可以实现在一台服务器虚拟出多个网站。例如个人网站使用的虚拟主机。
	3.反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用nginx做反向代理。并且多台服务器可以平均分担负载，不会因为某台服务器负载高宕机而某台服务器闲置的情况

---

-  ## nginx安装

	### 1.准备工作

	> nginx是C语言开发，安装包中只有源代码，需要手动编译，因此在linux上安装时需要C语言的运行环境以及一些别的库，所以需要一下的准备工作：
	```
	# nginx安装包下载
	http://nginx.org/
	# 安装gcc的环境
	yum install gcc-c++
	# 安装pcre库
	yum install -y pcre pcre-devel
	# 安装zlib库 
	yum install -y zlib zlib-devel
	# 安装openssl库
	yum install -y openssl openssl-devel	
	```

	> **执行yum命令时可能会报无法找到镜像的错误**
	```
	1.首先查看是否能正确连网，可以通过ping外网的方式测试
	2.再能正常连网的情况下，配置DNS服务，vi /etc/resolv.conf 在最后添加
	    nameserver 8.8.8.8
	    nameserver 8.8.4.4
	    search localdomain
	3.重启网络服务: service network restart 即可
	```

	---

	### 2.安装步骤

	- 把nginx的源码包上传到linux系统,解压缩

	- 进入nginx目录，有configure可执行文件，运行命令：
	```
	./configure \
	--prefix=/usr/local/nginx \
	--pid-path=/var/run/nginx/nginx.pid \
	--lock-path=/var/lock/nginx.lock \
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log \
	--with-http_gzip_static_module \
	--http-client-body-temp-path=/var/temp/nginx/client \
	--http-proxy-temp-path=/var/temp/nginx/proxy \
	--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
	--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
	--http-scgi-temp-path=/var/temp/nginx/scgi

	* 注释
	prefix=/usr/local/nginx  -安装目录
	/var/temp/nginx/client	 - 临时文件目录，需要手动创建
	mkdir /var/temp/nginx/client -p
	```

	- 创建makeFile文件(如果有则无需创建) -> make(编译) -> make install（安装）

	- 安装成功后，去对应目录查看，此处在/usr/local/nginx ，进入sbin,执行 ./nginx ，如果提示XXX目录不存在，则创建该目录即可，此处是安装时应用到的目录未创建的原因

	- .执行./nginx，通过查看进程判断是否正常启动，需要同时具备两个nginx进程才表示正常启动
	![nginx-ps][1]

	- 直接通过IP访问nginx，默认是80端口。需要注意是否关闭防火墙。如下图，即表示nginx安装并且启动成功
	![nginx-succ][2]

	- 相关操作命令
	```
	# 启动
	./nginx
	# 停止nginx
	sbin/nginx -s stop 或者 sbin/nginx -s quit
	# 刷新配置文件,平滑重启
	sbin/nginx -s reload
	```

	---

	### 3.配置虚拟主机

	- 含义：一台服务器启动多个网站，就是把一台物理服务器划分成多个“虚拟”的服务器，每一个虚拟主机都可以有独立的域名和独立的目录

	- 配置虚拟主机就是通过修改配置Nginx的配置文件：vi /usr/local/nginx/conf/nginx.conf 中的server节点，一个server节点对应一个虚拟主机

	- 通过端口区分
	```
	server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
        	# 对应nginx安装目录下的HTML目录
            root   html;
            index  index.html index.htm;
        }
    }
    server {
        listen       81;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
        	# 可以复制一个目录，修改index.html中的内容，进行区分
            root   html-81;
            index  index.html index.htm;
        }
    }
	```

	- 重启 sbin/nginx -s reload ，访问IP+端口，观察效果

	- 通过域名区分虚拟主机

	- 什么是域名？ littledragon.cn 就是域名哦，亲；可以网上查找相关知识

	- 本地测试可以修改hosts文件。修改window的hosts文件：（C:\Windows\System32\drivers\etc）可以配置域名和ip的映射关系，如果hosts文件中配置了域名和ip的对应关系，不需要走dns服务器，可以进行直接映射
	```
	192.168.XX.100 littledragon.cn
	192.168.XX.100 www.baidu.com
	```

	- 通过域名区分
	```
	 server {
        listen       80;
        server_name  littledragon.cn;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html-taobao;
            index  index.html index.htm;
        }
    }
    server {
        listen       80;
        server_name  www.baidu.com;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html-baidu;
            index  index.html index.htm;
        }
    }
	```

	---

	### 4.反向代理

	- 两个域名指向同一台nginx服务器，用户访问不同的域名显示不同的网页内容，详细解释可以网上参考其他资料

	- 此处模拟测试的域名采用：www.sian.com.cn和www.sohu.com，需要在host文件中配置本地映射

	- 安装两个tomcat，分别运行在8080和8081端口，启动（可以将两个显示内容加以区分，使效果明显）

	- 反向代理服务器的配置
	```
	upstream tomcat1 {
	server 192.168.XX.100:8080;
    }
    server {
        listen       80;
        server_name  www.sina.com.cn;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass   http://tomcat1;
            index  index.html index.htm;
        }
    }
    upstream tomcat2 {
	server 192.168.XX.100:8081;
    }
    server {
        listen       80;
        server_name  www.sohu.com;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass   http://tomcat2;
            index  index.html index.htm;
        }
    }
	```

	- 重启nginx，依次通过浏览器访问两个域名，查看效果

	---

	### 5.负载均衡

	- 如果一个服务由多条服务器提供，需要把负载分配到不同的服务器处理，需要负载均衡,可以根据服务器的实际情况调整服务器权重。权重越高分配的请求越多，权重越低，请求越少。默认是都是1
	```
	upstream tomcat2 {
		server 192.168.25.148:8081;
		server 192.168.25.148:8082 weight=2;
    }
	```

	---

	### 6.高可用（简介）
	> 
	1.keepalived + nginx
	2.主nginx 和 备nginx 都安装keepalive，并且都是用一个VIP（虚拟IP）
	3.备nginx 会一直向主nginx发送心跳包检测主机是否正常运行
	4.主机宕机后，备nginx绑定VIP，继续运行
	5.主机回复后，取回VIP



---

- ## 结语

> 简答介绍了nginx相关的一些内容，如果需要更详细的资料可以去网上或者官网查看，此处只是简单作为记录的作用，希望对路过的你有帮助^_^



---

[1]: http://ou36vgj5u.bkt.clouddn.com/image/blog/nginx/nginx_ps.png
[2]: http://ou36vgj5u.bkt.clouddn.com/image/blog/nginx/nginx_succ.png
