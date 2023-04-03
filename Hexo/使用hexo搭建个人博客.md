---
title: 使用hexo搭建个人博客
date: 2023-04-03 17:53:55
categories: Hexo
tags: Hexo
---

### <center>使用hexo搭建个人博客</center>

#### 1、初始化

&emsp;&emsp;新建文件夹blog，在VSCode中打开blog，在命令行输入hexo init进行初始化，npm install安装依赖。用hexo-renderer-markdown-it-plus替换默认的markdown渲染引擎，避免emsp等渲染失败。

#### 2、图床

&emsp;&emsp;使用Nginx代理图片，将图片和md文件放在一起上传到git，做好目录管理。

#### 3、博客和源文件的管理

&emsp;&emsp;将hexo生成的public的文件夹上传到github，仓库名称为{username}.github.io，可以被github识别为博客网站。注意readme.md文件不能有#一级标题，否则访问github page不会展示index.html页面。
&emsp;&emsp;将hexo的source文件夹下的源码上传到另一个github仓库。
&emsp;&emsp;也可以使用同一个仓库的两个不同分支分别提交源文件和hexo生成的静态文件。

#### 4、hexo的配置

&emsp;&emsp;hexo自身的配置在_config.yml文件中，主题相关的配置在themes/next/_config.yml中。hexo支持categories、tags、搜索、社交等功能。

```js
hexo new page categories
hexo new page tags
```

&emsp;&emsp;使用next主题，clone源码并在themes/next/_config.yml中更换next的样式。

```shell
cd blog
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

#### 5、部署到个人服务器

&emsp;&emsp;clone仓库{username}.github.io，用Nginx代理访问。

#### 6、管理多个ssh账号

&emsp;&emsp;新建~/.ssh/config文件，按照ssh协议规定的格式记录相关账号信息。
