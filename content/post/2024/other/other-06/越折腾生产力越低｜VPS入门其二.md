



---
title: " 越折腾生产力越低｜VPS入门其二"
date: 2024-05-24T19:39:15+08:00
lastmod: 2024-05-24T19:39:15+08:00
description: 我是电脑高手！
tags:
- 越折腾生产力越低
- VPS
categories:
- others
image: 
slug: others-my-vps-02
---




## 给自建服务分发子域名

原本以为会很麻烦，没想到还挺简单的……对不起我能这么说是因为问了CHATGPT，并且要求它像对待小学生一样给我一步步地保姆级解释。以防万一（主要是防止我哪天忘了）把流程记下来。
### 1，配置DNS
我用的Dynadot（考虑到价格大概也不会换了），因此就是去Manage Domain里，在DNS设置的地方，加上一个子域名。类型选A，主机名是你想要挂在自己主域名前面的东西，值是你VPS的IP地址，TTL默认就好。

### 2， 配置NGINX
在/etc/nginx/sites-available/目录下创建配置文件，以我的MEMOS举例，就是memos_harushuura_vip。记住用下划线而不是点点来分割（哽住）。
文件里复制粘贴过去这样的东西

```
server {
    listen 80;
    server_name blog.example.com;  #这里也要改！

    location / {
        proxy_pass http://127.0.0.1:8080;  # 将IP和端口换成自己的
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 3，创建符号连接
在终端里输入下面这些东西，记得把网址改掉：
```
sudo ln -s /etc/nginx/sites-available/blog_example_com /etc/nginx/sites-enabled/
```

### 4， 打发一下NGINX
看看配置是否正确，不正确应该是前面的哪一步出错了：
```
sudo nginx -t
```
验证通过了就让他重新读一下数据：
```
sudo systemctl reload nginx
```
### 5，SSL（如果你安了Certbot）
```
sudo certbot --nginx -d blog.example.com
```
## MEMOS
好吧我还没把它放进博客页面里！但是我成功地给他发了一个子域名。并且惊喜地发现有一个插件能一键把FLOMO导出放进MEMOS，但是我正式转移到MEMOS大概要等MOEMEMO支持最新版了，现在的最新版因为API大更新不能用，遗憾。
## Dashboard
在Slash、OneNav、Dashy货比三家之后选择了最后那个。Slash要钱，OneNav丑，Dashy只能说还好但是可自定义程度最高。部署很简单，按照官网说明来就好。我在一通折腾之后，搞出来一个还比较满意的界面，预计要再把别的一些（主要是画画参考用的）网址整合进去。
![简约色彩](https://imagehost.harushuura.vip/image/2024/05/30/6658038e87857.jpg)
有点麻烦的事这个东西……他好像没有账号，导致随便什么人都可以改数据，不像SLASH能放出来。但是SLASH长得也报看，就算了。

## 骰子！！！！
折腾了俩小时成功部署上了，我是电脑高手！

因为海豹的文档写的很全面了，我就不当搬运工了，而且想来也没什么需求……

其实唯一遇上的BUG就是在docker-compose.yml里，文档里给出来的代码，需要把第一行的name:sealdice删掉。我也不知道为啥一直报错，删了之后就好使了。
然后登陆比较麻烦的是，每次登陆都要重启lagrange的容器，然后迅速地点进他的目录，把那张二维码找出来扫码登陆。现在只能祈祷腾讯别又风控了。

目前没什么别的需求，可以安心写博客画画了。希望Moe Memos能早点更新。