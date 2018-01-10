---
title: Hexo+Github Pages搭建个人博客
date: 2017-02-24 00:30:36
categories: [教程, HEXO]
tags: [HEXO]
---
### 写在前面
一直想写博客，之前对看过的知识没进行总结，很多知识点，看过即忘，每次都要再重新去找，显得麻烦，又不利于自己的提高，在偷懒了n久之后，就以这篇搭建自己博客开始博客生涯。
### Hexo
[Hexo](https://hexo.io)是一个快速，简单、强大的基于Node的静态博客框架。你只需要通过[Markdown](http://markdown-zh.readthedocs.io/en/latest/)来写文章，就可以通过Hexo快速生成静态文件，并把静态文件push到github上，就可以拥有自己的个人博客啦。它还有一些很好看的[主题](https://hexo.io/themes/)可以供你选择。
### Hexo安装
#### 环境
Hexo基于Node，所以必须安装[Node](https://nodejs.org/en/download/)。
如果你需要托管到Github Pages, 还需要安装[git](https://git-scm.com/), mac上如果你安装完xcode的话，就会有git了。
#### 安装
安装完Node之后
```
sudo npm install hexo-cli -g
hexo init "blog"
cd blog
npm install
hexo server
```
逐步安装后，网站就建好了，你的个人博客就启动起来啦！[这里可以了解更多配置](https://hexo.io/zh-cn/docs/setup.html)
### GitHub Pages
GitHub Pages可以被认为是用户编写的、托管在github上的静态网页。由于它的空间免费稳定，可以用它来介绍你托管在github上的项目，或者搭建自己的网站。
### GitHub Pages配置
申请一个github账号
创建代码仓库，并且以yourname.github.io命名。如图
<img src="/images/create_github_io.jpeg" style="width: 600px;">
### Hexo与GitHub Pages
想要把本地的Hexo发布的git上面，只要在你的根目录下，找到_config.yml,并增加如下配置就可以了
```
deploy:
  type: git
  repo: https://github.com/vinlyn/vinlyn.github.io.git
  branch: master
```
repo地址为上面新建的代码仓库yourname.github.io的地址。
<img src="/images/repo_address.jpeg" style="width: 600px;">
添加完配置后，你只需要在hexo目录下跑下命令，就可以了。
```
hexo clean
hexo g
hexo d
```
命令解释，1.清缓存，2.生成本地发布文件夹，3.发布到Github Pages上。[更多命令](https://hexo.io/docs/commands.html)
然后这时候，你只要在浏览器地址上输入yourname.github.io, 你的博客就出来啦。
### 绑定个人域名
#### 个人域名
本人域名是上[godaddy](https://www.godaddy.com/)上买的，个人觉得还不错。
#### 域名配置
第一步：在博客的根目录下找到source文件夹，并创建文件CNAME，在CNAME添加你的域名。如图
<img src="/images/cname.jpeg" style="width: 600px;">
添加完后执行命令hexo d -g。(把这个文件发布github上面)
第二步：在你购买的网站里面进入DNS管理对DNS设置，将DNS修改为修改你域名的DNS地址为f1g1ns1.dnspod.net和f1g1ns2.dnspod.net，如果是其他网站购买的，也进行同样的配置。
<img src="/images/dns_setting.jpeg" style="width: 600px;">
第三步：注册[DNSPod](https://www.dnspod.cn)(提供专业的第三方域名解析服务)，然后添加域名，添加响应的记录。如图：
<img src="/images/dnspod_setting.jpeg" style="width: 600px;">
添加完后，要过段时间才能生效，过段时间后，就可以使用你的域名访问你在github上的博客啦。
### 最后
在配置个人域名上踩了许多的坑，希望这个简单的教程，对后面的人会有用，也是自己配置过程的一个记录。hexo还有挺多好玩的东西，如主题的配置等，也可以配置主题后，自己对layout进行修改等，有兴趣的话，可以查看官方文档，然后自己研究。文章就到这~[示例地址](https://github.com/vinlyn/webpack-test)



