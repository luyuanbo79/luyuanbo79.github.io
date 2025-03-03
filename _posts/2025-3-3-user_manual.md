---
layout: post
title: "docker命令大全"
date:   2024-3-3
tags: [文章]
comments: true
author: luyuanbo
---

# 一、启动类
## 1.启动docker
systemctl start docker
## 2.关闭docker
systemctl stop docker
## 3.重新启动docker
systemctl restart docker
## 4.docker设置自启动
systemctl enable docker
## 5.查看docker运行状态
systemctl status docker
## 6.查看docker版本号等信息
docker version
或者
docker info
该命令还可以查看到有多少 容器及其状态 和 镜像 的信息

# 二、镜像类
## 1.查看镜像
docker images
## 2.搜索镜像
docker search [OPTIONS] 镜像名字
docker search mysql
## 3.拉取镜像
docker pull
docker pull mysql #没有制定版本则默认最新版 
docker官方镜像地址

##4.运行镜像
docker run
docker run tomcat
运行镜像后可以按 ctrl+c 退出

## 5.删除镜像
docker rmi  镜像名/镜像ID #若镜像在运行则会报错
docker rmi -f 镜像名/镜像ID #强制删除一个
docker rmi -f mysql
 
docker rmi -f 镜像名/镜像ID 镜像名/镜像ID 镜像名/镜像ID #删除多个 其镜像ID或镜像用用空格隔开即可 
docker rmi -f mysql redis
 
docker rmi -f $(docker images -aq)
#删除全部镜像  -a 意思为显示全部, -q 意思为只显示ID
## 6.加载镜像
docker load -i 镜像保存文件位置
docker load myimage.tar
## 7. 保存镜像
docker save 镜像名/镜像ID -o 镜像保存位置和名字
docker save tomcat -o /myimage.tar
# 三、容器类
## 1.查看正在运行的容器
docker ps
docker ps -a # 查看所有容器
#加格式化方式访问，格式会更加清爽
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"
## 2.创建容器
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
常用参数：
--name=NAME   #为容器指定名字为NAME，不使用的话系统自动为容器命名
-d: 后台运行容器并返回容器ID，也即启动守护式容器(后台运行)；
 
-i：以交互模式运行容器，通常与 -t 同时使用；
-t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；
也即启动交互式容器(前台有伪终端，等待交互，一般连用，即-it)；
 
-P: 随机端口映射，大写P
-p: 指定端口映射，小写p
 
# 创建并允许 Nginx 容器
docker run -d --name nginx -p 80:80 nginx
## 3.启动守护式容器（后台运行）
docker run -d 容器名
docker run -d redis:6.0.8
## 4.停止容器
docker stop 容器名
docker stop nginx
## 5.启动容器
docker start 容器名
docker start nginx
docker restart 容器名
docker restart nginx
 
