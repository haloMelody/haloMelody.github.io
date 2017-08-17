---
title: Dubbo--简单介绍和使用（Simple）
date: 2017-08-16 16:02:11
toc: true
reward: true
comments: true
copyright: true
categories: dubbo
tags:
	- dubbo
	- spring boot
---

-  ## 内容简介

	> 此篇文章是介绍Dubbo以及它的简单使用，会列举运用spring boot + dubbo搭建项目运用dubbo的步骤，主要是介绍一下dubbo的作用以及简单的配置，若有兴趣的朋友可以继续关注后续的[dubbo][1]系列文章，也可以参考[官方文档][2]进行学习.

<!-- more -->

-  ## Dubbo介绍
	
	### 1.什么是Dubbo

	> 一个分布式服务治理框架

	---

	### 2.为什么用Dubbo

	- 官方介绍

	> 当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。 此时，用于提高业务复用及整合的分布式服务框架(RPC)是关键。

	- 自我心得

	> 小项目中dubbo作用不明显，因为项目中的Api都是通过直接依赖调用，当项目庞大比并且服务需要多次重复性的调用时，就需要一个框架来治理，dubbo可以做到的效果就是通多xml文件配置，达到一次提供，到处调用的效果，并且和可以对服务的提供者和消费者进行管理；就是将提供服务的Api打包到服务器，同时注册到注册中心（zookeeper）,需要调用此服务的只需依赖服务器上的jar包，配置消费者服务即可调用Api。

	### 3.基本概念

	- 节点角色说明

	![dubbo-node][3]

		> 
		- provider 服务的提供方
		- consumer 服用的消费方
		- registry 注册中心（可以对提供方和消费方统一管理）
		- monitor 统计中心
		- container 运行容器

	---

	- 调用关系说明：

		>
		- 服务容器负责启动，加载，运行服务提供者。
	    - 服务提供者在启动时，向注册中心注册自己提供的服务。
	    - 服务消费者在启动时，向注册中心订阅自己所需的服务。
	    - 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
	    - 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
	    - 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

	---

-  ## Dubbo使用（Simple）


	### 1.准备工作

	> 为了更加直观的体现dubbo的作用，在此我会搭建一个简单的maven项目，通过项目的搭建流程和dubbo的相关简单配置，介绍dubbo的使用，所以，需要做好以下最基本的准备工作：

		- JDK(1.8)
		- 开发工具(IDEA)
		- maven(3.3.9)
		- zookepper(注册中心)


	### 2.项目搭建

	- *dubbo-provider*

	> 
		- 为了后续代码更好的演示，将两个项目建立在一个工作空间下（IDEA），创建简单的接口和实现类，简单的测试方法，基本结构为：

		![dubbo-project][4]

		- 接口基本实现为：
		```
		public interface ISimpleService {
		    public String sayHello(String name);
		}

		@Service
		public class SimpleServiceImpl implements ISimpleService {
		    public String sayHello(String name) {
		        return "Hello" + name;
		    }
		}

		```

