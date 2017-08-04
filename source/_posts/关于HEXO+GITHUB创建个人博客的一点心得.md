---
title: 使用HEXO+GITHUB创建个人博客
reward: true
toc: true
tags: 
    - hexo
    - github
    - yilia
---

-  ## **准备工作**

    ### 1.github账号创建和git安装

	- 没有github账号的朋友先进行[github账号注册][1]，按照流程注册即可

	- 创建仓库,命名格式为 yourname.git.io,例如[我的github][2]名称为haloMelody，仓库名为haloMelody.git.io

	- 为仓库创建两个分支，master（默认的主分支）用于博客的发布，另外创建一个分支hexo（命名看你自己）用于保存博客项目，为了防止换电脑导致以前的博文丢失的情况，确保记录的同步，并且将自己创建的分支hexo设置为默认分支--settings中可进行设置
	 
	- 创建git-pages，一般仓库创建后会自动勾选，此选项可以保证通过已项目名为域名来访问你的博文（有自己域名的可进行域名绑定--本人暂时没有域名...）
	 
	- 接下来为保证可以在终端运行git命令，需要[安装git][3]，下载完成后配置环境变量，将cmd目录配置到path中。
		```
		git version  //检测是否安装成功
		```

	- 选择一个位置将仓库同步到本地，进入到文件中，打开文件查看选项中的显示影藏文件，可看到至少有一个.git文件（此处将.git文件复制到别处，随后清空仓库项目文件夹，为了后续操作执行--hexo init 的时候需要文件夹为空），同步命令为：
		```
		git clone  xxx  //复制github仓库路径
		```
    ---

    ### 2.nodejs安装
	
	- 进入[node.js官网][4]安装，按照导航进行，安装完成后配置环境变量，将nodejs目录配到path目录下
		```
		npm -v  //检测nodejs是否安装成功
		```

    ---

    ### 3.hexo安装
    
    ---
    ### 4.七牛云账号创建和设置(供博客图床只用，可选择性进行)

    ---




------
  
  [1]: https://github.com/
  [2]: https://github.com/haloMelody
  [3]: https://git-scm.com/downloads