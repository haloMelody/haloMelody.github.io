---
title: 学习笔记-Redis
date: 2017-09-02 10:41:24
toc: true
reward: true
comments: true
copyright: true
categories: 学习笔记
tags:
	- Linux
	- Redis
	
---

-  ## 内容简介

	> 此篇文章主要介绍了Redis的安装和简单使用，Redis在行业间应用相对较为广泛，并且具备数据持久化的优势，所以也是值得学习了解的一项技术，此处只是简单的介绍，更详细内容大家可以参考网上其他资料，希望对路过的你有所帮助^_^
---

<!-- more -->


-  ## Redis安装

	### 1.准备工作

	- Redis是c语言开发的，安装redis需要c语言的编译环境，所以需要先安装一些语言库：
	```
	yum install gcc-c++
	yum -y install gcc automake autoconf libtool make/yum install gcc-c++
	```

	---

	### 2.安装步骤

	- 上传安装包，解压，进入目录

	- make -> make install PREFIX=/usr/local/redis (指定安装路径)

	- 以上步骤即已经安装完成

	---

	### 3.相关操作

	- 前台启动:
	```
	/usr/local/redis/bin redis.server
	```

	- 后台启动:首先将解压缩后的目录下的redis.conf文件复制到安装路径下,随后修改配置文件，将deamonize no -> deamonize yes
	```
	cp redis.conf /usr/local/redis/bin/
	# 后台启动
	redis.server redis.conf 
	```

	- Redis客户端
	```
	# 通过后面的参数指定连接的IP和端口
	./redis-cli (-h 192.168.XX.100 -p 6379) 
	# 关闭
	./redis-cli shutdown / kill XXX
	```

	- 数据类型
	```
	set str abc / get str / keys * 查看key/ incr/decr key1 (生成key并且加1) / del key

	hash : / hset hash1 filed1 1 / hget hash1 filed1 / hkeys hash1 (列举某个hash列表的key) / hvals hash1 (列举hash的值) /hgetall hash1 (key and val) 
	/ hdel hash1 filed1

	list : / lpush list1 1 2 3 (从左边添加) / rpush list1 a b c (从左边添加) / lrange list1 0 -1 (列举全部)/lpop（rpop） list1 左(右)边取值

	set : 无序不可重复 / sadd set1 a b c / srem set1 a 删除 / smember set1 查看列表 / sdiff seta setb a中特有元素，差集 / sunion seta setb 交集

	expire key1 100 设置过期时间 / ttl key1 查看过期时间（正数-正在倒计时，-1 - 持久化的 ， -2 = 不存在的）/ persist key1 持久化key
	```

	---

	### 4.Redis集群

	- Redis集群中至少应该有三个节点。要保证集群的高可用，需要每个节点有一个备份机。Redis集群至少需要6台服务器。搭建伪分布式。可以使用一台虚拟机运行6个redis实例。需要修改redis的端口号7001-7006

	- 使用ruby脚本搭建集群。需要ruby的运行环境，
	```
	# 安装ruby
	yum install ruby
	```

	- 上传ruby脚本运行使用的包，redis-3.0.0.gem

	- 运行安装ruby脚本运行使用的包
	```
	gem install redis-3.0.0.gem 
	```

	- 新建redis-cluster集群目录，随后将redis解压包redis-3.0.0/src/redis-trib.rb 复制到集群目录下
 
	- 在redis-cluster目录下复制留个redis，模拟六台服务器上的redis,需要运行在不同的端口7001-7006，此处运行在同一台服务器上，启动，并且每个redis的配置文件中，将 cluster-enabled yes 配置打开

	- 为方便启动或者关闭集群中的redis，可以创建两个脚本文件
	```
	# start-all.sh ,根据实际情况修改
	cd redis01
	./redis-server redis.conf
	cd ..
	cd redis02
	./redis-server redis.conf
	cd ..
	cd redis03
	./redis-server redis.conf
	cd ..
	cd redis04
	./redis-server redis.conf
	cd ..
	cd redis05
	./redis-server redis.conf
	cd ..
	cd redis06
	./redis-server redis.conf
	cd ..

	# shutdown-all.sh ,根据实际情况修改
	redis01/redis-cli -p 7001 shutdown
	redis02/redis-cli -p 7002 shutdown
	redis03/redis-cli -p 7003 shutdown
	redis04/redis-cli -p 7004 shutdown
	redis05/redis-cli -p 7005 shutdown
	redis06/redis-cli -p 7006 shutdown

	# 修改文件的执行权限
	chmod u+x start-all.sh
	chmod u+x shutdow-all.sh
	```

	- 在redis-cluster目录下使用ruby脚本搭建集群
	```
	./redis-trib.rb create 
	--replicas 1 （表示每个节点有一个备份机）
	192.168.XX.153:7001 192.168.XX.153:7002 192.168.XX.153:7003 192.168.XX.153:7004 192.168.XX.153:7005 192.168.XX.153:7006
	```
	![redis-cluster][1]

	- 集群使用方法
	```
	# 可以用集群中除备份机以外的任意一台redis连接集群操作
	# 集群操作过程中会随机切换到集群中的redis上进行存储
	redis01/redis-cli -p 7002 -c
	```

---

- ## 结语

	> 此处简单介绍了redis在Linux系统上的相关操作，redis与系统之间的集成也是一个重点，感兴趣的同学可以参考其他资料进行学习，文中若有不对的地方，欢迎指正，仅供参考。


---

[1]: http://ou36vgj5u.bkt.clouddn.com/image/blog/redis/redis_cluster.png
