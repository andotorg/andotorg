---
title: GitHub利用Hexo搭建博客教程
date: 2018-02-20 12:43:23
tags: GitHub, Hexo, nodejs, javascript
---

### 安装Nodejs

nodo.js 下载与安装

下载地址：
官网
中文站
安装就不用多说了，跟安装普通软件没有区别，可以一路按提示Next。完成之后，打开命令行窗口 WIN + R，输入如下命令：

``` bash
node -v    
npm -v  
```

### 安装git

百度自行安装

### 安装 Hexo

在命令行输入如下命令:
> $ npm install -g hexo-cli

由于众所周知的原因,可能你执行这条命令会报错，如果你不会调整姿势上网，可以使用淘宝 npm 镜像，执行如下命令即可：

> $ npm install -g cnpm --registry=https://registry.npm.taobao.org

之后很多 npm 命令可以使用 cnpm 代替。执行下面命令即可安装 Hexo :

> $ cnpm install -g hexo-cli

可能出现一些 WARN ,可以不用理会，然后执行：

> $ cnpm install hexo --save

输入 

> $ hexo -v 

出现所示的版本信息，则说明安装成功

### 本地搭建博客

-初始化 hexo
新建一个文件夹，这个文件夹即为以后博客的文件存放路径。打开命令行，cd 到该文件夹，执行
> hexo init

-生成静态文件，执行：
> hexo g

OK,本地的博客搭建好了，下面可以执行 
> hexo s

进行本地调试，执行完之后，打开浏览器，输入 http://localhost:4000,可以访问就成功了
那么恭喜你，本地博客搭建已经完成了。下一步就是部署到远程服务器上。

### 将本地博客部署到远程服务器端

本地博客搭建好了，我们还需要将它部署到远程服务器上。因为博客主要都是一些静态的页面，不涉及与后台的数据传送，所以我们可以利用 Github 上提供的一个免费空间，那么，首先需要有一个 Github 账号，相信同行都有，非猿类请登录官网注册。做法是建立一个仓库，将文件上传到该仓库即可，这个仓库的起名要注意，为 yourname.github.io，yourname与你的注册用户名一致,比如的github用户名是 zhangsan，那么起名就为 zhangsan.github.io ,这个就是你博客的域名了。

下面还需要做相应的配置，在你项目的文件夹下，找到 _config.yml 文件，注意是修改整个项目的配置文件，不是主题文件夹下的配置文件。打开文件修改配置如下：
``` bash
deploy:    
  type: git     
  repo: <repository url>      
  branch: [branch]      
  message: [message]      
 ```
参数描述：

> repo： 库（Repository）地址

> branch： 分支名称。如果您使用的是 GitHub 或 GitCafe 的话，程序会尝试自动检测。

> message: 自定义提交信息 (默认为 Site updated: 'YYYY-MM-DD HH:mm:ss'