---

		- 配置simple-provider.pom,搭建spring boot运行环境，这里给出一个基本的模板，其中包含mysql的依赖，以及使用基本dubbo的依赖，还有注册中心zookeeper的相关依赖，可以根据实际情况修改：
		```
		<parent>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-parent</artifactId>
		    <version>1.2.8.RELEASE</version>
		</parent>

		<dependencies>
		    <dependency>
		        <groupId>org.springframework.boot</groupId>
		        <artifactId>spring-boot-starter-web</artifactId>
		        <exclusions>
		            <exclusion>
		                <artifactId>logback-classic</artifactId>
		                <groupId>ch.qos.logback</groupId>
		            </exclusion>
		            <exclusion>
		                <artifactId>log4j-over-slf4j</artifactId>
		                <groupId>org.slf4j</groupId>
		            </exclusion>
		        </exclusions>
		    </dependency>
		    <dependency>
		        <groupId>org.springframework</groupId>
		        <artifactId>spring-context-support</artifactId>
		    </dependency>
		    <dependency>
		        <groupId>org.springframework</groupId>
		        <artifactId>spring-jdbc</artifactId>
		    </dependency>
		    <dependency>
		        <groupId>org.slf4j</groupId>
		        <artifactId>slf4j-log4j12</artifactId>
		    </dependency>
		    <dependency>
		        <groupId>org.springframework.boot</groupId>
		        <artifactId>spring-boot-starter-jdbc</artifactId>
		    </dependency>
		    <dependency>
		        <groupId>mysql</groupId>
		        <artifactId>mysql-connector-java</artifactId>
		    </dependency>
		    <!--dubbo-->
		    <dependency>
		        <groupId>com.alibaba</groupId>
		        <artifactId>dubbo</artifactId>
		        <version>2.8.4</version>
		    </dependency>
		    <!--zookeeper 相关-->
		    <dependency>
		        <groupId>org.apache.zookeeper</groupId>
		        <artifactId>zookeeper</artifactId>
		        <version>3.4.8</version>
		    </dependency>
		    <dependency>
		        <groupId>com.github.sgroschupf</groupId>
		        <artifactId>zkclient</artifactId>
		        <version>0.1</version>
		    </dependency>
		</dependencies>

		<dependencyManagement>
		    <dependencies>
		        <dependency>
		            <!-- Import dependency management from Spring Boot -->
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-dependencies</artifactId>
		            <version>1.2.8.RELEASE</version>
		            <type>pom</type>
		            <scope>import</scope>
		        </dependency>
		    </dependencies>
		</dependencyManagement>

		<repositories>
		    <repository>
		        <id>public</id>
		        <name>nexus-repository</name>
		        <url>http://192.168.1.169:8080/nexus/content/groups/public</url>
		        <releases>
		            <enabled>true</enabled>
		        </releases>
		        <snapshots>
		            <enabled>true</enabled>
		        </snapshots>
		    </repository>
		    <repository>
		        <id>release</id>
		        <name>nexus-repository</name>
		        <url>http://192.168.1.169:8080/nexus/content/repositories/releases</url>
		        <releases>
		            <enabled>true</enabled>
		        </releases>
		        <snapshots>
		            <enabled>true</enabled>
		        </snapshots>
		    </repository>
		</repositories>
		<pluginRepositories>
		    <pluginRepository>
		        <id>nexus</id>
		        <name>nexus-repository</name>
		        <url>http://192.168.1.169:8080/nexus/content/groups/public/</url>
		        <releases>
		            <enabled>true</enabled>
		        </releases>
		        <snapshots>
		            <enabled>false</enabled>
		        </snapshots>
		    </pluginRepository>
		</pluginRepositories>

		<build>
		    <plugins>
		        <plugin>
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-maven-plugin</artifactId>
		            <executions>
		                <execution>
		                    <goals>
		                        <goal>repackage</goal>
		                    </goals>
		                </execution>
		            </executions>
		        </plugin>
		    </plugins>
		</build>

		```
	---

		- 配置完pom文件后还需要配置application.xml(spring默认加载的配置文件，根目录下即可，也可以自己修改配置文件制定)，由于spring boot默认会配置jdbcTemplate，所以需要指定一个dataSourcec(也可通过配置修改，不多说)：
		```
		# 制定spring boot运行的端口
		server.port=8899

		#db properties 需要指定一个datasource
		spring.datasource.url=xxx
		spring.datasource.username=xxx
		spring.datasource.password=xxx
		spring.datasource.driver-class-name=com.mysql.jdbc.Driver

		```

	---

		- 随后配置simple-dubbo-provider.xml，此处我们做最简单的配置：
		```
		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
		       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		       xmlns:context="http://www.springframework.org/schema/context"
		       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
		       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

		    <!--提供的服务名称 自己指定即可 代表你提供的这个服务-->
		    <dubbo:application name="simpleprovider"></dubbo:application>
		    <!--注册中心 本地启动zookeeper后默认的ip+port-->
		    <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>
		    <!--协议 port自己指定-->
		    <dubbo:protocol name="dubbo" port="8899"></dubbo:protocol>
		    <!--提供的接口服务-->
		    <dubbo:service ref="simpleServiceImpl" interface="cn.littledragon.dubbo.service.ISimpleService"></dubbo:service>

		```

	---

		- 最后可以配置日志文件进行，进行日志记录

		- 最后，编写main函数运行项目，运用spring boot中写好的main方法，加以修改：
		```
		@SpringBootApplication
		@ComponentScan("cn.littledragon")
		// @MapperScan(basePackages = "com.tdh.swaptrailer.comm.dal.mapper")
		@ImportResource("simple-dubbo-spring.xml")
		public class Application {

		   public static final String CONTAINER_KEY = "dubbo.container";

		   public static final String SHUTDOWN_HOOK_KEY = "dubbo.shutdown.hook";

		   private static final Logger LOGGER = LoggerFactory.getLogger(Application.class);

		   private static final ExtensionLoader<Container> LOADER = ExtensionLoader.getExtensionLoader(Container.class);

		   private static volatile boolean running = true;

		   protected static void keepRunning(String[] args) {
		      try {
		         if (args == null || args.length == 0) {
		            String config = ConfigUtils.getProperty(CONTAINER_KEY, LOADER.getDefaultExtensionName());
		            args = Constants.COMMA_SPLIT_PATTERN.split(config);
		         }

		         final List<Container> containers = new ArrayList<Container>();
		         for (int i = 0; i < args.length; i++) {
		            containers.add(LOADER.getExtension(args[i]));
		         }
		         LOGGER.info("Use container type(" + Arrays.toString(args) + ") to run dubbo serivce.");

		         if ("true".equals(System.getProperty(SHUTDOWN_HOOK_KEY))) {
		            Runtime.getRuntime().addShutdownHook(new Thread() {
		               public void run() {
		                  for (Container container : containers) {
		                     try {
		                        container.stop();
		                        LOGGER.info("Dubbo " + container.getClass().getSimpleName() + " stopped!");
		                     } catch (Exception t) {
		                        LOGGER.error(t.getMessage(), t);
		                     }
		                     synchronized (Application.class) {
		                        running = false;
		                        Application.class.notify();
		                     }
		                  }
		               }
		            });
		         }

		         for (Container container : containers) {
		            container.start();
		            LOGGER.info("Dubbo " + container.getClass().getSimpleName() + " started!");
		         }
		         LOGGER.info(new SimpleDateFormat("[yyyy-MM-dd HH:mm:ss]").format(new Date())
		               + " Dubbo service server started!");
		      } catch (RuntimeException e) {
		         LOGGER.error(e.getMessage(), e);
		      }
		      synchronized (Application.class) {
		         while (running) {
		            try {
		               Application.class.wait();
		            } catch (Exception e) {
		               LOGGER.error(e.getMessage(), e);
		            }
		         }
		      }
		   }

		   public static void main(String[] args) throws Exception {
		      // SpringApplication.run(Application.class, args);

		      SpringApplication app = new SpringApplication(Application.class);

		      app.setWebEnvironment(false);

		      app.run(args);

		      keepRunning(args);
		   }
		}

		```




	---

	### 3.spring boot配置

	---

	### 4.代码编辑

	---

	### 5.dubbo配置

	---

	### 6.运行效果

	---


- ## 结语

---




[1]: http://dubbo.io/user-guide/
[2]: http://dubbo.io/user-guide/ 
