---
layout:     post
title:      ubuntu配置hyperledger的血泪经验！耗时四天，卸载6次系统的爬坑经历
subtitle:   有史以来遇到过最难配置的环境
date:       2019-01-11 
author:     陈剑
header-img: img/post-bg-debug.png
catalog: true
tags:
   
---


众所周知上手任何一个新技术，配置环境绝对是最能消磨打击积极性的一步，这次hyperledger是我有史以来爬坑最久，多次面临放弃最后还是坚持下来的一次，总共耗时四天，卸载6次系统，最终成功上岸。

首先我选择了使用VMware虚拟机搭建的ubuntu18.04.1的系统（centos也可以，只不过ubuntu用的多一点）
首先第一步是最重要的，一定要提前把Go环境配置好！我因为一开始忽略了Go这个问题导致做了两天的无用功，最后还是在一个不知名的论坛偶然看到有人提到了Go环境的配置，我才想到我的问题可能是出在这了。
## 一.Go的配置与安装


这一步我栽倒在了非常多的教程里，我发现现在一堆无良程序员到处复制粘贴别人的文章，揉成一篇发上去，压根不管能不能用有没有坑，害得我被这些教程带跑了很多弯路！

首先第一点，很多教程里说用

wget https://storage.googleapis.com/golang/go1.9.linux-amd64.tar.gz

来下载Go安装包，但是却压根没说这玩意是需要翻墙的，并且ubuntu能够支持的翻墙软件极少，所以这里需要先把这个压缩包下载好后，再拷贝到ubuntu里，我的主机是可以翻墙的，于是我是先用主机下载好这个链接的压缩包，然后再拖拽（这里需要VMware toos支持）ubuntu的home主目录里，然后使用

sudo tar -C /usr/local -xzf go1.9.linux-amd64.tar.gz

来解压这个包，大家不要在意/usr/local这个路径，我刚开始被这个路径绕进去了，因为之前一直没有使用过ubuntu系统，死活再home主目录里找不到这个路径在哪，一直以为没有解压缩成功，最后发现是在computer这个目录里，如下图所示

<div align="center">
<img src="https://img-blog.csdnimg.cn/20190109180157155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="450" width="600">
</div>


解压缩完了以后，输入

vi ~/.profile

然后在最下方输入

export PATH=$PATH:/usr/local/go/bin  

export GOROOT=/usr/local/go  

export GOPATH=$HOME/go  

export PATH=$PATH:$HOME/go/bin

进行环境配置，这里又是一个坑！对于不熟悉ubuntu系统的同学这里全是坑啊！这个问题折腾了我近三个小时时间，因为中途中尝试了很多办法，所以现在也记不清具体是哪一个办法解决了问题，我尽量回忆一下问题的出现原因和解决过程。

vi打开的文件是无法直接进行编辑的，导致我刚开始死活不知道该怎么在最下面复制这四行内容，然后百度到方法以后（大家自行百度），终于可以在下方插入字符了，但是又不知道该怎么保存了...最后也是搜到说是要再输入

 ：wq 

就可以保存并退出，然后输入以后果真有效，接下来要载入环境变量，输入

source ~/.profile

坑又来了！一直提示我31行有误，可是我打开刚才的那个文件压根就只有30行，哪来的31行啊，最后折腾了我好一会才发现，原来刚才复制配置环境变量的sd那四行代码的时候，第一行没有另起一行，而是和fi给紧挨到一起了，我估摸着原因就是它，那就另起一行就好了啊，结果发现不管怎么按回车...它就是不动，然后也是百度了好一会说是要同时按 Esc和字幕O才可以换行，试了下果真可以了。

然后

cd ~

回到主目录，这时候要创建一个 go 文件，输入

mkdir go

创建完成，这时候输入

go version 查看是否有版本号出现，如果有的话就配置成功，你以为这就完了吗？坑又来了，只要你关闭命令行后，你就必须要重新载入一次环境变量才可以！我之前一直进行到最后一步报错，从来没想到问题会出在这，折腾了一下午，无意间试着再看看go的版本，结果发现输入go version后无版本号，最后才发现必须每次打开命令行都需要载入一遍环境变量才能生效！

## 二.docker的配置

