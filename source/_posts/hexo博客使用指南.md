---
title: hexo博客使用指南
date: 2020-09-14 09:45:23
categories: 技术杂谈
tags: 工具
---

久了不更新博客，就不知道怎么搞环境了，换了环境又得查。索性自己写个简单的(•̀⌄•́)
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　——　by JiHan
* * *
<!-- more -->

可根据自己的需求进行快速定位，本文对应的发布环境是GitHub

### 从0-1搭建hexo网站
我就不做copper了，直接上链接：
[超详细Hexo+Github博客搭建小白教程](https://godweiyang.com/2018/04/13/hexo-blog/)
[hexo主题next](https://github.com/iissnan/hexo-theme-next)（我自己用的）
[hexo主题推荐](https://www.zhihu.com/question/24422335)
[hexo主题美化1](https://www.jianshu.com/p/3ff20be8574c)
[hexo主题美化2](https://blog.csdn.net/nightmare_dimple/article/details/86661502)
[评论系统](https://valine.js.org/)

### 已有博客，环境迁移
主要是相应环境安装：
1. node.js 官方下载
   windows直接点安装
   Linux下载解压后，配置路径`export PATH=$PATH:xxxx/node-v14.10.0-linux-x64/bin`
2. git安装
   windows直接点安装
   Liunx `sudo yum install git`或`sudo apt install git`
3. 安装hexo
   ```
   npm i hexo-cli -g
   
    #配置git扩展，在你自己的博客里肯定已经配置了项目地址关联
   npm i hexo-deployer-git
   ```
4. 可能有必要
   如果项目里没有node_modules文件夹，需要到博客根目录重新安装一波：`npm install`


### 已有环境，新发文
发文常用命令(通常都是顺序执行)：
```
# 新建文章
hexo new post "article title"
# 生成博客网页文件
hexo g  
# 本地预览博客
hexo s  
# 上传网页文件到github
hexo d  
```
博客源码备份：
```
hexo clean
git add -u
git commit -m "new post"
git push
```

### 可能遇到问题
**发布图片视频音频：**
图片发布很简单：
你在`hexo new post "xxx"`会在__posts下生成一个对应xxx.md和一个xxx文件夹。图片放在文件夹里，文章里引用：
```
![hello](xxx/image.jpg)
```
[视频发布](https://www.jianshu.com/p/26a7fc7cc185)
**github配置域名发布后被清空：**
在对应source目录下新建一个CNAME文件，只放置一行你指向的域名。例如：
```
# tree Blog/source/ -L 1
Blog/source/
|-- categories
|-- CNAME
|-- download
|-- _posts
|-- tags
`-- uploads
# cat Blog/source/CNAME 
xxx.com
```
