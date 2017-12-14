---
title: 学习笔记-FastDFS
date: 2017-08-30 14:21:51
toc: true
reward: true
comments: true
copyright: true
categories: 学习笔记
tags:
	- Linux
	- FastDFS
---

-  ## 内容简介

	> 在跟随项目实践过程中，代码中需要使用上传图片的功能，其中使用了FastDFS作为图片服务器，在此记录一下搭建FastDFS服务器的过程

---

<!-- more -->

-  ## 框架介绍

	### 1.什么是FastDFS
	> FastDFS是用c语言编写的一款开源的分布式文件系统。FastDFS为互联网量身定制，充分考虑了冗余备份、负载均衡、线性扩容等机制，并注重高可用、高性能等指标，使用FastDFS很容易搭建一套高性能的文件服务器集群提供文件上传、下载等服务。

	### 2.FastDFS架构
	> FastDFS架构包括 Tracker server和Storage server。客户端请求Tracker server进行文件上传、下载，通过Tracker server调度最终由Storage server完成文件上传和下载。
	Tracker server作用是负载均衡和调度，通过Tracker server在文件上传时可以根据一些策略找到Storage server提供文件上传服务。可以将tracker称为追踪服务器或调度服务器。
	Storage server作用是文件存储，客户端上传的文件最终存储在Storage服务器上，Storage server没有实现自己的文件系统而是利用操作系统 的文件系统来管理文件。可以将storage称为存储服务器,如下图：

	<center>![fastdfs-show][1]</center>

	### 3.Tracker 集群
	> FastDFS集群中的Tracker server可以有多台，Tracker server之间是相互平等关系同时提供服务，Tracker server不存在单点故障。客户端请求Tracker server采用轮询方式，如果请求的tracker无法提供服务则换另一个tracker。

	### 4.Storage集群
	> Storage集群采用了分组存储方式。storage集群由一个或多个组构成，集群存储总容量为集群中所有组的存储容量之和。一个组由一台或多台存储服务器组成，组内的Storage server之间是平等关系，不同组的Storage server之间不会相互通信，同组内的Storage server之间会相互连接进行文件同步，从而保证同组内每个storage上的文件完全一致的。一个组的存储容量为该组内存储服务器容量最小的那个，由此可见组内存储服务器的软硬件配置最好是一致的。
	采用分组存储方式的好处是灵活、可控性较强。比如上传文件时，可以由客户端直接指定上传到的组也可以由tracker进行调度选择。一个分组的存储服务器访问压力较大时，可

-  ## 准备工作
	
	### 1.前提条件

	> 安装好了VM+CentOs6.4（网络等配置都可正常使用）
	> 已安装nginx(非必须)
	> JDK1.7

	### 2.安装所需

	> fastdfs-nginx-module_v1.16.tar.gz - 集成nginx所需模块
	> [FastDFS_v5.05.tar.gz][2] - FastDFS源码，C语言编写，需要手动编译
	> jdk-7u67-linux-x64.tar.gz
	> nginx-1.8.0.tar.gz - nginx源码,C语言编写，需要手动编译
	> libfastcommonV1.0.7.tar.gz - FastDFS官方提供的基础库
	> perl-5.20.2.tar.gz - 编译基础库所需要的语言环境

---

