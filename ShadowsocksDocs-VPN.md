# SelfBuiltVPN-_Shadowsocks

->`环境：aws ec2、ubuntu os （ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-20230517）、Shadowsocks-Python server`

REF：https://teddysun.com/486.html

## 一.购买海外服务器
注册azure、aws ...账号，首次可申领免费12个月试用项目

## 二.shadowsocks服务端安装
### 2.1 Shadowsocks 一键安装脚本（四合一）
本脚本适用环境
系统支持：CentOS 6+，Debian 7+，Ubuntu 12+  
内存要求：≥128M

关于本脚本  
1、一键安装 Shadowsocks-Python， ShadowsocksR， Shadowsocks-Go， Shadowsocks-libev 版（四选一）服务端；  
2、各版本的启动脚本及配置文件名不再重合；  
3、每次运行可安装一种版本；  
4、支持以多次运行来安装多个版本，且各个版本可以共存（注意端口号需设成不同）；  
5、若已安装多个版本，则卸载时也需多次运行（每次卸载一种）；  

友情提示：如果你有问题，请先阅读这篇《Shadowsocks Troubleshooting》之后再询问。  

### 2.2 默认配置
服务器端口：自己设定（如不设定，默认从 9000-19999 之间随机生成）  
密码：自己设定（如不设定，默认为 teddysun.com）  
加密方式：自己设定（如不设定，Python 和 libev 版默认为 aes-256-gcm，R 和 Go 版默认为 aes-256-cfb）  
协议（protocol）：自己设定（如不设定，默认为 origin）（仅限 ShadowsocksR 版）  
混淆（obfs）：自己设定（如不设定，默认为 plain）（仅限 ShadowsocksR 版）  
备注：脚本默认创建单用户配置文件，如需配置多用户，请手动修改相应的配置文件后重启即可。  

### 2.3 安装方法  
使用root用户登录，运行以下命令：
```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log  
或 
后台运行：nohup ./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log &
```

安装完成后，脚本提示如下
```
Congratulations, your_shadowsocks_version install completed!
Your Server IP        :your_server_ip
Your Server Port      :your_server_port
Your Password         :your_password
Your Encryption Method:your_encryption_method

Your QR Code: (For Shadowsocks Windows, OSX, Android and iOS clients)
 ss://your_encryption_method:your_password@your_server_ip:your_server_port
Your QR Code has been saved as a PNG file path:
 your_path.png

Welcome to visit:https://teddysun.com/486.html
Enjoy it!
```

### 2.4 卸载方法
若已安装多个版本，则卸载时也需多次运行（每次卸载一种）  
使用root用户登录，运行以下命令：  
```
./shadowsocks-all.sh uninstall
```

### 2.5 启动脚本
```
启动脚本后面的参数含义，从左至右依次为：启动，停止，重启，查看状态。

Shadowsocks-Python 版：
/etc/init.d/shadowsocks-python start | stop | restart | status

ShadowsocksR 版：
/etc/init.d/shadowsocks-r start | stop | restart | status

Shadowsocks-Go 版：
/etc/init.d/shadowsocks-go start | stop | restart | status

Shadowsocks-libev 版：
/etc/init.d/shadowsocks-libev start | stop | restart | status
```

### 2.6 各版本默认配置文件
```
Shadowsocks-Python 版：
/etc/shadowsocks-python/config.json

ShadowsocksR 版：
/etc/shadowsocks-r/config.json

Shadowsocks-Go 版：
/etc/shadowsocks-go/config.json

Shadowsocks-libev 版：
/etc/shadowsocks-libev/config.json
```


## 三.shadowsocks客户端下载地址
常规版 Windows 客户端  
https://github.com/shadowsocks/shadowsocks-windows/releases  
  
ShadowsocksR 版 Windows 客户端  
https://github.com/shadowsocksrr/shadowsocksr-csharp/releases


## 四.问题处理  

### 4.1 安装服务端时显示 [Error] Failed to install python 
问题：shadowsocks-all.sh 脚本没更新，不适用新系统；  
解决：vim shadowsocks-all.sh，把shad***socks.all.sh里面图中标黄的python改成 python3，图中是改过后的，没改之前是python，因为centos8已经不识别 python了；  
![img_1.png](img_1.png)

### 4.2 安装过程中出现 unzip: command not found
解决：yum install -y unzip zip  

### 4.3 编译安装Libsodium  
```
sudo wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
sudo tar -zxvf LATEST.tar.gz
cd libsodium-stable
sudo ./configure
sudo make && sudo make check   (两个sudo)
sudo make install
```

### 4.4 configure: error: no acceptable C compiler found in $PATH 问题解决
![img_2.png](img_2.png)


### 4.5 AWS的EC2实例突然无法连接(ping 不通)
大概率是ip被封了，可按以下方式进行验证：  
```
用站长工具检查，http://ping.chinaz.com/ ，并与下面的结果分析对照：
（1）如果国内的正常，国外的也正常，说明你的云服务器正常，当然这是费话，不需要讨论。
（2）如果国内的超时，国外不超时，说明该实例当前公网ip确实被墙了。
    -> 重启实例，会分配一个新的公网ip，使用新ip进行连接
（3）如果国内的超时，国外的也超时，说明你的安全组设置得有问题，或者你的云服务器有问题。
    -> 检查安全组设置，可参考 https://m.guigege.cn/cloud/43958.html
```

针对封锁机制-配置策略
![img_3.png](img_3.png)

### 4.* 其它
![img.png](img.png)