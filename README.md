# zju_server_vpn

# 日常使用
```bash
# 1.翻到校外
zjunet vpn -c
# 2.翻到国外，需要开启服务+配置代理，与配置文件有关，clash和V2ray不一样
#用clash
cd clash
./clash-linux-amd64-v1.10.0 -f glados.yaml -d .
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
#或者用V2ray
cd v2ray-linux-64
./v2ray
export http_proxy="http://127.0.0.1:10809"
export https_proxy="http://127.0.0.1:10809"
# 配置完成，开始科学上网

```
端口修改
- clash: glados.yaml
- V2ray: config.json

# 准备

校内网-校外网 vpn软件：zjunet

国内网-国外网 vpn软件：Clash / V2ray

科学上网服务器节点：比如[Glados](https://glados.rocks)，zju邮箱免费使用一年教育套餐

# vpn安装与使用

## [zjunet安装与使用](https://github.com/QSCTech/zjunet/blob/master/README.zh.md)

### zjunet安装

直接在能连上校园网的服务器上输入以下命令就可以安装
```bash
curl https://dl.zjuqsc.com/linux/qsc.public.key | sudo apt-key add -
curl https://dl.zjuqsc.com/linux/debian/qsc.list | sudo tee /etc/apt/sources.list.d/qsc.list
sudo apt-get update
sudo apt-get install zjunet
```
 ### zjunet使用

```bash
zjunet help #查看使用手册
zjunet user add [学号]@[abc] #添加用户，如果套餐是10元，就是a。实测只输入学号没用
zjunet vpn -c #连接vpn
```
## [Clash安装与使用](https://glados.rocks/console/terminal)
### Clash安装
```bash
curl https://glados.rocks/tools/clash-linux.zip -o clash.zip #下载Clash

unzip clash.zip

cd clash

curl https://update.glados-config.com/clash/xxxxxx/xxxxxxx/xxxxxx/glados-terminal.yaml > glados.yaml #下载您的终端配置文件

chmod +x ./clash-linux-amd64-v1.10.0
```
### Clash使用
启动clash
```bash
./clash-linux-amd64-v1.10.0 -f glados.yaml -d .
```
clash 打印如下日志说明clash启动成功。

```bash
INFO[0000] HTTP proxy listening  at: [::]:7890
INFO[0000] SOCKS proxy listening at: [::]:7891
INFO[0000] RESTful API listening at: 127.0.0.1:9090
```
注意这里的HTTP proxy 和 SOCKS proxy 的端口，待会配置代理要用到

![在这里插入图片描述](https://img-blog.csdnimg.cn/a00b221455a140f985f38e942dab321c.png)

当前terminal不要关闭，不要ctl+c。新开terminal进行其他活动

## [V2ray安装与使用](https://github.com/v2ray/v2ray-core)
### 下载 v2ray-linux-64.zip
[v2ray的Github地址](https://github.com/v2ray/v2ray-core/releases/)

在页面中找到 v2ray-linux-64.zip 文件下载（我的是64位的Ubuntu系统）。

下载后用ftp/sftp工具上传到linux的服务器上。再用unzip命令解压。

### 设置文件可执行权限

进入 v2ray-linux-64 目录，可以用 `ll`查看目录下的文件。
目录中的几个文件需要修改下权限，需要添加下可执行的权限。

```bash
cd v2ray-linux-64
ll
chmod 755 v2ray
chmod 755 v2ctl
chmod 755 systemd/system/v2ray.service
chmod 755 systemd/system/v2ray@.service
```
### config.json配置文件
1.先在windows下的v2rayN的客户端配置好，使得windows能科学上网
2.然后在客户端的服务器列表中中右键->【导出所选服务器为客户端配置】，保存成config.json文件。
注意这里的HTTP proxy 和 SOCKS proxy 的端口，待会配置代理要用到。

然后把这个config.json文件也上传到 v2ray-linux-64 目录中。

### 启动v2ray
启动V2ray

```bash
cd v2ray-linux-64
./v2ray run
```
关闭Vray：`Ctl+C`

# 网络代理配置
## 打开代理
### 配置代理方式1：基于网络协议
1. 走HTTP

```bash
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
```
两个都是http://127.0.0.1:7890，不用怀疑，就是这样。
clash 打印如下日志说明clash完成了工作
```bash
INFO[0016] [TCP] 127.0.0.1:35950 --> github.com:443 match DomainKeyword(github) using Proxy[GLaDOS-S1-05]
```

2. 走SOCKS
```bash
export http_proxy="socks5://127.0.0.1:7891"
export https_proxy="socks5://127.0.0.1:7891"
```
clash 打印如下日志说明clash完成了工作

```bash
INFO[2063] [TCP] 127.0.0.1:34428 --> 157.159.160.111:80 match Match() using Proxy[GLaDOS-JP-01]
```

### 配置代理方式2：基于需要翻墙的下载工具

1. Git

命令末尾临时生效
```bash
user@localhost:~$ git clone https://github.com/twbs/bootstrap.git --config "https.proxy=127.0.0.1:7890"  
```
或者

```bash
git config --global http.proxy "http://127.0.0.1:7890" 
git config --global https.proxy "https://127.0.0.1:7890"
# 或者
git config --global http.proxy "socks5://127.0.0.1:7891" 
git config --global https.proxy "socks5://127.0.0.1:7891"
```
2. apt
   
改apt的配置文件

```bash
sudo vim /etc/apt/apt.conf
```

在文件末尾加入下面这行

```bash
Acquire::http::Proxy "http://127.0.0.1:7890"
Acquire::https::Proxy "https://127.0.0.1:7890"
```

### 配置代理方式3：写入~/.bashrc永久生效

要想永久生效，就将以上语句任选其一写入~/.bashrc
比如：

```bash
vim ~/.bashrc
# 在文档末尾加上
git config --global http.proxy "http://127.0.0.1:7890" 
git config --global https.proxy "https://127.0.0.1:7890"
或者
export http_proxy="127.0.0.1:7890"
export https_proxy="127.0.0.1:7890"
# 写入后，别忘了
source ~/.bashrc
```

## 关闭代理

```bash
export http_proxy=""
export https_proxy=""
git config --global --unset http.proxy
```

# 参考资料

[Linux使用v2ray](https://www.hduzn.cn/2022/06/14/Linux%E4%BD%BF%E7%94%A8v2ray/)
