---
title: hexo+github创建个人博客--基础篇
date: 2017-07-16 16:02:11
comments: true
reward: true
toc: true
copyright: true
tags: 
    - hexo
    - github
    - nodejs
---

-  ## 内容简介
	
	> 此篇文章介绍的是基础的hexo+github搭建个人博客的方法，包括搭建之前的准备工作和搭建的步骤过程，当最后达到了预期效果，并且想深入研究其他功能时，可以参考[hexo+github创建个人博客--深入篇](../hexo+github创建个人博客--深入篇),里面介绍了关于博客的主题，图床，评论，统计等功能的配置和实现。

---

<!--more-->

-  ## 工具介绍
	
	### 1.hexo介绍

	> Hexo是一个快速、简洁且高效的博客框架。Hexo使用Markdown解析文章，在几秒内，即可利用靓丽的主题生成静态网页

	### 2.Markdown介绍

	> Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。

---

-  ## 准备工作

    ### 1.github账号创建和git安装

	- 没有github账号的朋友先进行[github账号注册][1]，按照流程注册即可

	- 创建仓库,命名格式为 yourname.git.io,例如[我的github][2]名称为haloMelody，仓库名为haloMelody.git.io

	- 为仓库创建两个分支，master（默认的主分支）用于博客的发布，另外创建一个分支hexo（命名看你自己）用于保存博客项目，为了防止换电脑导致以前的博文丢失的情况，确保记录的同步，并且将自己创建的分支hexo设置为默认分支--settings中可进行设置
	 
	- 创建git-pages，一般仓库创建后会自动勾选，此选项可以保证通过已项目名为域名来访问你的博文（有自己域名的可进行域名绑定，参考http://www.cnblogs.com/penglei-it/p/hexo_domain_name.html）
	 
	- 接下来为保证可以在终端运行git命令，需要[安装git][3]，下载完成后配置环境变量，将cmd目录配置到path中。
		```
		git version  //检测是否安装成功
		```

    ---

    ### 2.nodejs安装
	
	- 进入[node.js官网][4]安装，按照导航进行
		```
		npm -v  //检测nodejs是否安装成功
		```

	- 若出现 命令未找到 的错误提示，则需要手动的配置环境变量，将nodejs目录配到path目录下。此处选择nodejs版本时尽量选择最新的版本，方便后续选择的主题时候的兼容性。

    ---

    ### 3.hexo安装

    - nodejs安装成功后可以使用以下命令安装hexo.
    	```
		npm install hexo -g //安装全局的hexo
		npm install hexo //在某目录下有效
		hexo -v //检测是否成功安装
		```

	- 若出现 命令未找到 的错误提示，则需要手动的配置环境变量，找到hexo的安装目录，全局安装可以参照C:\Users\LittleDragon\AppData\Roaming\npm（改为自己电脑的路径），将此路径配置到path中，并且运行hexo -v检测是否成功。

    ---


- ## 博客搭建

	### 1.项目创建

	- 选择一个位置将仓库同步到本地，进入到文件中，打开文件查看选项中的显示影藏文件，可看到至少有一个.git文件（此处将.git文件复制到别处，随后清空仓库项目文件夹，为了后续操作执行--hexo init 的时候需要文件夹为空），同步命令为：
		```
		git clone  xxx  //复制github仓库路径
		```

	- 随后在此目录下（保证此时是空文件夹），打开CMD（shift+鼠标右键），顺序运行：
		```
		hexo init //初始化项目
		npm install hexo //安装插件
		npm install hexo-deployer-git //发布到git上时必要的插件
		```

	- 项目初始化后，可以看到如下的目录结构，分别表示为：
		> 
		- .deploy #需要部署的文件
		- node_modules #Hexo插件
		- public #生成的静态网页文件
		- scaffolds #模板
		- source #博客正文和其他源文件，404、favicon、CNAME 都应该放在这里
		- _drafts #草稿
		- _posts #文章
		- themes #主题
		- _config.yml #全局配置文件
		- package.json

	- 接下来可以运行命令，创建一篇博文，文章使用的是MarkDown语言，具体语法此处不做介绍
		```
		hexo new "title" //创建指定标题的文章
		```

	- 文章内容编辑好以后，执行命令：
		```
		hexo clean //清除原有记录
		hexo g //generate生成html文件
		hexo s //server本地运行
		```

	- 本地运行以后会实时的对本地文件进行监控，修改后直接刷新浏览器即可看到效果，到此，简单的个人博客已经搭建完成了，接下来可以件博客发布到github上，让网友可以通过你的域名访问个人博客,发布需要的几个步骤：
		> 1. 成功安装了发布需要的插件
			```
			npm install hexo-deployer-git //发布到git上时必要的插件
			```

		> 2. 为你的github配置SSH Key，只需要配置一次，详情请参照[github配置SSH Key][6]

		> 3. 在博客项目根目录下的_config.yml中进行如下配置：
			```
			deploy:
			  type: git
			  repo: git@github.com:yourname/yourname.github.io.git 
			  branch: master
			```
		> 4. 执行发布命令，首次可能需要输入github的用户名和密码
			```
			hexo d
			```
		> 5. 发布之后可以登录你的域名查看效果

------
  
  [1]: https://github.com/
  [2]: https://github.com/haloMelody
  [3]: https://git-scm.com/downloads
  [4]: https://nodejs.org/
  [6]: http://jingyan.baidu.com/article/a65957f4e91ccf24e77f9b11.html