---
layout: default
title: 使用git命令行访问github
---

# {{ page.title }}
新注册个github账号，打算记录点东西，发现首先要解决的是git方式的checkout和checkin，为了练练git命令和Markdown语法，不打算用官方客户端。
这不是完整的git命令说明，只是为了实现自己的需求。
我的环境是Windows 10， Git Portable。
1. 建个文件夹用来放github库里的文件。
2. 进入这个文件夹，执行`git clone https://github.com/PureMug/Blog.git`，会有个Blog文件夹出现。
3. 进入Blog文件夹，随便修改点什么，执行 `git add 2017\\xxx.md`，会提示定义用户名和邮箱：
> *** Please tell me who you are.
> Run
> git config --global user.email "you@example.com"
> git config --global user.name "Your Name"
> ......
4. 按提示设置自己的用户名和邮箱，我打算设置为本地仓库级别的，所以不用global参数：
```
git config --local user.name PureMug
git config --local user.email "itisme@gmail.com"
```
5. 提交修改 `git commit`，写注释后提交到本地仓库，这地方居然做了个windows版的小vi，令人惊叹。(貌似是MinGW实现的)
6. 上传到远端 `git push`，会提示输入用户名密码，输入正确即可。

上服务器看效果，打完收工。

另外记几个命令，强制用服务器端文件覆盖本地：
```
git fetch --all
git reset --hard origin/master
```

