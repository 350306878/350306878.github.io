---
layout:     post
title:      下载docker images非常慢，配置阿里云镜像加速器完美解决
subtitle:   fabric开发系列
date:       2019-02-20 
author:     陈剑
header-img: img/post-bg-alibaba.jpg
catalog: true
tags:
   
---

## 前言
最近一些学习fabirc的同学经常问我docker image下载时奇慢无比，并且中途会卡住甚至直接断开连接，具体原因当然是因为GWF搞得鬼，但是因为在ubuntu的终端里配置翻墙操作也比较麻烦，所以我推荐配置阿里云镜像加速器，实测下载速度非常快。

## 正文
首先注册一个阿里云账号

登陆以后点击右上角的控制台

<div >
<img src="https://img-blog.csdnimg.cn/20190220123523677.png" height="130" width="650">
</div>

然后在产品与服务中选择容器镜像服务

<div>
<img src="https://img-blog.csdnimg.cn/2019022012360099.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="600" width="400">
</div>

然后选择镜像加速器

<div >
<img src="https://img-blog.csdnimg.cn/20190220123658827.png" height="300" width="150">
</div>

会看到阿里云已经自动为你的账号分配了多个终端的加速器地址，每个人的都不一样

<div >
<img src="https://img-blog.csdnimg.cn/20190220123904763.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="450" width="450">
</div>

这时候打开ubuntu终端，并依次输入以下命令

<div >
<img src="https://img-blog.csdnimg.cn/20190220123847927.png" height="160" width="500">
</div>

然后重启一遍docker

<div >
<img src="https://img-blog.csdnimg.cn/20190220123950336.png" height="60" width="500">
</div>

这时候再使用./pull_images.sh命令拉取images就会异常顺利了！

最终差不多耗时20分钟可全部下载完毕

<div >
<img src="https://img-blog.csdnimg.cn/20190220124955843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="500" width="500">
</div>
