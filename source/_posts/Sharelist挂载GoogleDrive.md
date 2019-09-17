---
title: Sharelist挂载GoogleDrive
date: 2019-09-09 16:14:35
tags: Google Drive
categories: 工具
---

**转载文章**：[ShareList（原 GDLIST） - 支持 Google Drive/OneDrive 多网盘同时挂载](https://www.moewah.com/archives/1462.html)

`ShareList` 原名 `GDlist`是一个易用的网盘工具，支持快速挂载 Google Drive、OneDrive ，可通过插件扩展功能。不占服务器空间；可多个 `Google Drive` 和 `OneDrive` 同时挂载到网盘；支持直链下载；在线预览（图片、视频、音频）。

Github 开源地址：[https://github.com/reruin/sharelist](https://www.moewah.com/go/aHR0cHM6Ly9naXRodWIuY29tL3JlcnVpbi9zaGFyZWxpc3Q=)



## 截图

![搭建成功主页展示](sharelist1.png)

![管理界面展示](sharelist2.png)

![管理界面展示](sharelist3.png)

## 特性

- 多种**网盘系统**快速挂载。
- 支持虚拟目录和虚拟文件。
- 支持目录加密。
- 插件机制。
- 国际化支持。
- WebDAV 导出。



## 脚本安装

**系统要求：**`CentOS`、`Debian`、`Ubuntu`

运行命令：

```
#Debian/Ubuntu系统
apt-get -y install git

#CentOS/RHEL系统
yum -y install git

#下载源码安装
git clone https://github.com/reruin/sharelist.git
cd sharelist && bash install.sh
```

完成后，访问 `http://ip:33001`，进入界面开始设置，记住网盘文件夹要共享一下，不然会出现 `500` 错误。WebDAV 目录则为 `http://localhost:33001/webdav`



## Docker 安装

**1、安装 Docker 和 Docker Compose**

```
#安装Docker
curl -sSL https://get.docker.com/ | sh
service docker start
#安装Docker Compose
curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

**2、安装 ShareList**

```
#Debian/Ubuntu系统
apt-get -y install git

#CentOS/RHEL系统
yum -y install git

#安装GDLIST
git clone https://github.com/reruin/sharelist.git
cd sharelist
docker-compose up -d
```

最后访问 `http://IP:33001` 即可。



## 添加域名 SSL

如果想用域名查看的，可以用 `Caddy` 反代，并自动添加 `SSL` 证书。首先需要解析一个域名到服务器上。

安装 `Caddy`

```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
#备用地址
wget -N --no-check-certificate https://www.moewah.com/source/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
```

编辑配置文件

```
#以下全部内容是一个整体，修改域名、IP、邮箱后一起复制到SSH运行！
echo "https://xx.com {
 tls admin@moewah.com
 proxy / 111.111.111.111:33001 {
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Proto {scheme}
  }
 log /var/log/caddy.log
 gzip
}" > /usr/local/caddy/Caddyfile
```

启动 `Caddy`

```
/etc/init.d/caddy restart
```

最后就可以通过域名 `https` 访问了。



## 功能说明



### 挂载对象

首次使用时将提示选在挂载源，选择挂载源，填入对应路径即可。 系统内置了本地路径（FileSystem）挂载源。



### 目录加密

在需加密目录内新建 `.passwd` 文件，`type` 为验证方式，`data` 为验证内容。

```
type: basic 
data: 
  - user1:111111 
  - user2:aaaaaa 
```

`basic` 是内置的验证方式，使用用户名密码对进行判断，上面的例子中可使用 `user1` 的密码为 `111`，`user2` 的密码为 `aaaaaa`。请参考 [example/SecretFolder/.passwd](https://www.moewah.com/go/aHR0cHM6Ly9naXRodWIuY29tL3JlcnVpbi9zaGFyZWxpc3QvYmxvYi9tYXN0ZXIvZXhhbXBsZQ==)。



### 虚拟目录

在需创建虚拟目录处新建`目录名.d.ln` 文件。 其内容为`挂载源:挂载路径` 如：创建虚拟目录指向本地 `/root`。

```
fs:/root 
```

其中挂载源 `fs` 表示本地磁盘，`/root` 代表路径。

再如：创建虚拟目录指向 Google Drive 的某个共享文件夹

```
gd:0BwfTxffUGy_GNF9KQ25Xd0xxxxxxx 
```

`gd` 是 Google Drive 的挂载源标示，冒号后的是共享文件夹 ID。



### 虚拟文件

与虚拟目录类似，目标指向具体文件。
在需创建虚拟文件处新建`文件名.后缀名.ln` 文件。 其内容为`挂载源:挂载路径`。 如：创建一个 `ubuntu_18.iso` 的虚拟文件，请参考 [example/linkTo_download_ubuntu_18.iso.ln](https://www.moewah.com/go/aHR0cHM6Ly9naXRodWIuY29tL3JlcnVpbi9zaGFyZWxpc3QvYmxvYi9tYXN0ZXIvZXhhbXBsZQ==)。



### WebDAV

系统部分支持 WebDAV。可使用的功能包括列目录、展示内容、权限校验。由于系统仅做挂载用途，不支持写入、删除、重命名、复制等操作。默认根路径为 `/webdav`，可在后台修改 WebDAV 的路径。

**注意事项：**
windows 挂载 webdav 可读取文件最大为 50M，请[参考](https://www.moewah.com/go/aHR0cHM6Ly9hbnN3ZXJzLm1pY3Jvc29mdC5jb20vZW4tdXMvaWUvZm9ydW0vYWxsL2Vycm9yLTB4ODAwNzAwZGYtdGhlLWZpbGUtc2l6ZS1leGNlZWRzLXRoZS1saW1pdC9kMjA4YmJhNi05MjBjLTQ2MzktYmQ0NS1mMzQ1ZjQ2MjkzNGY=)修改