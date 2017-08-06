---
title: 使用HEXO+GITHUB创建个人博客
reward: true
toc: true
tags: 
    - hexo
    - github
    - yilia
---

-  ## 工具介绍
	
	### 1.hexo介绍

	> Hexo是一个快速、简洁且高效的博客框架。Hexo使用Markdown解析文章，在几秒内，即可利用靓丽的主题生成静态网页

	### 2.七牛云图床

	> 挺好的一个云服务网站，注册后可以免费领取10G免费空间等等，可以将博客中需要使用的图片上传到注册的服务器上，可以加快图片的加载速度，至少比在github上的速度快很多。

---

-  ## 准备工作

    ### 1.github账号创建和git安装

	- 没有github账号的朋友先进行[github账号注册][1]，按照流程注册即可

	- 创建仓库,命名格式为 yourname.git.io,例如[我的github][2]名称为haloMelody，仓库名为haloMelody.git.io

	- 为仓库创建两个分支，master（默认的主分支）用于博客的发布，另外创建一个分支hexo（命名看你自己）用于保存博客项目，为了防止换电脑导致以前的博文丢失的情况，确保记录的同步，并且将自己创建的分支hexo设置为默认分支--settings中可进行设置
	 
	- 创建git-pages，一般仓库创建后会自动勾选，此选项可以保证通过已项目名为域名来访问你的博文（有自己域名的可进行域名绑定--本人暂时没有域名...）
	 
	- 接下来为保证可以在终端运行git命令，需要[安装git][3]，下载完成后配置环境变量，将cmd目录配置到path中。
		```
		git version  //检测是否安装成功
		```

    ---

    ### 2.nodejs安装
	
	- 进入[node.js官网][4]安装，按照导航进行，安装完成后配置环境变量，将nodejs目录配到path目录下
		```
		npm -v  //检测nodejs是否安装成功
		```

	- 此处选择nodejs版本时尽量选择最新的版本，方便后续选择的主题时候的兼容性。

    ---

    ### 3.hexo安装

    - nodejs安装成功后可以使用以下命令安装hexo.
    	```
		npm install hexo -g //安装全局的hexo
		npm install hexo //在某目录下有效
		hexo -v //检测是否成功安装
		```

	- 到此基本的准备工作已完成，以下是提供更加丰富的页面效果时可以选择性添加的

    ---

    ### 4.七牛云账号创建和设置(供博客图床使用，可选择性进行)

    - 进入[七牛云官网][5]注册，按照流程进行，登陆后选择对象存储，此时可以选择绑定域名，在内容管理中可以上传图片，复制图片的链接即可在再网页上访问

    ---

    ### 5.评论功能

    ### 6.统计功能

- ## 博客搭建

	### 1.项目创建

	- 选择一个位置将仓库同步到本地，进入到文件中，打开文件查看选项中的显示影藏文件，可看到至少有一个.git文件（此处将.git文件复制到别处，随后清空仓库项目文件夹，为了后续操作执行--hexo init 的时候需要文件夹为空），同步命令为：
		```
		git clone  xxx  //复制github仓库路径
		```

	- 随后在此目录下（保证此时是空文件夹），打开CMD（shift+鼠标右键），运行：
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


------
  
  [1]: https://github.com/
  [2]: https://github.com/haloMelody
  [3]: https://git-scm.com/downloads
  [4]: https://nodejs.org/
  [5]: https://www.qiniu.com/