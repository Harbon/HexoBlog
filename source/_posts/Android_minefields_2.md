---
title: ubuntu 配置 nginx
tags: notes
categories:
- Linux
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;之前买了一台阿里云，用于搭建博客用，博客是搭好了，但访问时网址后面要加端口号，这个是个问题，感觉一点都不酷。想到 nginx 可以做反向代理转发，于是配置了 nginx.为了以后配置不用再查资料走坑，把整个配置步骤写下来作为笔记。

### 安装 nginx
因为是 ubuntu 系统，所以可以使用自带的 apt-get.
```
sudo apt-get install nginx
```
然后就是自动安装了，你不用管。如果网速慢的话可以去喝杯茶。

### 配置 nginx
安装好后进入 /etc/nginx/ 目录下修改配置文件，查看下 nginx.conf 文件，看看 http {} 中是否 include 了几个文件夹。一般是
```
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```
至于 conf.d 和 sites-enabled 的区别，只是为了分类而已。我选择编辑 sites-enabled 文件夹下的 default 配置文件。server{} 中加入如下代码：
```
        listen 80;

        server_name xxxxx;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                proxy_pass http://0.0.0.0:4000/;

                # Uncomment to enable naxsi on this location
                # include /etc/nginx/naxsi.rules
        }
```
因为需求比较简单只是单一端口的转发，所以只配置了一个 4000 端口，就是我的博客的监听端口。

### 检查配置文件正确性
执行如下命令检查配置文件语法的正确性：
```
nginx -t nginx.conf
```
如果有错会直接报错到具体行数

### 重新加载配置文件
如果修改了配置文件不需要重新启动 nginx 服务器，只需执行如下命令：
```
nginx -s reload
```
到此为止简单的端口转发完成了。
