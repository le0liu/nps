# nps
![](https://img.shields.io/github/stars/cnlh/nps.svg)   ![](https://img.shields.io/github/forks/cnlh/nps.svg) ![](https://img.shields.io/github/license/cnlh/nps.svg)

nps是一款轻量级、高性能、功能强大的**内网穿透**代理服务器。目前支持**tcp、udp流量转发**，可支持任何tcp、udp上层协议（访问内网网站、本地支付接口调试、ssh访问、远程桌面，内网dns解析等等……），此外还**支持内网http代理、内网socks5代理**，可实现在非内网环境下如同使用vpn一样访问内网资源和设备的效果。

目前市面上提供类似服务的有花生壳、TeamView、GoToMyCloud等等，但要使用第三方的公网服务器就必须为第三方付费，并且这些服务都有各种各样的限制，此外，由于数据包会流经第三方，因此对数据安全也是一大隐患。


go语言编写，无第三方依赖，各个平台都已经编译在release中。

## 背景
![image](https://github.com/cnlh/nps/blob/master/image/web.png?raw=true)

1. 做微信公众号开发、小程序开发等----> 域名代理模式


2. 想在外网通过ssh连接内网的机器，做云服务器到内网服务器端口的映射，----> tcp代理模式

3. 在非内网环境下使用内网dns，或者需要通过udp访问内网机器等----> udp代理模式

4. 在外网使用HTTP代理访问内网站点----> http代理模式

5. 搭建一个内网穿透ss，在外网如同使用内网vpn一样访问内网资源或者设备----> socks5代理模式


## 目录

* [安装](#安装)
    * [编译安装](#源码安装)
    * [release安装](#release安装)
* [服务端](#web管理模式)
    * [服务端启动](#服务端启动)
       * [服务端测试](#服务端测试)
       * [服务端启动](#服务端启动)
       * [web管理](#web管理)
       * [服务端停止或重启](#服务端停止或重启)
    * [配置文件说明](#服务端配置文件)
    * [详细使用说明](#详细说明)
       * [http|https域名解析](#域名解析)
       * [tcp隧道](#tcp隧道)
       * [udp隧道](#udp隧道)
       * [socks5代理](#socks5代理)
       * [http正向代理](#http正向代理)
    * [使用https](#使用https)
    * [与nginx配合](#与nginx配合)
    * [关闭http|https代理](#关闭代理)
    * [将nps安装到系统](#将nps安装到系统)
* [客户端](#客户端)
    * [客户端启动](#客户端启动)
        * [无配置文件模式](#无配置文件模式)
        * [配置文件模式](#配置文件模式)
    * [配置文件说明](#配置文件说明)
        * [全局配置](#全局配置)
        * [域名代理](#域名代理)
        * [tcp隧道模式](#tcp隧道模式)
        * [udp隧道模式](#udp隧道模式)
        * [http代理模式](#http代理模式)
        * [socks5模式](#socks5模式)
    * [断线重连](#断线重连)
    * [状态检查](#状态检查)
    * [重载配置文件](#重载配置文件)
    * [通过代理连接nps](#通过代理连接nps)

* [相关功能](#相关功能)
   * [数据压缩支持](#数据压缩支持)
   * [站点密码保护](#站点保护)
   * [加密传输](#加密传输)
   * [host修改](#host修改)
   * [自定义header](#自定义header)
   * [自定义404页面](#404页面配置)
   * [流量限制](#流量限制)
   * [带宽限制](#带宽限制)
   * [负载均衡](#负载均衡)
   * [端口白名单](#端口白名单)
   * [端口范围映射](#端口范围映射)
   * [守护进程](#守护进程)
   * [KCP协议支持](#KCP协议支持)
   * [域名泛解析](#域名泛解析)
   * [URL路由](#URL路由)
   * [限制ip访问](#限制ip访问)
* [相关说明](#相关说明)
   * [流量统计](#流量统计)
   * [热更新支持](#热更新支持)
   * [获取用户真实ip](#获取用户真实ip)
   * [客户端地址显示](#客户端地址显示)
* [简单的性能测试](#简单的性能测试)
   * [qps](#qps)
   * [速度测试](#速度测试)
   * [内存和cpu](#内存和cpu)
   * [额外消耗连接数](#额外消耗连接数)
* [webAPI](#webAPI)
* [贡献](#贡献)
* [交流群](#交流群)



## 安装

### release安装
> https://github.com/cnlh/nps/releases

下载对应的系统版本即可，服务端和客户端是单独的，go语言开发，无需任何第三方依赖

### 源码安装
- 安装源码
> go get github.com/cnlh/nps
- 编译
> go build cmd/nps/nps.go

> go build cmd/npc/npc.go

## web管理模式

![image](https://github.com/cnlh/nps/blob/master/image/web2.png?raw=true)
### 介绍

可在网页上配置和管理各个tcp、udp隧道、内网站点代理，http、https解析等，功能强大，操作方便。


**提示：使用web模式时，服务端执行文件必须在项目根目录，否则无法正确加载配置文件**

### 启动


#### 服务端测试
```
 ./nps test
```
如有错误请及时修改配置文件，无错误可继续进行下去
#### 服务端启动
```
 ./nps start
```
如果无需daemon运行，去掉start即可

#### web管理

进入web界面，公网ip:web界面端口（默认8080），密码默认为123

进入web管理界面，有详细的说明

#### 服务端停止或重启
如果是daemon启动
```
 ./nps stop|restart
```

### 服务端配置文件
- /conf/app.conf

名称 | 含义
---|---
httpport | web管理端口
password | web界面管理密码
bridePort  | 服务端客户端通信端口
pemPath | ssl certFile绝对路径
keyPath | ssl keyFile绝对路径
httpsProxyPort | 域名代理https代理监听端口
httpProxyPort | 域名代理http代理监听端口
authip|web api免验证IP地址
bridgeType|客户端与服务端连接方式kcp或tcp
publicVkey|客户端以配置文件模式启动时的密钥，设置为空表示关闭客户端配置文件连接模式
ipLimit|是否限制ip访问，true或false或忽略

### 详细说明

#### 域名解析

**适用范围：** 小程序开发、微信公众号开发、产品演示

**假设场景：**
- 有一个域名proxy.com，有一台公网机器ip为1.1.1.1
- 两个内网开发站点127.0.0.1:81，127.0.0.1:82
- 想通过（http|https://）a.proxy.com访问127.0.0.1:81，通过（http|https://）b.proxy.com访问127.0.0.1:82
- 例如配置文件中tcpport为8284

**使用步骤**
- 将*.proxy.com解析到公网服务器1.1.1.1
- 在客户端管理中创建一个客户端，记录下验证密钥
- 点击该客户端的域名管理，添加两条规则规则：1、域名：a.proxy.com，内网目标：127.0.0.1:81，2、域名：b.proxy.com，内网目标：127.0.0.1:82
- 内网客户端运行

```
./npc -server=1.1.1.1:8284 -vkey=客户端的密钥
```
现在访问（http|https://）a.proxy.com，b.proxy.com即可成功

**https:** 如需使用https请在配置文件中将https端口设置为443，和将对应的证书文件路径添加到配置文件中，上面添加的这条记录将会把http、https都转发到内网目标

#### tcp隧道


**适用范围：**  ssh、远程桌面等tcp连接场景

**假设场景：**
 想通过访问公网服务器1.1.1.1的8001端口，连接内网机器10.1.50.101的22端口，实现ssh连接，例如配置文件中tcpport为8284

**使用步骤**
- 在客户端管理中创建一个客户端，记录下验证密钥
- -内网客户端运行
```
./npc -server=1.1.1.1:8284 -vkey=客户端的密钥
```
- 在该客户端隧道管理中添加一条tcp隧道，填写监听的端口（8001）、内网目标ip和目标端口（10.1.50.101:22），选择压缩方式，保存。
- 访问公网服务器ip（127.0.0.1）,填写的监听端口(8001)，相当于访问内网ip(10.1.50.101):目标端口(22)，例如：ssh -p 8001 root@127.0.0.1

#### udp隧道



**适用范围：**  内网dns解析等udp连接场景

**假设场景：**
内网有一台dns（10.1.50.102:53），在非内网环境下想使用该dns，公网服务器为1.1.1.1，例如配置文件中tcpport为8284

**使用步骤**
- 在客户端管理中创建一个客户端，记录下验证密钥
- -内网客户端运行
```
./npc -server=1.1.1.1:8284 -vkey=客户端的密钥
```
- 在该客户端的隧道管理中添加一条udp隧道，填写监听的端口（53）、内网目标ip和目标端口（10.1.50.102:53），选择压缩方式，保存。
- 修改本机dns为127.0.0.1，则相当于使用10.1.50.202作为dns服务器

#### socks5代理


**适用范围：**  在外网环境下如同使用vpn一样访问内网设备或者资源

**假设场景：**
想将公网服务器1.1.1.1的8003端口作为socks5代理，达到访问内网任意设备或者资源的效果，例如配置文件中tcpport为8284

**使用步骤**
- 在客户端管理中创建一个客户端，记录下验证密钥
- -内网客户端运行
```
./npc -server=1.1.1.1:8284 -vkey=客户端的密钥
```
- 在该客户端隧道管理中添加一条socks5代理，填写监听的端口（8003），验证用户名和密码自行选择（建议先不填，部分客户端不支持，proxifer支持），选择压缩方式，保存。
- 在外网环境的本机配置socks5代理，ip为公网服务器ip（127.0.0.1），端口为填写的监听端口(8003)，即可畅享内网了

#### http正向代理

**适用范围：**  在外网环境下使用http代理访问内网站点

**假设场景：**
想将公网服务器1.1.1.1的8004端口作为http代理，访问内网网站，例如配置文件中tcpport为8284

**使用步骤**
- 在客户端管理中创建一个客户端，记录下验证密钥
- -内网客户端运行
```
./npc -server=1.1.1.1:8284 -vkey=客户端的密钥
```
- 在该客户端隧道管理中添加一条http代理，填写监听的端口（8004），选择压缩方式，保存。
- 在外网环境的本机配置http代理，ip为公网服务器ip（127.0.0.1），端口为填写的监听端口(8004)，即可访问了


### 使用https

在配置文件中将httpsProxyPort设置为443或者其他你想配置的端口，和将对应的证书文件路径添加到配置文件中，然后就和http代理一样了，例如

- 需要访问https://a.proxy.com 对应内网127.0.0.1:80

- 在域名代理中添加a.proxy.com 内网目标127.0.0.1:80 即可将所有到达本代理的http(s)请求都转发到127.0.0.1:80

### 与nginx配合

有时候我们还需要在云服务器上运行nginx来保证静态文件缓存等，本代理可和nginx配合使用，在配置文件中将httpProxyPort设置为非80端口，并在nginx中配置代理，例如httpProxyPort为8024时
```
server {
    listen 80;
    server_name *.proxy.com;
    location / {
        proxy_set_header Host  $http_host;
        proxy_pass http://127.0.0.1:8024;
    }
}
```
如需使用https也可在nginx监听443端口并配置ssl，并将本代理的httpsProxyPort设置为空关闭https即可，例如httpProxyPort为8024时

```
server {
    listen 443;
    server_name *.proxy.com;
    ssl on;
    ssl_certificate  certificate.crt;
    ssl_certificate_key private.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        proxy_set_header Host  $http_host;
        proxy_pass http://127.0.0.1:8024;
    }
}
```
### 关闭代理

如需关闭http代理可在配置文件中将httpProxyPort设置为空，如需关闭https代理可在配置文件中将httpsProxyPort设置为空。

### 将nps安装到系统
如果需要长期并且方便的运行nps服务端，可将nps安装到操作系统中，可执行命令

```
(./nps|nps.exe) install
```
安装成功后，对于linux，darwin，将会把配置文件和静态文件放置于/etc/nps/，并将可执行文件nps复制到/usr/bin/nps或者/usr/local/bin/nps，安装成功后可在任何位置执行

```
nps test|start|stop|restart|status
```
对于windows系统，将会把配置文件和静态文件放置于C:\Program Files\nps，安装成功后可将可执行文件nps.exe复制到任何位置执行

```
nps.exe test|start|stop|restart|status
```

## 客户端

### 客户端启动
#### 无配置文件模式
此模式的各种配置在服务端web管理中完成
```
 ./npc -server=ip:port -vkey=web界面中显示的密钥
```
#### 配置文件模式
此模式使用nps的公钥验证，各种配置在客户端完成，同时服务端web也可以进行管理
```
 ./npc -config=npc配置文件路径
```
#### 配置文件说明
[示例配置文件](https://github.com/cnlh/nps/tree/master/conf/npc.conf)
##### 全局配置
```
[common]
server=127.0.0.1:8284
tp=tcp
vkey=123
username=111
password=222
compress=snappy
crypt=true
```
项 | 含义
---|---
server | 服务端ip:port
tp | 与服务端通信模式(tcp或kcp)
vkey|服务端配置文件中的密钥(非web)
username|socks5或http(s)密码保护用户名(可忽略)
username|socks5或http(s)密码保护密码(可忽略)
compress|是否压缩传输(snappy或空或忽略)
crypt|是否加密传输(true或false或忽略)
##### 域名代理

```
[web1]
host=a.o.com
target=127.0.0.1:8080,127.0.0.1:8082
host_change=www.proxy.com
header_set_proxy=nps
```
项 | 含义
---|---
web1 | 备注
host | 域名(http|https都可解析)
target|内网目标，负载均衡时多个目标，逗号隔开
host_change|请求host修改
header_xxx|请求header修改或添加，header_proxy表示添加header proxy:nps

##### tcp隧道模式

```
[tcp]
mode=tcpServer
target=127.0.0.1:8080
port=9001
```
项 | 含义
---|---
mode | tcpServer
port | 在服务端的代理端口
target|内网目标

##### udp隧道模式

```
[udp]
mode=udpServer
target=127.0.0.1:8080
port=9002
```
项 | 含义
---|---
mode | udpServer
port | 在服务端的代理端口
target|内网目标
##### http代理模式

```
[http]
mode=httpProxyServer
port=9003
```
项 | 含义
---|---
mode | httpProxyServer
port | 在服务端的代理端口
##### socks5代理模式

```
[socks5]
mode=socks5Server
port=9004
```
项 | 含义
---|---
mode | socks5Server
port | 在服务端的代理端口

#### 断线重连
```
[common]
auto_reconnection=true
```

#### 状态检查
```
 ./npc status -config=npc配置文件路径
```
#### 配置文件重载
```
 ./npc restart -config=npc配置文件路径
```

#### 通过代理连接nps
有时候运行npc的内网机器无法直接访问外网，此时可以可以通过socks5代理连接nps

对于配置文件方式启动,设置
```
[common]
proxy_socks5_url=socks5://111:222@127.0.0.1:8024
```
对于无配置文件模式,加上参数

```
-proxy=socks5://111:222@127.0.0.1:8024
```
即socks5://username:password@ip:port

## 相关功能

### 数据压缩支持

由于是内网穿透，内网客户端与服务端之间的隧道存在大量的数据交换，为节省流量，加快传输速度，由此本程序支持SNNAPY形式的压缩。


- 所有模式均支持数据压缩，可以与加密同时使用
- 开启此功能会增加cpu和内存消耗
- 在web管理或客户端配置文件中设置


### 加密传输

如果公司内网防火墙对外网访问进行了流量识别与屏蔽，例如禁止了ssh协议等，通过设置 配置文件，将服务端与客户端之间的通信内容加密传输，将会有效防止流量被拦截。

- 开启此功能会增加cpu和内存消耗
- 在server端加上参数
- 在web管理或客户端配置文件中设置



### 站点保护
域名代理模式所有客户端共用一个http服务端口，在知道域名后任何人都可访问，一些开发或者测试环境需要保密，所以可以设置用户名和密码，nps将通过 Http Basic Auth 来保护，访问时需要输入正确的用户名和密码。


- 在web管理或客户端配置文件中设置

### host修改

由于内网站点需要的host可能与公网域名不一致，域名代理支持host修改功能，即修改request的header中的host字段。

**使用方法：在web管理中设置**

### 自定义header

支持对header进行新增或者修改，以配合服务的需要

### 404页面配置
支持域名解析模式的自定义404页面，修改/web/static/page/error.html中内容即可，暂不支持静态文件等内容

### 流量限制

支持客户端级流量限制，当该客户端入口流量与出口流量达到设定的总量后会拒绝服务
，域名代理会返回404页面，其他代理会拒绝连接

### 带宽限制

支持客户端级带宽限制，带宽计算方式为入口和出口总和，权重均衡

### 负载均衡
本代理支持域名解析模式的负载均衡，在web域名添加或者编辑中内网目标分行填写多个目标即可实现轮训级别的负载均衡

### 端口白名单
为了防止服务端上的端口被滥用，可在app.conf中配置allowPorts限制可开启的端口，忽略或者不填表示端口不受限制，格式：

```
allowPorts=9001-9009,10001,11000-12000
```

### 端口范围映射
当客户端以配置文件的方式启动时，可以将本地的端口进行范围映射，仅支持tcp和udp模式，例如：

```
[tcp]
mode=tcpServer
port=9001-9009,10001,11000-12000
target=8001-8009,10002,13000-14000
```

逗号分隔，可单个或者范围，注意上下端口的对应关系，无法一一对应将不能成功
### 守护进程
本代理支持守护进程，使用示例如下，服务端客户端所有模式通用,支持linux，darwin，windows。
```
./(nps|npc) start|stop|restart|status 若有其他参数可加其他参数
```
```
(nps|npc).exe start|stop|restart|status 若有其他参数可加其他参数
```
### KCP协议支持

KCP 是一个快速可靠协议，能以比 TCP浪费10%-20%的带宽的代价，换取平均延迟降低 30%-40%，在弱网环境下对性能能有一定的提升。可在app.conf中修改bridgeType为kcp
，设置后本代理将开启udp端口（bridgePort）

注意：当服务端为kcp时，客户端连接时也需要使用相同配置，无配置文件模式加上参数type=kcp,配置文件模式在配置文件中设置tp=kcp

### 域名泛解析
支持域名泛解析，例如将host设置为*.proxy.com，a.proxy.com、b.proxy.com等都将解析到同一目标，在web管理中或客户端配置文件中将host设置为此格式即可。

### URL路由
本代理支持根据URL将同一域名转发到不同的内网服务器，可在web中或客户端配置文件中设置，此参数也可忽略，例如在客户端配置文件中

```
[web1]
host=a.proxy.com
target=127.0.0.1:7001
location=/test
[web2]
host=a.proxy.com
target=127.0.0.1:7002
location=/static
```
对于a.proxy.com/test将转发到web1，对于a.proxy.com/static将转发到web2

### 限制ip访问
如果将一些危险性高的端口例如ssh端口暴露在公网上，可能会带来一些风险，本代理支持限制ip访问。

**使用方法:** 在配置文件app.conf中设置ipLimit=true，设置后仅通过注册的ip方可访问。

**ip注册**： 在需要访问的机器上，运行客户端

```
./npc register -server=ip:port -vkey=公钥或客户端密钥 time=2
```

time为有效小时数，例如time=2，在当前时间后的两小时内，本机公网ip都可以访问nps代理.

**注意：** 本机公网ip并不是一成不变的，请自行注意有效期的设置，同时同一网络下，多人也可能是在公用同一个公网ip。

## 相关说明

### 获取用户真实ip

在域名代理模式中，可以通过request请求 header 中的 X-Forwarded-For 和 X-Real-IP 来获取用户真实 IP。

**本代理前会在每一个请求中添加了这两个 header。**

### 热更新支持
在web管理中的修改将实时使用，无需重启客户端或者服务端

### 客户端地址显示
在web管理中将显示客户端的连接地址

### 流量统计
可统计显示每个代理使用的流量，由于压缩和加密等原因，会和实际环境中的略有差异

## 简单性能测试

### qps
![image](https://github.com/cnlh/nps/blob/master/image/qps.png?raw=true)
### 速度测试
**测试环境：** 1M带宽云服务器，理论125kb/s，带宽与代理无关，与服务器带宽和内网客户端外网带宽关系较大。
![image](https://github.com/cnlh/nps/blob/master/image/speed.png?raw=true)


### 内存和cpu

**1000次性能测试后**
![image](https://github.com/cnlh/nps/blob/master/image/cpu1.png?raw=true)

**启动时**
![image](https://github.com/cnlh/nps/blob/master/image/cpu2.png?raw=true)

### 额外消耗连接数
为了最大化的提升效率和并发，客户端与服务端之间仅两条tcp连接，减少建立连接的时间消耗和多余tcp连接对机器性能的影响。

## webAPI

为方便第三方扩展，在web模式下可利用webAPI进行相关操作，详情见
[webAPI文档](https://github.com/cnlh/nps/wiki/webAPI%E6%96%87%E6%A1%A3)

## 贡献
- 如果遇到bug可以直接提交至dev分支
- 使用遇到问题可以通过issues反馈
- 项目处于开发阶段，还有很多待完善的地方，如果可以贡献代码，请提交 PR 至 dev 分支
- 如果有新的功能特性反馈，可以通过issues或者qq群反馈



## 交流群

![二维码.jpeg](https://i.loli.net/2019/02/15/5c66c32a42074.jpeg)
