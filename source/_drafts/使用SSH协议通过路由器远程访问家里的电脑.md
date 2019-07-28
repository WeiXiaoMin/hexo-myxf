---
title: 使用SSH协议通过路由器远程访问家里的电脑
author: 暮野夕风
top: true
cover: false
date: 2019-07-26 01:27:39
img:
coverImg:
summary:
categories: 技术
tags:
  - SSH
  - freeSSH
  - window系统
  - 远程访问
  - 路由器
---

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=1315901717&auto=1&height=66"></iframe>

## 技术背景

需要掌握命令行终端的使用和常用的 Shell 命令。

## 需求说明

之前在 Vultr 搭建了 VPN，搭建过程是通过 SSH 远程连接实现的。受此启发，便想着能不能在自己的电脑上搭建 SSH 服务，通过 SSH 连接家里电脑的终端，以执行命令或传输文件。

## 实现步骤

### 在路由器上配置端口映射

1. 在浏览器地址栏中输入 “192.168.1.1” 并打开，进入路由器设置页面

2. 百度搜索“ip”查看你的外网 IP （一般位于百度搜索的第一个结果），并查看路由器设置中的 IP 和百度搜索的 IP 是否一致，一致则说明该路由器分配到的是外网 IP。假设我的是“222.222.22.2”。

3. 查看路由器给自己电脑分配的内网 IP，我的主机名是“wxm-PC”，内网 IP 是“192.168.1.5”，如图：

   ![](https://ae01.alicdn.com/kf/H48fbb7963a8848a5939d488a093854cev.png)

4. 进入“静态分配客户端配置”项，给电脑设置固定的内网 IP：

   ![](https://ae01.alicdn.com/kf/Hfc878cdfdfa6474f8675534fadf3c392h.png)

5. 进入“虚拟主机配置”项，设置端口映射，即将外网端口”8222“映射到 SSH 的端口”22“。外网端口自己定义，一般要设大一些，避免冲突，这里我设置为”8222“。

   ![](https://ae01.alicdn.com/kf/Hc2351dc45c7b4bd389d46452c333ef3de.png)

### 搭建 SSH 服务器

window7 系统没有自带 SSH 服务器软件，需要下载一款。这里用的是 freeSSH。

1. 下载 [freeSSHd.exe](http://www.freesshd.com/?ctt=download)，安装过程中会为 SSH 服务器生成密钥，以及为 freeSSHD 添加系统服务，需要点击确认。

2. 下载 [freeUserImport.exe](http://www.freesshd.com/?ctt=download) 软件，点击运行，导入登录用户。

3. 设置 SSH 参数。”Listen address“设置为内网 IP，使用默认的”0.0.0.0“也行。”Port“为默认的22，”Command shell“默认为 cmd ，这里我设置成了 git-bash（后面要“--login -i”参数）。

   ![](https://ae01.alicdn.com/kf/Haee0ce72e6194acc9c06e0f1c3b33105u.png)

4. 添加用户信息。“Authorization”设置为“Public key(SSH only)”，即用密钥登录，相比用密码登录的安全系数要高。“User can use” 这里，“Shell”指可以执行终端的命令，“SFTP”指可以通过命令传输文件。

   ![](https://ae01.alicdn.com/kf/H5426dcf9f7e04f68b82c7de692c6d920n.png)

5. 启动服务。打开“Server status”标签，如果是下图的状态，则点击“SSH server is not running”下方的“Click here to start it”来启动服务。

   ![](https://ae01.alicdn.com/kf/Hc36ee729366a479b98d5f5560c32d19f3.png)
   
6. 如果启动服务报错：generic error for access violation，表明服务被占用，需要关闭服务：按快捷键“Ctrl+Alt+Delete”启动任务管理器，点击服务进入服务设置页面，找到“freeSSHService”，关闭服务，再重复步骤 5，直到出现“SSH server is running”状态。

   ![](https://ae01.alicdn.com/kf/H6c9fd1833cdb4ff7a1b54f4641637348o.png)

   ![](https://ae01.alicdn.com/kf/H2e103b4317f14209b698973b49e3593ex.png)

   ![](https://ae01.alicdn.com/kf/H862988ab3e2049a8a3a41a7eebf39976C.png)

### 配置密钥

家里启动了 SSH 服务的是“被访问”的电脑，我们要在“访问”的电脑上生成密钥，通过以下命令生成：

```bash
ssh-keygen -t rsa
```

执行完之后会在用户目录下的“.ssh”目录中生成密钥和公钥文件，假设是 id_rsa 和 id_rsa.pub 文件。在“.ssh”目录中的 config 文件（没有则创建）添加如下配置（以Mac系统为例）：

```
Host wxm # 主机名
User wxm # 用户名
HostName 222.222.22.2 # 外网 IP
Port 8222 # 外网端口
IdentityFile ~/.ssh/id_rsa # 私钥路径
```

将公钥文件（id_rsa.pub）复制到被访问的电脑的 freeSSH 软件安装目录下，并重命名为之前在”Users“配置中添加的用户名，这里是”wxm“（连后缀一起改）。

### 远程连接

打开访问的电脑终端，执行以下命令建立 SSH 连接：

```bash
ssh wxm
```

”wxm“指的是在”.ssh/config“文件中配置的”Host“的值。

退出连接则在终端中输入`exit` 命令。

在进行 SSH 连接之前要确保”被访问“的电脑中的 SSH 服务已经开启。

### 使用 sftp 服务上传下载文件

前提是在 freeSSH 的”User can use“选项中勾选了”SFTP“。

1. 上传：`put /path/filename(本地主机) /path/filename(远端主机)`
2. 下载：`get /path/filename(远端主机) /path/filename([本地主机](https://baike.baidu.com/item/本地主机))`

## 参考资料

1. [Windows 系统中安装 SSH 服务](https://www.cnblogs.com/chenmingjun/p/8535067.html)
2. [windows freeSSHd搭建SFtp服务器](https://blog.csdn.net/lsfreeing/article/details/82424115)
3. [百度百科](https://baike.baidu.com/item/sftp)
