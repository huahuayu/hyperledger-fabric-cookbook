# hyperledger fabric cookbook(working in progress)
本教程使用的版本为[hyperledger fabric 1.4](https://hyperledger-fabric.readthedocs.io/en/release-1.4)，这是目前最新的版本，也是第一个长期支持版本，官方保证后续能平滑升级。详情可以参考[what's new in hyperledger fabric 1.4](https://hyperledger-fabric.readthedocs.io/en/release-1.4/whatsnew.html)  

开发平台为Mac OS

## 概念介绍
概念介绍[官方文档](https://hyperledger-fabric.readthedocs.io/en/latest/key_concepts.html)包含以下方面：  
* Introduction
* Hyperledger Fabric Functionalities
* Hyperledger Fabric Model
* Blockchain network
* Identity
* Membership
* Peers
* Private data
* Ledger
* Smart Contracts and Chaincode
* Use Cases

特别是要理解里面讲到的[sample network](https://hyperledger-fabric.readthedocs.io/en/latest/network/network.html#the-sample-network):
![](https://hyperledger-fabric.readthedocs.io/en/latest/_images/network.diagram.1.png)

## 开发环境准备
**1. 安装最新curl**  
先用`curl --version`检查系统中是否已经安装了curl
``` zsh
shiming@pro ➜  ~ curl --version
curl 7.54.0 (x86_64-apple-darwin18.0) libcurl/7.54.0 LibreSSL/2.6.4 zlib/1.2.11 nghttp2/1.24.1
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp smb smbs smtp smtps telnet tftp
Features: AsynchDNS IPv6 Largefile GSS-API Kerberos SPNEGO NTLM NTLM_WB SSL libz HTTP2 UnixSockets HTTPS-proxy
```

如果没有安装，mac可以使用`brew`安装  
``` zsh
shiming@pro ➜  ~ brew install curl
```

**2. 安装Docker 和 Docker Compose**  
如果已安装，检查版本，版本需大于17.06.2，建议安装最新版：    
``` zsh
shiming@pro ➜  ~ docker --version
Docker version 18.09.2, build 6247962
shiming@pro ➜  ~ docker-compose --version
docker-compose version 1.23.2, build 1110ad01
```

如果未安装，请参考[docker官方安装指引(Mac)](https://docs.docker.com/docker-for-mac/install/)

**3. 安装golang**  
参考golang官方指引 https://golang.org/dl/

安装好golang之后注意要把GOPATH和$GOPATH/bin加到PATH环境变量中

官方推荐golang版本为1.11.x，但是目前golang最新版本为1.12.x，应该也是兼容的。可以用以下命令检查golang版本：
``` zsh
shiming@pro ➜  ~ go version
go version go1.11.5 darwin/amd64
```

**4. 安装nodejs和npm**  
如果使用nodejs开发hyperledger fabric应用则需安装nodejs和npm
**官方只支持8.x的nodejs版本**，另外官方推荐npm版本是5.6.0

查看nodejs和npm版本
``` zsh
shiming@pro ➜  ~ npm -v
6.9.0
shiming@pro ➜  ~ node -v
v11.9.0
```
我的npm版本是6.9.0，nodejs版本是11.9.0，跟官方要求的不太一致

可以使用以下命令安装指定版本npm
``` zsh
npm install npm@5.6.0 -g
```

nodejs多版本管理可以使用nvm，如果没有nvm可以用`brew install nvm`安装（或参考[官方安装指引](https://github.com/creationix/nvm#install--update-script)），nvm的使用可以参考[这篇文章](https://zhuanlan.zhihu.com/p/24698499)

nodejs的版本列表可以在nodejs的[release页面](https://nodejs.org/en/download/releases/)查看

使用`nvm install` + `nvm use`就可以安装并指定nodejs版本了
``` zsh
shiming@pro ➜  ~ nvm install v8.15.1
Downloading and installing node v8.15.1...
Downloading https://nodejs.org/dist/v8.15.1/node-v8.15.1-darwin-x64.tar.xz...
######################################################################## 100.0%
Computing checksum with shasum -a 256
Checksums matched!
Now using node v8.15.1 (npm v6.4.1)
Creating default alias: default -> v8.15.1

shiming@pro ➜  ~ nvm use v8.15.1
Now using node v8.15.1 (npm v6.4.1)
```

## 安装示例、二进制文件和docker镜像
官方整理好了脚本去安装示例项目，二进制文件和docker镜像
``` zsh
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0
```
但是bit.ly短网址在国内是被墙的，如果terminal没走代理是无法读到这个短网址的信息的，会出现报错curl: (56) Recv failure: Connection reset by peer。

解决方案：1.给terminal挂代理 2.可以用这个命令代替短网址，这个长网址就是短网址解析出来的`curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 1.4.0`

整个脚本执行需要一点时间
``` zsh
shiming@pro ➜  ~ curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0
curl: (56) Recv failure: Connection reset by peer
shiming@pro ➜  ~ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 1.4.0

Installing hyperledger/fabric-samples repo
...
```

**脚本功能详解**：  
**1. clone fabric-samples 1.4.0示例项目, 放在当前目录中**  

**2. 在`fabric-sample/bin`目录中有以下二进制文件被下载**  
* configtxgen
* configtxlator
* cryptogen
* discover
* idemixgen
* orderer
* peer
* fabric-ca-client

这个目录下有个文件叫`get-docker-images.sh`，它的功能是拉取指定的镜像  
fabric镜像：    
```
FABRIC_IMAGES=(fabric-peer fabric-orderer fabric-ccenv fabric-tools)
```

第三方镜像：  
```
THIRDPARTY_IMAGES=(fabric-kafka fabric-zookeeper fabric-couchdb fabric-baseos)
```

**3. 拉取docker镜像**  
如果你docker中本身没有其他image则`docker image ls`查看到的镜像列表应该如下，这些镜像都是从[docker hub](https://hub.docker.com/u/hyperledger/)上下载的  
``` zsh
shiming@pro ➜  ~ docker image ls
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
hyperledger/fabric-javaenv     1.4.0               3d91b3bf7118        8 weeks ago         1.75GB
hyperledger/fabric-javaenv     latest              3d91b3bf7118        8 weeks ago         1.75GB
hyperledger/fabric-tools       1.4.0               0a44f4261a55        2 months ago        1.56GB
hyperledger/fabric-tools       latest              0a44f4261a55        2 months ago        1.56GB
hyperledger/fabric-ccenv       1.4.0               5b31d55f5f3a        2 months ago        1.43GB
hyperledger/fabric-ccenv       latest              5b31d55f5f3a        2 months ago        1.43GB
hyperledger/fabric-orderer     1.4.0               54f372205580        2 months ago        150MB
hyperledger/fabric-orderer     latest              54f372205580        2 months ago        150MB
hyperledger/fabric-peer        1.4.0               304fac59b501        2 months ago        157MB
hyperledger/fabric-peer        latest              304fac59b501        2 months ago        157MB
hyperledger/fabric-ca          1.4.0               1a804ab74f58        2 months ago        244MB
hyperledger/fabric-ca          latest              1a804ab74f58        2 months ago        244MB
hyperledger/fabric-zookeeper   0.4.14              d36da0db87a4        5 months ago        1.43GB
hyperledger/fabric-zookeeper   latest              d36da0db87a4        5 months ago        1.43GB
hyperledger/fabric-kafka       0.4.14              a3b095201c66        5 months ago        1.44GB
hyperledger/fabric-kafka       latest              a3b095201c66        5 months ago        1.44GB
hyperledger/fabric-couchdb     0.4.14              f14f97292b4c        5 months ago        1.5GB
hyperledger/fabric-couchdb     latest              f14f97292b4c        5 months ago        1.5GB
```

如果要分开指定fabric, fabric ca和第三方image版本则可以用以下命令
``` zsh
curl -sSL http://bit.ly/2ysbOFE | bash -s <fabric> <fabric-ca> <thirdparty>
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 0.4.14
```

如果要方便使用`fabric-sample/bin`中的命令，需要把这个bin目录加入到PATH环境变量中
``` zsh
shiming@pro ➜  bin git:(bb39b6e) vi ~/.dotfile/.zprofile
shiming@pro ➜  bin git:(bb39b6e) source ~/.zshrc
shiming@pro ➜  bin git:(bb39b6e) configtxgen -version
configtxgen:
 Version: 1.4.0
 Commit SHA: d700b43
 Go version: go1.11.1
 OS/Arch: darwin/amd64
```


## 

## 官方培训和认证
linux基金会training目录：  
https://training.linuxfoundation.org/training/course-catalog/?_sf_s=hyperledger  

hyperledger官方目前提供的两个认证：  
https://www.hyperledger.org/resources/training





