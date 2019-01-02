---
layout: default
title: 在CentOS 7上安装docker
---

# {{ page.title }}

## 1. 基本安装
*以下操作根据官方文档整理：*  
1)  安装依赖包  
`sudo yum install yum-utils device-mapper-persistent-data lvm2`
2) 添加docker仓库  
`sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
3) 安装社区版  
`sudo yum install docker-ce`
4) 启动docker  
`sudo systemctl start docker`
5) 运行hello world测试  
`sudo docker run hello-world`
6) 以普通用户身份运行docker hello world  
将当前用户加入docker组即可，如果运行时出错，检查是否之前生成了.docker目录（root权限）  
`sudo usermod -aG docker $USER`  
`docker run hello-world`
7) 其他命令  
- 启用自动启动：  
`sudo systemctl enable docker`  
- 查看当前已下载镜像  
`docker image ls`