## 6.进入正在运行的容器
docker exec -it 容器名 bashshell
docker exec -it nginx /bin/bash```
## 7.停止容器
docker stop 容器名
docker stop nginx
## 8.强制停止容器
docker kill 容器名
docker kill nginx
## 9.删除容器
#删除一个
docker rm 容器ID  
docker rm nginx
docker rm -f 容器ID  #强制删除
docker rm -f nginx
 
 
#删除多个
docker rm -f $(docker ps -a -q)
或
docker ps -a -q | xargs docker rm
## 10.查看容器日志
docker logs 容器名
docker logs nginx
## 11.查看容器内运行的进程
docker top 容器名
docker top nginx
## 12.查看容器内部细节
docker inspect 容器名
docker inspect nginx
## 13.创建容器数据卷挂载
创建容器并指定数据卷，注意通过 -v 参数来指定数据卷
docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx
## 14.查看数据卷
docker volume ls
## 15.查看数据卷详情
docker volume inspect 数据卷名
docker volume inspect html
## 16.删除数据卷
docker volume rm 数据卷名
docker volume rm html
# 四.网络类
## 1.查看网络
docker network ls
## 2.创建网络
docker network create 网络名
docker network create hmall
##?3.查看网络数据源
docker network inspect 网络名
docker network inspect hmall
## 4.删除网络
docker network rm 网络名
docker nerwork rm hmall
## 五、Docker compose
官方文档：基本语法

假设docker run 部署 mysql命令如下

docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  --network hmall
  mysql
那么用docker-compose.yml 文件定义就是：

version: "3.8"
 
services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
    networks:
      - new
networks:
  new:
    name: hmall
## 1.查看帮助
docker-compose -h
## 2.启动所有服务
docker-compose up
docker-compose up -d # 后台运行
## 3.停止并删除容器、网络、卷、镜像。
docker-compose down
## 4.进入容器实例内部
docker-compose exec  yml里面的服务id
## 5.展示容器
ocker-compose ps
## 6.展示进程
docker-compose top
## 7.查看容器输出日志
docker-compose logs  yml里面的服务id
## 8.检查配置
docker-compose config
docker-compose config -q # 检查配置，有问题才有输出
## 9.启动服务
docker-compose start
## 10.重启服务
docker-compose restart
## 11.停止服务
docker-compose stop
# 六•其他
## 1.命令别名
# 修改/root/.bashrc文件
vi /root/.bashrc
内容如下：
# .bashrc
 
# User specific aliases and functions
 
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'
 
# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
#退出
exit
:wq
 
#执行命令使别名生效
source /root/.bashrc
八。Docker更换国内镜像源
#!/bin/bash
 
# 创建 /etc/docker 目录，如果目录已存在则不报错
sudo mkdir -p /etc/docker
 
# 写入 daemon.json 文件内容
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn",
        "https://cr.console.aliyun.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://hub-mirror.c.163.com",
        "https://hub.daocloud.io",
        "http://mirror.azure.cn",
        "https://mirror.baidubce.com",
        "https://docker.mirrors.sjtug.sjtu.edu.cn",
        "https://docker.nju.edu.cn",
        "https://docker.hpcloud.cloud",
        "https://docker.m.daocloud.io",
        "https://docker.unsee.tech",
        "https://docker.1panel.live",
        "http://mirrors.ustc.edu.cn",
        "https://docker.chenby.cn",
        "http://mirror.azure.cn",
        "https://dockerpull.org",
        "https://dockerhub.icu",
        "https://hub.rat.dev",
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn",
        "https://cr.console.aliyun.com",
        "https://ccr.ccs.tencentyun.com",
        "https://dockerproxy.com",
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com",
        "https://hubgw.docker.com"
    ]
}
EOF
 
# 重新加载 systemd 配置
sudo systemctl daemon-reload
 
# 重启 Docker 服务
sudo systemctl restart docker
【Docker】Docker常用命令总结_查看docker启动命令
1、启动docker:systemctl start docker 2、停止docker:systemctl stop docker 3、重启docker:systemctl restart docker 4、查看docker状态:systemctl status docker 5、查看docker概要信息:docker info 6、查看docker总体帮助文档:docker --help 7、查看具体命令的帮助文档:docker 具体命令 --help 8、开机启动:systemct...
继续访问
Docker常用命令_docker 启动命令
启动docker: systemctl start docker 停止docker: systemctl stop docker 重启docker: systemctl restart docker 查看docker状态: systemctl status docker 开机启动: systemctlenabledocker 查看docker概要信息: docker info 查看docker总体帮助文档: docker --help 查看docker命令帮助文档: docker 具体命令 --help 1.2、...
继续访问
docker的常用基本命令
docker的常用基本命令
继续访问

Docker 常用命令大全！！
docker 命令大全
继续访问

启动的docker容器里默认运行dockerd_docker默认启动
docker.service 文件在Dockerfile 里COPY, 再enable # Dockerfile增加 COPY docker.service /etc/systemd/system/docker.service RUN systemctl enable docker.service 1 2 3 这样docker run xxx 启动的容器, dockerd 就能默认启动了 参考
继续访问
Docker常用命令总结_docker启动命令
#利用docker load命令可以将镜像导出的打包或压缩文件再导入 格式:docker load -i tar文件路径 选项:-i:输入字符串从tar存档文件读取 用法: #查看本地镜像列表 [root@centos7-temp ~]# docker images REPOSITORY TAG IMAGE ID CREATED SIZE #导入镜像 [root@centos7-temp ~]# docker load -i /data/nginx.t...
继续访问
Docker常用命令
docker常用命令
继续访问

Docker常用命令汇总（100条）
管理不同的Docker守护进程上下文。- 搜索一个Docker仓库中的镜像。- 显示Docker守护进程的事件。- 显示Docker守护进程的信息。- 启动Docker后台守护进程。- 从一个压缩文件中加载一个镜像。- 登录一个Docker镜像仓库。- 登录到一个Docker仓库。- 查看Docker的系统信息。- 查看Docker的版本信息。- 查看容器中正在运行的进程。- 从一个归档文件中加载镜像。- 将一个镜像打包为归档文件。- 列出所有正在运行的容器。- 导入一个tar包为镜像。
继续访问

Docker基础操作指南:命令、镜像与容器管理
一、Docker 基础命令 1、启动docker systemctl start docker 2、关闭 docker systemctl stop docker 3、重启 docker systemctl restart docker 4、设置开机启动 docker systemctlenabledocker 5、查看 docker 运行状态 systemctl status docker 6、查看 docker 版本信息 ...
继续访问
docker常用命令及设置开机自启_docker开机自启动命令
常用基本命令 # 启动docker systemctl start docker # 停止dokcer systemctl stop docker # 查看docker状态 systemctl status docker # 重启docker systemctl restart docker # 设置docker 开机自启 systemctl enable docker # 查看docker 版本 docker version # 查看docker 镜像 docker images # 查看docker 运行列表 ...
继续访问
热门推荐 docker常用命令汇总
在开发过程中，经常涉及到 docker 的相关操作，本文对常用的指令进行汇总
继续访问

Docker 常用命令大全.pdf
Docker在操作的过程中，会遇到很多的命令，每一次使用查询起来都比较麻烦，所以我整理了一份Docker 常用命令大全分享给大家，这样在实用的过程中，直接在文档搜索就可以了，更加的便利，需要的小伙伴可以自行下载。
Docker命令详解
一、帮助启动命令 启动docker:systemctl start docker 停止docker:systemctl stop docker 重启docker:systemctl restart docker 查看docker状态:systemctl status docker 开机启动:systemctl enable docker 查看docker概要信息:docker info 查看docker总体帮助文档:docker --help ...
继续访问
...操作命令(启动类,镜像命令,容器命令)_docker启动镜像
systemctl status docker # docker开机启动 systemctlenabledocker # 查看docker概要信息 docker info # 查看docker总体帮助文档 docker --help # 查看docker命令帮助文档 docker 命令 --help 二、镜像命令 1、列出本地主机上的镜像 # 列出本地主机上的镜像 ...
继续访问
最新发布 docker命令大全.zip
Docker命令大全涵盖了Docker操作的各个方面，从启动Docker服务、管理镜像、操作容器到配置网络和数据卷等。下面将详细解析Docker命令大全，帮助用户更好地理解和使用Docker。
Docker命令大全
docker常用命令，很适合初学者，希望能帮到大家 容器操作 ps inspect top attach events logs wait export port
Docker 常用命令(三)_systemctl start docker
一、帮助命令 # 显示 Docker 版本信息。 docker version # 显示 Docker 系统信息,包括镜像和容器数。 docker info # 帮助 docker --help # 启动 docker 后台服务 sudo systemctl start docker # 关闭 docker 后台服务 # 如果提示“Warning: Stopping docker.service, but it can still be activated by: docker...
继续访问
docker命令大全集合
docker命令集合，集中的列出了所有的docker操作的命令，以及相关的命令说明; 包含： 服务器启动， docker系统信息， docker镜像的各种操作， 日常运行的各种命令以及组合命令 阅读建议： 保存一份到项目的readme中，...
精华！Docker 常用命令大全
点击上方“小白学视觉”，选择加"星标"或“置顶”重磅干货，第一时间送达本文包括容器生命周期管理命令、容器操作命令、容器rootfs命令、镜像仓库命令、本地镜像管理命令和基础版本信息命令。容器生命周期管理命令run创建一个新的容器。#使用docker镜像nginx:latest以后台模式启动一个容器, #并将容器命名为mynginx。 dockerrun--namemynginx-d...
继续访问

Docker常用命令（三）
官方帮助文档： https://docs.docker.com/reference/ 帮助命令 docker version # 显示docker版本信息 docker info # 显示docker系统系统信息，镜像以及容器数量等信息 docker 命令 --help # 帮助命令 镜像命令 docker images 查看本地的所有镜像 # docker images 查看本地的所有镜像 [root@izuf6akcgealirj602cmxsz /]# docker images REPOS
继续访问

docker 常用命令总结
注意 stop，restart后的容器，或者commit后的镜像启动环境变量需要重新加载，比如 source /etc/source 我们运行docker容器的时候，使用了-d参数，把容器在后台运行后。 1.启动docker docker run -d -i -t <imageID> /bin/bash 上述命令执行某些命令回报错Failed to get D-Bus co...
继续访问
Web安全和渗透测试有什么关系？
做渗透测试的一个环节就是测试web安全，需要明白漏洞产生原理，通过信息收集互联网暴露面，进行漏洞扫描，漏洞利用，必要时进行脚本自编写和手工测试，力求挖出目标存在的漏洞并提出整改建议，当然如果技术再精一些，还要学习内网渗透（工作组和域环境），白盒审计，app，小程序渗透那些了......可以说是最科学最系统的学习路线，大家跟着这个大的方向学习准没问题。当然除了有配套的视频，同时也为大家整理了各种文档和书籍资料&工具，并且已经帮大家分好类了。总之，web安全包含于渗透测试，但不是渗透测试的全部。
继续访问



以下是为你整理的上述内容的Markdown格式代码：
 
热门频道
 
- 首页
- 博客
- 研修院
- VIP
- APP
- 问答
- 下载
- 社区
 
推荐频道
 
- 活动
- 招聘
- 专题
 
打开CSDN APP
Copyright © 1999-2020, CSDN.NET, All Rights Reserved
 
打开APP
 
docker命令大全 原创
 
公开
 
2025-02-16 19:22:08
阅读量：404
收藏：5
赞：3
 
码龄144天
 
一、启动类
 
启动 docker： systemctl start docker 
关闭 docker： systemctl stop docker 
重新启动 docker： systemctl restart docker 
docker 设置自启动： systemctl enable docker 
查看 docker 运行状态： systemctl status docker 
查看 docker 版本号等信息：
-  docker version 
- 或者  docker info （该命令还可以查看到有多少容器及其状态和镜像的信息）
 
二、 镜像类
 
查看镜像： docker images 
搜索镜像： docker search [OPTIONS] 镜像名字 ，例如  docker search mysql 
拉取镜像：
-  docker pull 
-  docker pull mysql （没有制定版本则默认最新版 ）
- docker官方镜像地址
运行镜像：
-  docker run 
-  docker run tomcat （运行镜像后可以按 ctrl+c 退出）
删除镜像：
-  docker rmi 镜像名/镜像ID （若镜像在运行则会报错）
-  docker rmi -f 镜像名/镜像ID （强制删除一个），例如  docker rmi -f mysql 
-  docker rmi -f 镜像名/镜像ID 镜像名/镜像ID 镜像名/镜像ID （删除多个 其镜像ID或镜像用用空格隔开即可 ），例如  docker rmi -f mysql redis 
-  docker rmi -f $(docker images -aq) （删除全部镜像 ， -a  意思为显示全部， -q  意思为只显示ID）
加载镜像： docker load -i 镜像保存文件位置 ，例如  docker load myimage.tar 
保存镜像： docker save 镜像名/镜像ID -o 镜像保存位置和名字 ，例如  docker save tomcat -o /myimage.tar 
 
三、容器类
 
查看正在运行的容器：
-  docker ps 
-  docker ps -a （查看所有容器）
- 加格式化方式访问，格式会更加清爽： docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}" 
创建容器： docker run [OPTIONS] IMAGE [COMMAND] [ARG...] 
- 常用参数：
-  --name=NAME ：为容器指定名字为NAME，不使用的话系统自动为容器命名
-  -d ：后台运行容器并返回容器ID，也即启动守护式容器(后台运行)；
-  -i ：以交互模式运行容器，通常与  -t  同时使用；
-  -t ：为容器重新分配一个伪输入终端，通常与  -i  同时使用；也即启动交互式容器(前台有伪终端，等待交互，一般连用，即-it)；
-  -P ：随机端口映射，大写P
-  -p ：指定端口映射，小写p
- 创建并允许 Nginx 容器： docker run -d --name nginx -p 80:80 nginx 
启动守护式容器（后台运行）： docker run -d 容器名 ，例如  docker run -d redis:6.0.8 
停止容器： docker stop 容器名 ，例如  docker stop nginx 
启动容器：
-  docker start 容器名 ，例如  docker start nginx 
-  docker restart 容器名 ，例如  docker restart nginx 
进入正在运行的容器： docker exec -it 容器名 bashshell ，例如  docker exec -it nginx /bin/bash 
停止容器： docker stop 容器名 ，例如  docker stop nginx 
强制停止容器： docker kill 容器名 ，例如  docker kill nginx 
删除容器：
- 删除一个：
-  docker rm 容器ID  ，例如  docker rm nginx 
-  docker rm -f 容器ID （强制删除），例如  docker rm -f nginx 
- 删除多个：
-  docker rm -f $(docker ps -a -q) 
- 或  docker ps -a -q | xargs docker rm 
查看容器日志： docker logs 容器名 ，例如  docker logs nginx 
查看容器内运行的进程： docker top 容器名 ，例如  docker top nginx 
查看容器内部细节： docker inspect 容器名 ，例如  docker inspect nginx 
创建容器数据卷挂载：创建容器并指定数据卷，注意通过  -v  参数来指定数据卷，例如  docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx 
查看数据卷： docker volume ls 
查看数据卷详情： docker volume inspect 数据卷名 ，例如  docker volume inspect html 
删除数据卷： docker volume rm 数据卷名 ，例如  docker volume rm html 
 
四. 网络类
 
查看网络： docker network ls 
创建网络： docker network create 网络名 ，例如  docker network create hmall 
查看网络数据源： docker network inspect 网络名 ，例如  docker network inspect hmall 
删除网络： docker network rm 网络名 ，例如  docker nerwork rm hmall 
 
五、 Docker compose
 
官方文档：基本语法
 
假设docker run 部署 mysql命令如下：
 
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  --network hmall
  mysql
 
 
那么用docker-compose.yml 文件定义就是：
 
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
    networks:
      - new
networks:
  new:
    name: hmall
 
 
查看帮助： docker-compose -h 
启动所有服务：
-  docker-compose up 
-  docker-compose up -d （后台运行）
停止并删除容器、网络、卷、镜像： docker-compose down 
进入容器实例内部： docker-compose exec yml里面的服务id 
展示容器： docker-compose ps 
展示进程： docker-compose top 
查看容器输出日志： docker-compose logs yml里面的服务id 
检查配置：
-  docker-compose config 
-  docker-compose config -q （检查配置，有问题才有输出）
启动服务： docker-compose start 
重启服务： docker-compose restart 
停止服务： docker-compose stop 
 
六、 其他
 
命令别名：
- 修改/root/.bashrc文件： vi /root/.bashrc 
- 内容如下：
 
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'

# Source global definitions
if [ -f /etc/bashrc ]; then
       . /etc/bashrc
fi
 
 
- 退出： exit 
- 保存： :wq 
- 执行命令使别名生效： source /root/.bashrc 
 
八。Docker更换国内镜像源
 
#!/bin/bash

# 创建 /etc/docker 目录，如果目录已存在则不报错
sudo mkdir -p /etc/docker

# 写入 daemon.json 文件内容
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn",
        "https://cr.console.aliyun.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://hub-mirror.c.163.com",
        "https://hub.daocloud.io",
        "http://mirror.azure.cn",
        "https://mirror.baidubce.com",
        "https://docker.mirrors.sjtug.sjtu.edu.cn",
        "https://docker.nju.edu.cn",
        "https://docker.hpcloud.cloud",
        "https://docker.m.daocloud.io",
        "https://docker.unsee.tech",
        "https://docker.1panel.live",
        "http://mirrors.ustc.edu.cn",
        "https://docker.chenby.cn",
        "http://mirror.azure.cn",
        "https://dockerpull.org",
        "https://dockerhub.icu",
        "https://hub.rat.dev",
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn",
        "https://cr.console.aliyun.com",
        "https://ccr.ccs.tencentyun.com",
        "https://dockerproxy.com",
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com",
        "https://hubgw.docker.com"
    ]
}
EOF

# 重新加载 systemd 配置
sudo systemctl daemon-reload

# 重启 Docker 服务
sudo systemctl restart docker
 
 
【Docker】Docker常用命令总结_查看docker启动命令
1、启动docker: systemctl start docker 
2、停止docker: systemctl stop docker 
3、重启docker: systemctl restart docker 
4、查看docker状态: systemctl status docker 
5、查看docker概要信息: docker info 
6、查看docker总体帮助文档: docker --help 
7、查看具体命令的帮助文档: docker 具体命令 --help 
8、开机启动: systemct... 
继续访问
 
Docker常用命令_docker 启动命令
启动docker:  systemctl start docker 
停止docker:  systemctl stop docker 
重启docker:  systemctl restart docker 
查看docker状态:  systemctl status docker 
开机启动:  systemctlenabledocker 
查看docker概要信息:  docker info 
查看docker总体帮助文档:  docker --help 
查看docker命令帮助文档:  docker 具体命令 --help 
1.2、...
继续访问
 
docker的常用基本命令
docker的常用基本命令
继续访问
 
Docker 常用命令大全！！
docker 命令大全
继续访问
 
启动的docker容器里默认运行dockerd_docker默认启动
docker.service 文件在Dockerfile 里COPY, 再enable
 
Dockerfile增加
 
COPY docker.service /etc/systemd/system/docker.service
RUN systemctl enable docker.service
1 2 3
这样docker run xxx 启动的容器, dockerd 就能默认启动了
参考
继续访问
 
Docker常用命令总结_docker启动命令
#利用docker load命令可以将镜像导出的打包或压缩文件再导入
格式:docker load -i tar文件路径
选项:-i:输入字符串从tar存档文件读取
用法:
#查看本地镜像列表
[root@centos7-temp ~]# docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
#导入镜像
[root@centos7-temp ~]# docker load -i /data/nginx.t...
继续访问
 
Docker常用命令
docker常用命令
继续访问
 
Docker常用命令汇总（100条）
管理不同的Docker守护进程上下文。- 搜索一个Docker仓库中的镜像。- 显示Docker守护进程的事件。- 显示Docker守护进程的信息。- 启动Docker后台守护进程。- 从一个压缩文件中加载一个镜像。- 登录一个Docker镜像仓库。- 登录到一个Docker仓库。- 查看Docker的系统信息。- 查看Docker的版本信息。- 查看容器中正在运行的进程。- 从一个归档文件中加载镜像。- 将一个镜像打包为归档文件。- 列出所有正在运行的容器。- 导入一个tar包为镜像。
继续访问
 
Docker基础操作指南:命令、镜像与容器管理
一、Docker 基础命令
1、启动docker  systemctl start docker 
2、关闭 docker  systemctl stop docker 
3、重启 docker  systemctl restart docker 
4、设置开机启动 docker  systemctlenabledocker 
5、查看 docker 运行状态  systemctl status docker 
6、查看 docker 版本信息 ...
继续访问
 
docker常用命令及设置开机自启_docker开机自启动命令
常用基本命令
 
启动docker  systemctl start docker 
 
停止dokcer  systemctl stop docker 
 
查看docker状态  systemctl status docker 
 
重启docker  systemctl restart docker 
 
设置docker 开机自启  systemctl enable docker 
 
查看docker 版本  docker version 
 
查看docker 镜像  docker images 
 
查看docker 运行列表 ...
 
继续访问
 
热门推荐 docker常用命令汇总
在开发过程中，经常涉及到 docker 的相关操作，本文对常用的指令进行汇总
继续访问
 
Docker 常用命令大全.pdf
Docker在操作的过程中，会遇到很多的命令，每一次使用查询起来都比较麻烦，所以我整理了一份Docker 常用命令大全分享给大家，这样在实用的过程中，直接在文档搜索就可以了，更加的便利，需要的小伙伴可以自行下载。
Docker命令详解
一、帮助启动命令
启动docker: systemctl start docker 
停止docker: systemctl stop docker 
重启docker: systemctl restart docker 
查看docker状态: systemctl status docker 
开机启动: systemctl enable docker 
查看docker概要信息: docker info 
查看docker总体帮助文档: docker --help  ...
继续访问
...操作命令(启动类,镜像命令,容器命令)_docker启动镜像
 systemctl status docker 
 
docker开机启动
 
 systemctlenabledocker 
 
查看docker概要信息
 
 docker info 
 
查看docker总体帮助文档
 
 docker --help 
 
查看docker命令帮助文档
 
 docker 命令 --help 
二、镜像命令
1、列出本地主机上的镜像
 
列出本地主机上的镜像 ...
 
继续访问
最新发布 docker命令大全.zip
Docker命令大全涵盖了Docker操作的各个方面，从启动Docker服务、管理镜像、操作容器到配置网络和数据卷等。下面将详细解析Docker命令大全，帮助用户更好地理解和使用Docker。
Docker命令大全
docker常用命令，很适合初学者，希望能帮到大家 容器操作 ps inspect top attach events logs wait export port
Docker 常用命令(三)_systemctl start docker
一、帮助命令
 
显示 Docker 版本信息。
 
 docker version 
 
显示 Docker 系统信息,包括镜像和容器数。
 
 docker info 
 
帮助
 
 docker --help 
 
启动 docker 后台服务
 
 sudo systemctl start docker 
 
关闭 docker 后台服务
 
如果提示“Warning: Stopping docker.service, but it can still be activated by: docker...
 
继续访问
docker命令大全集合
docker命令集合，集中的列出了所有的docker操作的命令，以及相关的命令说明; 包含： 服务器启动， docker系统信息， docker镜像的各种操作， 日常运行的各种命令以及组合命令 阅读建议： 保存一份到项目的readme中，...
精华！Docker 常用命令大全
点击上方“小白学视觉”，选择加"星标"或“置顶”重磅干货，第一时间送达本文包括容器生命周期管理命令、容器操作命令、容器rootfs命令、镜像仓库命令、本地镜像管理命令和基础版本信息命令。容器生命周期管理命令run创建一个新的容器。#使用docker镜像nginx:latest以后台模式启动一个容器, #并将容器命名为mynginx。 dockerrun--namemynginx-d...
继续访问
 
Docker常用命令（三）
官方帮助文档： https://docs.docker.com/reference/
帮助命令
