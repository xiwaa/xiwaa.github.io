---
title: 'GoIndex: 无需服务器的Google Drive目录索引程序'
date: 2019-09-17 16:07:51
tags: 
- Google Drive
categories: 工具
---

**转载文章**：[GoIndex：一个无需服务器的Google Drive目录索引程序](https://www.moerats.com/archives/1001/)

**说明：**`GoIndex`是一款部署在`Cloudflare Workers`的`Google Drive`目录索引程序，无需提供服务器，可以直接列出你谷歌网盘的所有文件，同时下载和访问也不需要挂梯子，也可以在线观看某些格式的视频文件，拿来玩玩也是可以的。

## 截图

![搭建成功主页演示](goindex1.png)

![在线播放演示](goindex2.png)

## 安装

**Github地址：**https://github.com/donwa/goindex

首先肯定是需要一个谷歌网盘的，个人的话容量有点小，如果想容量大的，可以免费申请一个无限容量的`Google Drive`网盘，申请方法查看→[传送门](https://www.moerats.com/archives/879)。

这里直接使用作者提供的快速部署方法，由于使用的是`rclone`的`google api`，高峰期难免会出现点问题，后面也会说下使用自己的`api`部署方法。

**1、获取Workers代码**
先访问：https://installen.gd.workers.dev/，点击`GET AUTH CODE`，验证你的谷歌账号获取`Auth code`。

然后开始输入你的`Auth code`，其它可以先默认，然后点击`BUILD`获取你的代码。

开头的相关参数，可自行修改：

```
#站点名称
"siteName": "GoIndex",
#根目录访问密码，如不设置留空即可
"root_pass": "moerats",
#根目录ID，默认整个网盘文件夹，如果只想列出根目录某个文件夹，那么填入该文件夹ID即可。
"root": "root"
```

文件夹`ID`获取方法，点击根目录某个文件夹，地址栏为：`https://drive.google.com/drive/folders/1C4Aro`，则`1C4Aro`为文件夹`ID`。

**2、部署到Workers**

首先登录`cloudflare`，官网→[传送门](https://www.cloudflare.com/)，然后首页右侧可以看到`Workers`入口。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(1).png)](https://www.moerats.com/usr/picture/cfwk(1).png)

点进去后新建一个`Workers`子域名，也就是你的专属域名，填入确认后就更改不了了，所以别乱填。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(2).png)](https://www.moerats.com/usr/picture/cfwk(2).png)
接下来点击`Create a Worker`创建一个`Worker`。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(3).png)](https://www.moerats.com/usr/picture/cfwk(3).png)
把你之前获取的`Workers`代码粘贴进去，然后可以在左上角双击修改域名，再点击`Save and Deploy`即可。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(4).png)](https://www.moerats.com/usr/picture/cfwk(4).png)
最后打开分配的子域名即可，此后修改配置可以自行修改`Workers`代码。



## 绑定域名

如果想绑定自己的域名，就需要先将自己的域名转到`cloudflare`，然后点自己的域名，解析一个`A`记录，`IP`地址随意。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(5).png)](https://www.moerats.com/usr/picture/cfwk(5).png)
然后在上方找到`Workers`，选择后，点击页面下的`Add route`，进行如下设置。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(6).png)](https://www.moerats.com/usr/picture/cfwk(6).png)
按照图中的格式，填上自己的域名，并选择对应的`Workers`程序即可。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(7).png)](https://www.moerats.com/usr/picture/cfwk(7).png)



## API部署

这里也可以使用自己的`API`部署，好处就是安全点，高峰期也不容易爆炸，当然安装会麻烦点，如果不经常使用的话，还是建议用上面的方法快速部署。到**[Google Developer Console](https://console.developers.google.com/)**进行如下操作。

**1、获取GDrive客户端**

首先需要选择一个项目，如果没有就先创建一个，然后启用`Google Drive API`，启用地址：[点击进入](https://console.developers.google.com/apis/api/drive.googleapis.com/overview)，注意这里使用个人账号操作。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(8).png)](https://www.moerats.com/usr/picture/cfwk(8).png)
再创建一个`OAuth client ID`，创建地址：[点击进入](https://console.developers.google.com/apis/credentials/oauthclient)。应用类型选择其他(`Other`)，名称自己填，这里博主填的`GoIndex`。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(9).png)](https://www.moerats.com/usr/picture/cfwk(9).png)
然后你会获得自己的客户端`ID`和客户端密钥，再复制下来。
[![请输入图片描述](https://www.moerats.com/usr/picture/cfwk(10).png)](https://www.moerats.com/usr/picture/cfwk(10).png)

**2、获取refresh_token**
这里使用`Rclone`来获取`refresh_token`，先登录`VPS`，先安装依赖，使用命令：

```
#CentOS系统
yum install p7zip unzip -y
#Debian/Ubuntu系统
apt install -y p7zip-full
```

再安装`Rclone`，使用命令：

```
curl https://rclone.org/install.sh | bash
```

开始配置，使用命令：

```
rclone config
```

会出现以下信息：

```
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n
name> Rats  #配置名称，随便填
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / 1Fichier
   \ "fichier"
 2 / Alias for an existing remote
   \ "alias"
 3 / Amazon Drive
   \ "amazon cloud drive"
 4 / Amazon S3 Compliant Storage Provider (AWS, Alibaba, Ceph, Digital Ocean, Dreamhost, IBM COS, Minio, etc)
   \ "s3"
 5 / Backblaze B2
   \ "b2"
 6 / Box
   \ "box"
 7 / Cache a remote
   \ "cache"
 8 / Dropbox
   \ "dropbox"
 9 / Encrypt/Decrypt a remote
   \ "crypt"
10 / FTP Connection
   \ "ftp"
11 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
12 / Google Drive
   \ "drive"
13 / Google Photos
   \ "google photos"
14 / Hubic
   \ "hubic"
15 / JottaCloud
   \ "jottacloud"
16 / Koofr
   \ "koofr"
17 / Local Disk
   \ "local"
18 / Mega
   \ "mega"
19 / Microsoft Azure Blob Storage
   \ "azureblob"
20 / Microsoft OneDrive
   \ "onedrive"
21 / OpenDrive
   \ "opendrive"
22 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
23 / Pcloud
   \ "pcloud"
24 / Put.io
   \ "putio"
25 / QingCloud Object Storage
   \ "qingstor"
26 / SSH/SFTP Connection
   \ "sftp"
27 / Union merges the contents of several remotes
   \ "union"
28 / Webdav
   \ "webdav"
29 / Yandex Disk
   \ "yandex"
30 / http Connection
   \ "http"
31 / premiumize.me
   \ "premiumizeme"
Storage> 12  #选择12，Google Drive
client_id> 850428  #填上你的Google Drive客户端ID
client_secret> D72gPc  #填上你的Google Drive客户端密匙
Choose a number from below, or type in your own value
 1 / Full access all files, excluding Application Data Folder.
   \ "drive"
 2 / Read-only access to file metadata and file contents.
   \ "drive.readonly"
   / Access to files created by rclone only.
 3 | These are visible in the drive website.
   | File authorization is revoked when the user deauthorizes the app.
   \ "drive.file"
   / Allows read and write access to the Application Data folder.
 4 | This is not visible in the drive website.
   \ "drive.appfolder"
   / Allows read-only access to file metadata but
 5 | does not allow any access to read or download file content.
   \ "drive.metadata.readonly"
scope> 1 
ID of the root folder
Leave blank normally.
Fill in to access "Computers" folders. (see docs).
Enter a string value. Press Enter for the default ("").
root_folder_id> 
Service Account Credentials JSON file path 
Leave blank normally.
Needed only if you want use SA instead of interactive login.
Enter a string value. Press Enter for the default ("").
service_account_file> 
Edit advanced config? (y/n)
y) Yes
n) No
y/n> n  #输入n
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes
n) No
y/n> n  #输入n
If your browser doesn't open automatically go to the following link: https://accounts.google.com/o/  #打开该地址获取code
Log in and authorize rclone for access
Enter verification code>hjdd   #输入你获取到的code
Configure this as a team drive?
y) Yes
n) No
y/n> n  #输入n
--------------------
[Rats]
type = drive
client_id = 85042871
client_secret = D72gPc
scope = drive
token = {"access_token":"y902Z"}  #记住里面的refresh_token参数
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y  #输入y
Current remotes:

Name                 Type
====                 ====
Rats                 drive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q  #输入q保存退出
```

此时我们在配置过程中就获取到了`refresh_token`参数，如果忘了的可以查看配置文件。

不知道配置文件在哪的，可以使用命令：

```
rclone config file | grep rclone.conf
```

**3、获取并修改Workers代码**
`Workers`代码可以点击该地址查看→[传送门](https://github.com/donwa/goindex/blob/master/index.js)，先复制代码，部分修改可参考上面，不同的是，需要填上自己的`api`信息，如下：

```
#填上自己的api客户端，密匙和获取到的refresh_token
“client_id”：“20226481”，
“client_secret”：“X4Z3ca8”，
“refresh_token”：“”，
```

最后就可以部署`Workers`代码了，方法参考上面的快速部署。

