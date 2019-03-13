# hyperledger fabric cookbook(working in progress)
本教程使用的版本为hyperledger fabric 1.4，这是目前最新的版本，也是第一个长期支持版本，官方保证后续能平滑升级。详情可以参考[what's new in hyperledger fabric 1.4](https://hyperledger-fabric.readthedocs.io/en/release-1.4/whatsnew.html)  

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


## 官方培训和认证
linux基金会training目录：  
https://training.linuxfoundation.org/training/course-catalog/?_sf_s=hyperledger  

hyperledger官方目前提供的两个认证：  
https://www.hyperledger.org/resources/training





