---
title: hexo+github创建个人博客--深入篇
reward: true
toc: true
tags: 
    - hexo
    - github
    - yilia
---

- ## 内容简介

	> 此篇文章介绍的是个人博客的一些配置内容，包含博客项目的介绍、主题配置、图床配置以及各种第三方功能插件的使用，若还未搭建个人博客的哥们可以先参考[hexo+github创建个人博客--基础篇][1]搭建出自己的个人博客。

<!-- more -->

---

- ## hexo项目介绍

	### 1.目录结构介绍
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

	---

	### 2.全局配置文件介绍
	```
	title: 个人博客		//页面标题
	subtitle: 玉面小飞龙		//小标题
	description: 贼溜		//描述
	author: Little Dragon		//作者
	language: zh-CN		//语言
	timezone:		//时区

	# URL
	## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
	url: https://halomelody.github.io/		//个人域名
	root: /		//根目录
	permalink: :year/:month/:day/:title/		//文章创建后生成的目录结构
	permalink_defaults:

	# Directory		//变量与目录的对应情况
	source_dir: source
	public_dir: public
	tag_dir: tags
	archive_dir: archives
	category_dir: categories
	code_dir: downloads/code
	i18n_dir: :lang
	skip_render:

	# Writing
	new_post_name: :title.md # File name of new posts
	default_layout: post
	titlecase: false # Transform title into titlecase
	external_link: true # Open external links in new tab
	filename_case: 0
	render_drafts: false
	post_asset_folder: false
	relative_link: false
	future: true
	highlight:
	  enable: true
	  line_number: true
	  auto_detect: false
	  tab_replace:
	  
	# Home page setting
	# path: Root path for your blogs index page. (default = '')
	# per_page: Posts displayed per page. (0 = disable pagination)
	# order_by: Posts order. (Order by date descending by default)
	index_generator:		//分页设置
	  path: ''
	  per_page: 5
	  order_by: -date
	  
	# Category & Tag
	default_category: uncategorized
	category_map:
	tag_map:

	# Date / Time format
	## Hexo uses Moment.js to parse and display date
	## You can customize the date format as defined in
	## http://momentjs.com/docs/#/displaying/format/
	date_format: YYYY-MM-DD
	time_format: HH:mm:ss

	# Pagination
	## Set per_page to 0 to disable pagination
	per_page: 5
	pagination_dir: page

	# Extensions
	## Plugins: https://hexo.io/plugins/
	## Themes: https://hexo.io/themes/
	theme: yilia		//主题配置

	# Deployment
	## Docs: https://hexo.io/docs/deployment.html
	deploy:		//发布对应的github账号
	  type: git
	  repo: git@github.com:haloMelody/haloMelody.github.io.git
	  branch: master

	```
---