-  ## 服务搭建

	### 1.环境介绍

	- VM + CentOs6.4 + Jdk1.7 + nginx1.8.0

	- 此处记录的是在同一台服务器上同时搭建tracker和storage,并且都是一个，组成的是一个最简便的集群环境。

	---

	### 2.安装libfastcommon基础库

	- 将压缩包上传至虚拟机/usr/local下，解压缩

	- 进入文件夹，看到有make.sh文件，执行 ./make.sh ,初次安装会报错，因为缺少必要的语言运行环境，执行下列命令
	```
	# FastDFS依赖libevent库，需要安装：
    yum -y install libevent

	# 安装gcc库,若之前安装过nginx，则不需要重复安装
	yum -y install gcc 
	yum -y install gcc -c++ 
	```

	- 安装Perl语言环境，需要下载源码perl-5.20.2.tar.gz,上传至/usr/local下，解压缩，进入目录，依次执行：
	```
	$ tar -xzf perl-5.x.y.tar.gz //解压缩
	$ cd perl-5.x.y
	$ ./Configure -de
	$ make
	$ make test
	$ make install
	$ perl -v //检测是否安装成功
	```

	- 环境准备完成后再进入libfastcommon目录，执行 ./make.sh ，库会安装在/usr/lib64目录下，但是FastDFS设置的lib目录是/usr/local/lib ,所以此时可以有两种方式
	```
	# 第一种方式，复制一份
	cp /usr/lib64/libfastcommon.so /usr/local/lib  

	# 第二种方式，建立软连接（推荐）
	ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
    ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
	```

	- 到此基础库安装完成

	---

	### 3.安装FastDFS

	- 将FastDFS_v5.05.tar.gz拷贝至/usr/local/下,依次执行
	```
	tar -zxvf FastDFS_v5.05.tar.gz
	cd FastDFS
	./make.sh
	./make.sh install
	```

	- 如果安装过程中没有报错，并且在/etc/fdfs目录中包含配置文件，说明安装成功，随后将FastDFS目录下的conf下的文件拷贝到/etc/fdfs/下

	---

	### 4.配置tracker

	- 前面所执行的步骤无论是tracker还是storage都是必须的，之后若要在不同的虚拟机上分别配置，主要就是对FastDFS安装完成之后的配置过程的不同，此处演示的是在同一台虚拟机上安装配置

	- 初始化文件目录
	```
	# 配置tracker所需要的base_path
	mkdir /home/fastdfs_tracker 
	```

	- 进入/etc/fdfs目录，复制tracker.conf.simple并改名为tracker.conf

	- 编辑tracker.conf，对以下几个配置进行修改
	```
	# 是否启用此配置文件
	disabled = true 
	# 端口号，一般使用这个默认端口
	port = 22122 
	# tracker的数据和日志文件目录（预先创建）
	base_path = /home/fastdfs_tracker 
	```

	- 启动tracker,执行命令
	```
	/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
	```

	- 查看日志观察启动过程中是否报错
	```
	tail -100f /home/fastdfs_tracker/logs/tracker.log 	(base_path路径)
	```

	- 启动没有问题，则设置tracker为开机自动启动,编辑：vi /etc/rc.d/rc.local ， 将此命令加入末行。至此，tracker安装完毕

	---

	### 5.配置storage

	- 若更换虚拟机则需要执行以上的步骤，此处继续在原虚拟机上执行

	- 初始化文件目录
	```
	# 配置storage所需要的base_path,保存日志信息
	mkdir /home/fastdfs_storage_info 
	# 配置storage所需要的base_path,保存数据文件
	mkdir /home/fastdfs_storage_data 
	```

	- 进入/etc/fdfs目录，复制storage.conf.simple并改名为storage.conf

	- 编辑storage.conf，对以下几个配置进行修改
	```
	# 是否启用此配置文件
	disabled = true 
	# 组名
	group_name = group1
	# 端口,同一组必须保证端口统一
	port = 23001
	# 日志目录
	base_path = /home/fastdfs_storage_info
	# 存储路径个数，与组成员个数保持一致
	store_path_count = 1
	# 数据文件存储路径，果有多个挂载磁盘则定义多个store_path，后缀递增即可
	store_path0 = /home/fastdfs_storage_data
	# tracker服务器IP和端口，如果有多个则配置多个tracker
	tracker_server = 192.168.XX.XXX:22122
	```

	- 启动storage,执行命令
	```
	/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart
	```

	- 观察启动过程中是否报错
	```
	# 通过日志观察
	tail -100f /home/fastdfs_storage_info/logs/storaged.log
	# 通过监控观察是否成功注册到tracker
	/usr/bin/fdfs_monitor /etc/fdfs/storage.conf
	```

	- 启动没有问题，则设置storage为开机自动启动,编辑：vi /etc/rc.d/rc.local ， 将此命令加入末行。至此，storage安装完毕

	---

	### 6.测试服务

	- FastDFS安装成功可通过/usr/bin/fdfs_test测试上传、下载等操作。

	- 编辑/etc/fdfs/client.conf，修改以下配置
	```
	base_path=/home/fastdfs_tracker
	tracker_server=192.168.XX.XXX:22122
	```

	- 使用格式：/usr/bin/fdfs_test 客户端配置文件地址  upload  上传文件,例如：
	```
	/usr/bin/fdfs_test /etc/fdfs/client.conf upload test.jpg
	```

	- 上传后会打印返回信息，可以观察是否上传成功

		> http://192.168.XX.XXX/group1/M00/00/00/wKhlBVVY2M-AM_9DAAAT7-0xdqM485_big.png 就是文件的下载路径。

		> 对应storage服务器上的：/home/fastdfs/fdfs_storage_data/data/00/00/wKhlBVVY2M-AM_9DAAAT7-0xdqM485_big.png文件。

	- 由于现在还没有和nginx整合无法使用http下载

