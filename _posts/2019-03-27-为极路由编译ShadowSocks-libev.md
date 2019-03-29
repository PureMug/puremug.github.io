---
layout: default
title: 为极路由编译ShadowSocks-libev
---

# {{ page.title }}

买的极路由3pro已经过了保修期，因此考虑申请root后安装ss客户端更方便。  
## 1. 路由器开通ROOT
到[官网](http://doc.hiwifi.com/)按最下方的说明操作  
成功后登陆试试：  
`ssh root@192.168.199.1 -p 1022`  
至于申请开发者，没有考虑也就没有尝试。  

## 2. 下载SDK和准备编译环境
到[网站](http://doc.hiwifi.com/docs/sdk_usage)上根据说明找适合自己的SDK并下载。  
我用的是mtmips。  
下载后按文档说明解压和安装依赖库。  
我用的是Debian 9，文档中的openjdk-6现在已经到8了，自己改改就好了。  
`tar xfj mtmips-sdk.tar.bz2`  
`apt install subversion git build-essential libncurses5-dev zlib1g-dev gawk unzip gettext libssl-dev intltool openjdk-8-jre-headless optipng`  
`ln -sf bash /bin/sh`  

## 3. 获取github上的源码Makefile
因为极路由就是OpenWRT的一个变种，所以使用OpenWRT版本的shadowsocks即可。  
到[github](https://github.com/shadowsocks/openwrt-shadowsocks)上按照文档说明中的“编译”部分操作。  
上面原文复制如下：  
```bash  
# 以 ar71xx 平台为例
tar xjf OpenWrt-SDK-ar71xx-for-linux-x86_64-gcc-4.8-linaro_uClibc-0.9.33.2.tar.bz2
cd OpenWrt-SDK-ar71xx-*
# 添加 feeds
git clone https://github.com/shadowsocks/openwrt-feeds.git package/feeds
# 获取 shadowsocks-libev Makefile
git clone https://github.com/shadowsocks/openwrt-shadowsocks.git package/shadowsocks-libev
# 选择要编译的包 Network -> shadowsocks-libev
make menuconfig
# 开始编译
make package/shadowsocks-libev/compile V=99
```  
这里有几个坑：
1. make menuconfig时报错，解决办法是进入`scripts/config`目录，执行
`make clean`  
`make`  
再回到主目录运行`make menuconfig`就好了  

2. 执行最后的编译时，报错：  
> Package libpcre is missing dependencies for the following libraries   
> libpcre.so.0  

中间各种google/百度/必应，找到的方法都是修改Makefile的`define Packages/libpcre`部分或者最后一行的`eval`部分，试过了均无效。  
后来仔细想想，这个报错和搜到的那些文章最大的不同在于，这个报错是特么自己说缺少自己应该编译出来的文件，有矛盾，所以尝试修改了`define Packages/libpcre/install`，照猫画虎增加一行：   
`$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpcre.so $(1)/usr/lib/libpcre.so.0`  
再次make，居然成功了。  
猜测原因是这个包编译好本来应该生成.so.0的文件，后面的步骤需要这个文件，但是这个Makefile没有生成这个文件。  

## 4. 上传安装
生成的打包文件是ipk后缀，放在bin目录下：  
`ll bin/mtmips/packages/base/`  
> libmbedtls_2.16.0-2_mtmips_1004kc.ipk  
> shadowsocks-libev_3.2.5-1_mtmips_1004kc.ipk  
> shadowsocks-libev-server_3.2.5-1_mtmips_1004kc.ipk  

`ll bin/mtmips/packages/packages/`  
> libcares_1.14.0-1_mtmips_1004kc.ipk  
> libev_4.25-1_mtmips_1004kc.ipk  
> libpcre_8.42-1_mtmips_1004kc.ipk  
> libsodium_1.0.17-1_mtmips_1004kc.ipk  

我需要的是shadowsocks-libev_3.2.5-1_mtmips_1004kc.ipk这个文件，scp到极路由/tmp下安装：  
`opkg install shadowsocks-libev_3.2.5-1_mtmips_1004kc.ipk`  
报缺少包，按提示复制这里的其他包并安装即可。  
安装后运行时可能还会报缺少pcre包，也一样传上去安装。  

## 5. 配置运行
新建一个配置文件/etc/ss-local.conf，内容为一段json：  
```json
{
	"server": "myserver",
	"server_port": [myserverport],
	"local_address": "192.168.199.1",
	"local_port": 1080,
	"password": "secret key",
	"method": "aes-256-cfb",
	"mode": "tcp_and_udp"
}
```  
极路由shell下运行：
```bash
ss-local -c /etc/ss-local.conf -a nobody
```  
连上路由器的电脑上配置代理为SOCKS5 192.168.199.1:1080，访问google成功。  
手机WiFi高级设置以及电脑浏览器中可均配置pac文件动态代理，缺点是只能支持浏览器。  
pac文件可以用genpac脚本生成，此处不详述，生成的pac文件可以上传到路由器/www下，通过http://192.168.199.1/ss.pac (假设文件名为ss.pac)访问。

# 6. 自动启动
新建脚本 /etc/init.d/sslocal  
```bash	
#!/bin/sh /etc/rc.common
 
START=99
 
SERVICE_USE_PID=1
SERVICE_WRITE_PID=1
SERVICE_DAEMONIZE=1
SERVICE_PID_FILE=/var/run/ss-local.pid
CONFIG=/etc/ss-local.json
RUNAS=nobody
 
start() {
	service_start /usr/bin/ss-local -c $CONFIG -a $RUNAS -f $SERVICE_PID_FILE
}

stop() {
	service_stop /usr/bin/ss-local
}
```  
增加执行权限： `chmod +x /etc/init.d/sslocal`  
启用自动启动： `/etc/init.d/sslocal enable`  
启动： `/etc/init.d/sslocal start`  
停止： `/etc/init.d/sslocal stop`  

# 7. 透明代理
socks代理使用还是不太方便，考虑使用透明代理（ss-redir），待研究。