go配置完后，接下来开始配置docker，输入

curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -

这里需要将当前用户添加到docker组里，要不然后面会经常提示无权限，依次执行下面四步

sudo groupadd docker

sudo gpasswd -a ${USER} docker

sudo service docker restart

newgrp - docker

结束以后，需要添加docker镜像，要不然后面下载image会极其的慢，我刚开始不知道，开着电脑一天一夜才下载了7%。

首先你需要有一个自己的阿里云镜像地址，这里我也是折腾了好久才找到，先登录阿里云，然后点击右上角的控制台

<div align="center">
<img src="https://img-blog.csdnimg.cn/20190109182506457.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="400" width="300">
</div>

进入后，点击镜像加速器，可以看到给你的ubuntu的镜像加速地址

<div align="center">
<img src="https://img-blog.csdnimg.cn/2019010918271135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="400" width="400">
</div>

然后建立docker文件

sudo mkdir -p /etc/docker

输入

sudo gedit /etc/docker/daemon.json 打开配置文件

在里面输入刚才阿里云给你的那个地址，上下大括号要有

{  

这里是阿里云给你的地址

}

保存后退出，然后依次输入

sudo systemctl daemon-reload

sudo systemctl restart docker

最后输入docker version查看是否配置成功。

<div align="center">
<img src="https://img-blog.csdn.net/20171026190426611?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc281NDE4NDE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" height="250" width="550">
</div>

如果只显示client而server那里显示的没有权限，则在输入docker version 前加上sudo就好。

## 三.docker-compose的配置

接下来安装Docker-Compose。这里也有坑，要注意，为了保险起见一定要安装1.12版本，我刚开始安装的是最新版，结果走到最后一步一直报错，折腾了好久才发现原来是docker-compose的版本比docker新太多了。

首先安装python-pip，输入

sudo apt-get install python-pip

然后输入

curl -L https://get.daocloud.io/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > ~/docker-compose

这里实名diss一下csdn账号名为 馬McccccL的博主，它的教程里将上面链接的`uname -s`-`uname -m`头上的`那一小撇给漏了，导致我一直下载不下来，最后才发现是漏了两个小撇，刚开始以为这两个小撇是引号，加上后依然不行，最后仔细对比才发现不是引号，而是和~同一个按键的`!你说这到哪说理去...

然后依次输入

sudo mv ~/docker-compose /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

最后查看版本是否安装成功

docker-compose version

<div align="center">
<img src="https://img-blog.csdn.net/20171026192503643?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc281NDE4NDE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" height="130" width="550">
</div>

## 四.下载fabric源码

接下来下载fabric源码

首先建立源码存放地址

mkdir –p ~/go/src/github.com/hyperledger

如果上面这行代码无法直接建立地址时，一个一个mkdir和cd手动建立吧。

cd ~/go/src/github.com/hyperledger

git clone https://github.com/hyperledger/fabric.git

cd ~/go/src/github.com/hyperledger/fabric 

接下来要注意，切换到1.0.0版本，要不后面会出错

git checkout v1.0.0

## 五.下载fabric docker镜像

接下来下载Fabric Docker镜像

这里如果刚才没有配置阿里云镜像加速的话，会非常慢！一定要配置

cd ~/go/src/github.com/hyperledger/fabric/examples/e2e_cli/

接下来进行下载，如果刚才没有添加用户docker组权限的话，下面的这行代码会无法运行，一定记得添加

source download-dockerimages.sh -c x86_64-1.0.0 -f x86_64-1.0.0

差不多十分钟左右下载好，然后输入

docker images

如果出现下图，说明搞定了

<div align="center">
<img src="https://img-blog.csdn.net/20171026195419555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc281NDE4NDE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" height="230" width="550">
</div>

最后一步，运行网络，上岸

最后，停留在这个路径里不要动，输入

./network_setup.sh up

静静地等待编译吧，运气好的话，三到五分钟左右编译完成，如果看到下图，出现大大的END三个字母，说明你成功上岸了。

<div align="center">
<img src="https://img-blog.csdnimg.cn/20190109184729978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdWthb3lpbmc2Njc0,size_16,color_FFFFFF,t_70" height="400" width="550">
</div>


