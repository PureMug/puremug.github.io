---
layout: default
title: Windows下MySQL多实例配置
---

# {{ page.title }}
1. 首先准备好一个已安装的MySQL环境，需要用到其中的程序部分。假设路径：D:\techapps\mysql-5.7.16-winx64
2. 准备一个空文件夹，例如：D:\temp\mysql_instance。
3. 在上面提到的mysql_instance文件夹建两个空目录，分别叫data和tmp。
4. 在mysql_instance文件夹下新建一个文本文件，命名为my.ini，文件内容如下：
```
[mysqld]
port			= 3307
socket		= D:/temp/mysql_instance/mysql.sock
basedir		= D:/techapps/mysql-5.7.16-winx64
tmpdir		= D:/temp/mysql_instance/tmp
datadir		= D:/temp/mysql_instance/data
character-set-server = utf8
```
上面配的目录跟第三步建的文件夹对应就可以了。

5. 以管理员身份打开cmd，运行下面的命令：
```
D:\techapps\mysql-5.7.16-winx64\bin\mysqld.exe --defaults-file=D:\temp\mysql_instance\my.ini --initialize-insecure --console
```
执行之后可以看到data下生成了一批文件和目录，就是mysql的系统数据文件。

6. 在上面的cmd窗口输入以下命令，创建服务：
```
D:\techapps\mysql-5.7.16-winx64\bin\mysqld.exe --install MySQL3307 --defaults-file=D:\temp\mysql_instance\my.ini
```
7. 在计算机管理->服务下可以找到MySQL3307服务，并尝试启动它。
8. 完成。

---
如要删除服务，先停止服务后执行：
```
D:\techapps\mysql-5.7.16-winx64\bin\mysqld.exe --remove MySQL3307
```
