## Nginx 安装和配置

Nginx是一款轻量级的 Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个 BSD-like 协议下发行。其特点是占有内存少，并发能力强等。

### 安装准备

由于nginx的一些模块依赖一些lib库，所以在安装nginx之前，必须先安装这些lib库，这些依赖库主要有g++、gcc、openssl-devel、pcre-devel和zlib-devel 所以执行如下命令安装：

#### gcc

安装nginx需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需要安装gcc。
```
$   yum install gcc-c++
```

#### PCRE
PCRE(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式，所以需要在linux上安装pcre库。
```
$   yum install -y pcre pcre-devel
```

#### zlib

zlib库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip，所以需要在linux上安装zlib库。
```
$   yum install -y zlib zlib-devel
```

#### OpenSSL
OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。 
nginx不仅支持http协议，还支持https（即在ssl协议上传输http），所以需要在linux安装OpenSSL库。
```
$   yum install -y openssl openssl-devel
```

### 安装Nginx

从官网下载最新版的nginx
```
$   wget http://nginx.org/download/nginx-1.14.2.tar.gz
```

解压nginx压缩包
```
$   tar -zxvf nginx-1.14.2.tar.gz  
```

此时会产生一个nginx-1.14.2 目录，这时进入nginx-1.14.2 目录
```
$   cd  nginx-1.14.2
```

接下来安装，使用--prefix参数指定nginx安装的目录,make、make install安装
```
$   ./configure  $默认安装在/usr/local/nginx   
$   make  
$   make install 
```

如果没有报错，顺利完成后，最好看一下nginx的安装目录
```
$   whereis nginx  
```

此时进入/usr/local/nginx/sbin目录，执行命令，启动nginx
```
$   ./nginx
```

其它命令
```
$   ./nginx -v              # 查看 Nginx 版本
$   ./nginx -s reload       # 重新载入配置文件
$   ./nginx -s reopen       # 重启 Nginx
$   ./nginx -s stop         # 停止 Nginx
$   ./nginx -t              # 检查配置文件nginx.conf的正确性
```

安装完成就可以从浏览器输入服务器的公网ip地址访问了，但是我在这里遇到个坑，查了一下，需要在服务器的安全组规则中，添加一条http的规则。

<img src="https://wx2.sinaimg.cn/mw1024/a6944a0egy1g14muwau1dj20j60l50t5.jpg" width = "300"/>

现在在浏览器的地址栏输入ip地址，就会出现nginx的标志啦。

![](https://wx1.sinaimg.cn/mw1024/a6944a0egy1g14muwcpgpj20y90d6mxy.jpg)

### 虚拟主机的配置

#### 将自定义域名指向服务器 ip 地址

在域名解析中添加一条A记录

<img src="https://wx2.sinaimg.cn/mw1024/a6944a0egy1g14muwf8dzj20mq0g9weo.jpg" width = "300"/>

#### 建立虚拟主机存放网页的根目录

并创建首页文件index.html

![](https://wx3.sinaimg.cn/mw1024/a6944a0egy1g14muwcow1j20ms08tt8t.jpg)

#### 修改 nginx.conf

![](https://wx4.sinaimg.cn/mw1024/a6944a0egy1g14muwdaihj20mp0c30tl.jpg)

配置文件结尾的最后一个`}`之前加入以下语句
```
include vhost/*.conf;
```

之后在 conf 目录下创建一个 vhost 文件夹

#### 编辑每个域名的配置文件（每个虚拟主机的配置文件）

在 vhost 目录下创建 `api.iamliuyu.com.conf` 文件，并添加入一下内容

![](https://wx1.sinaimg.cn/mw1024/a6944a0egy1g14muwd8jjj20f309bweh.jpg)

#### 重启服务器

![](https://wx4.sinaimg.cn/mw1024/a6944a0egy1g14muwcmsrj20mt06ojrl.jpg)

在浏览器中输入 api.iamliuyu.com ，如果出现跳转至 index.html 文件就算成功啦。

![](https://i.loli.net/2019/03/16/5c8c9f4d79def.png)