- ## 主题配置

	### 1.主题选择

	- 网上好的主题有很多，可以[点击][2]选择，本文介绍的是[yilia][3]主题

	---

	### 2.配置主题

	- 从github上下载主题
		```
		git clone https://github.com/litten/hexo-theme-yilia.git
		```

	- 将主题配置到个人博客中，在根目录的全局配置文件中修改此变量（主题项目中也有_config.yml配置文件）
		```
		theme: yilia
		```

	- 主题配置文件介绍（摘要）
	```
	# Header，主题页面显示链接
	menu:
	  主页: /
	  所有文章: /archives

	# Content
	# 文章太长，截断按钮文字
	excerpt_link: more
	# 文章卡片右下角常驻链接，不需要请设置为false
	show_all_link: '展开全文'
	# 数学公式
	mathjax: false
	# 是否在新窗口打开链接
	open_in_new: false

	# 打赏
	# 打赏type设定：0-关闭打赏； 1-文章对应的md文件里有reward:true属性，才有打赏； 2-所有文章均有打赏
	reward_type: 1
	# 打赏wording
	reward_wording: 老板大气，谢谢老板！
	# 支付宝二维码图片地址，跟你设置头像的方式一样。比如：/assets/img/alipay.jpg
	alipay: http://ou36vgj5u.bkt.clouddn.com/image/blog/alipay.JPG
	# 微信二维码图片地址
	weixin: http://ou36vgj5u.bkt.clouddn.com/image/blog/wechatpay.JPG

	# 目录
	# 目录设定：0-不显示目录； 1-文章对应的md文件里有toc:true属性，才有目录； 2-所有文章均显示目录
	toc: 1
	# 根据自己的习惯来设置，如果你的目录标题习惯有标号，置为true即可隐藏hexo重复的序号；否则置为false
	toc_hide_index: true
	# 目录为空时的提示
	toc_empty_wording: '目录，不存在的…'

	# 是否有快速回到顶部的按钮
	top: true

	# Miscellaneous，网站统计
	baidu_analytics: '24451a8b853bb443019686be21dfdff4'
	google_analytics: ''
	favicon: http://ou36vgj5u.bkt.clouddn.com/image/blog/fav.ico

	#你的头像url
	avatar: http://ou36vgj5u.bkt.clouddn.com/image/blog/head.jpg

	#是否开启分享
	share_jia: true

	#评论：1、多说；2、网易云跟帖；3、畅言；4、Disqus 不需要使用某项，直接设置值为false，或注释掉
	#具体请参考wiki：https://github.com/litten/hexo-theme-yilia/wiki/
	#1、多说
	duoshuo: false
	#2、网易云跟帖
	wangyiyun: false
	#3、畅言
	changyan_appid: false
	changyan_conf: false
	#4、Disqus 在hexo根目录的config里也有disqus_shortname字段，优先使用yilia的
	disqus: false
	#disqus_shortname: halomelody

	# slider的设置
	slider:
	  # 是否默认展开tags板块
	  showTags: true

	# 智能菜单
	# 如不需要，将该对应项置为false
	# 比如
	#smart_menu:
	#  friends: false
	smart_menu:
	  innerArchive: '文章查找'
	  friends: '友链'
	  aboutme: '关于我'
	  #tagcloud: '标签'

	#friends:
	aboutme: little dragon

	# 页面统计 true为开启
	#page_count: false

	# 站点统计 true为开启
	#site_count: true
	```

	- 弹框配置

		-	首先需要具备hexo中nodeJS版本高于6.2才可以生效（下载最新的nodeJS->得到的hexo可以即可满足，可以通过hexo -v查看nodeJS版本）

		![hexo-yilia-pop][4]

		-	在根目录配置文件中添加如下内容：
		```
		jsonContent:  
		  meta: false  
		  pages: false  
		  posts:  
		    title: true  
		    date: true  
		    path: true  
		    text: true  
		    raw: false  
		    content: false  
		    slug: false  
		    updated: false  
		    comments: false  
		    link: false  
		    permalink: false  
		    excerpt: false  
		    categories: false  
		    tags: true
		```

		- 执行清除，生成，其服务等操作查看效果

	- 标签配置
	```
	title: hexo+github创建个人博客--深入篇
	tags: 
	    - hexo
	    - github
	    - yilia
	```

	- 文章分类
	```
	title: hexo+github创建个人博客--深入篇
	categories: 瞎搞
	```

	- 目录配置
	```
	title: hexo+github创建个人博客--深入篇
	toc: true
	```

	- 内容截断
		-	主题配置文件中配置截断显示的文字，excerpt_link: more ,此处显示为more
		-	在文章中键入该注释，即可在该位置生效：
		```
		<!-- more -->
		```

	- 相册功能(待实现)

	---

- ## github同步本地博客

	### 1.提交

	- 在本地配置好个人博客，发布使用后，为防止电脑更换或突发事件导致电脑文件丢失，可以讲博客项目上传至github上保存，需要时再更新到本地即可（此时我们提交的分支为修改后的默认分支--hexo,详细介绍请看[hexo+github创建个人博客--基础篇][1]中的仓库设计），提交命令为:
		```
		git add . 		//将文件加入到提交缓存
		git commit -m "comment"	//添加文件注释
		git push origin hexo	//提交到某分支
		```

	- **提交以后会发现主题文件没有提交上去，是因为此时的主题文件是另一个仓库的git文件，需要将本地主题文件中的.git文件夹删掉，再次提交即可**

	---

	### 2.更新

	- 在本地项目中运行
		```
		git pull	//更新到本地
		git status	//查看文件对比后的状态
		```

	---

- ## 其他功能配置

	### 1.七牛云图床(供博客图床使用，可选择性进行)

	- 七牛云介绍

	> 挺好的一个云服务网站，注册后可以免费领取10G免费空间等等，可以将博客中需要使用的图片上传到注册的服务器上，可以加快图片的加载速度，至少比在github上的速度快很多。

	- 七牛云账号创建和设置

    > 进入[七牛云官网][5]注册，按照流程进行，登陆后选择对象存储，此时可以选择绑定域名，在内容管理中可以上传图片，复制图片的链接即可在再网页上访问
	
    ### 2.评论功能

    - 具体请参考wiki：https://github.com/litten/hexo-theme-yilia/wiki/

    ### 3.统计功能

    ### (未完待续 ......)

------
  
  [1]: ../hexo+github创建个人博客--基础篇
  [2]: https://www.zhihu.com/question/24422335
  [3]: https://github.com/litten/hexo-theme-yilia
  [4]: http://ou36vgj5u.bkt.clouddn.com/image/blog/hexo+github%E5%88%9B%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2--%E6%B7%B1%E5%85%A5%E7%AF%87/hexo-yilia-pop.png
  [5]: https://www.qiniu.com/