---

-  ## 整合nginx

	- 1.此处讨论的是在之前nginx上重新安装nginx（添加了新模块）的情况，如何安装nginx可以参考之前的博文：学习笔记-nginx

	- 2.不管是在tracker上安装还是storage安装nginx，都需要先上传FastDFS-nginx-module模块，并且修改相应配置

	### 3.tracker上安装nginx

	> 在每个tracker上安装nginx，的主要目的是做负载均衡及实现高可用。此处只有一个tracker.为配置nginx

	### 4.在storage上安装nginx

	- 库环境在此处不再赘述，由于之前已经安装了nginx，此处需要添加新模块FastDFS-nginx-module，添加之前，先下载该模块并且修改相应的配置文件

	- 上传FastDFS-nginx-module至/usr/java目录，解压缩，进入/FastDFS-nginx-module/src目录下，编辑config文件，将/usr/local改为/usr (将/local去掉)
	```
	CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon/"
	CORE_LIBS="$CORE_LIBS -L/usr/lib -lfastcommon -lfdfsclient"
	```

	- 将FastDFS-nginx-module/src下的mod_FastDFS.conf拷贝至/etc/fdfs/下,并且修改配置（类比按照自己实际情况修改）：
	```
	base_path = /home/fastdfs_storage_info
	tracker_server = XX
	storage_server_port = XX
	group_name = XX
	url_have_group_name = true #是否在url中使用组名
	store_path_count = 1
	store_path0 = /home/fastdfs_storage_data
	group_count = 1
	#最后根据自己实际情况，为每一个组添加如下配置
 	[group1]
	group_name=group1
	storage_server_port=23000
	store_path_count=1
	store_path0=/home/fastdfs_storage_data
	```

	- 进入nginx源码的目录（不是编译后生产的目录）：/usr/java/nginx-1.8.0执行命令：
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
	--add-module=/usr/java/fastdfs-nginx-module/src  //XXX为模块存在路径
	```

	- 成功后执行make，重新编译，会生成新的nginx执行文件，不要执行make install，在obj目录下，将obj/nginx复制到旧版本的nginx安装目录下，将旧版本的改为nginx.bak

	- 测试新的nginx程序是否正确，执行/usr/local/nginx/sbin/nginx -t , 若显示以下信息则测试通过
	```
	nginx: theconfiguration file /usr/local/nginx/conf/nginx.conf syntax is ok
	nginx:configuration file /usr/local/nginx/conf/nginx.conf test issuccessful
	```

	- FastDFS设置的lib目录是/usr/local/lib ,所以此时可以有两种方式
	```
	# 第一种方式，复制一份
	cp /usr/lib64/libfdfsclient.so /usr/local/lib  

	# 第二种方式，建立软连接（推荐）
	ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
    ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
	```

	- 最后需要配置nginx反向代理，进入nginx安装目录，编辑nginx.conf文件，添加server节点：
	```
	server {
        listen       80;
        server_name  localhost;

        location /group1/M00 {
                root /home/fastdfs_storage_data/data;
                ngx_fastdfs_module;
        }

        # location / {
           # root   html;
           # index  index.html index.htm;
        # }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html81;
        }
    }
	```

	- 配置好节点后，依次重启tracker,重启storage，重启nginx，利用上述测试上传图片返回的url，在浏览器中访问，检测是否可以成功访问

	## 5.坑和排坑

	- 我在操作过程中，卡在重新安装nginx这一个步骤很久，启动nginx的时候一直不成功，此时可以通过查看nginx的日志文件来查看错误，这个很重要

	- 编辑nginx.conf文件，添加server节点时，注意监听的端口，80端口只能配置一个

	- 若正常启动了nginx还是不能成功访问图片（图片已经成功上传的情况下），也可以通过查看日志来解决，比如我访问的时候路径里面会带上/html/group1/...等路径，就是因为下面那个默认的location没有注释掉导致的

	- 看日志，很重要 tail -100f /var/log/nginx/error.log 

---

- ## 结语

> 搭这个服务器花了差不多一天的时间。。。过程中难免遇到很多坑，多查资料多百度，文中记录的信息有限，中途说不定也有遗漏的信息，欢迎指正，仅供参考！

---

[1]: http://ou36vgj5u.bkt.clouddn.com/image/blog/fastdfs/fastdfs-show.png
[2]: http://sourceforge.net/projects/FastDFS/