## 部署 FTP 服务

vsftp 被公认目前最好的 ftp 之一，搭建它是为了我们可以让虚拟机与主机更加方便的通信。

#### 安装

```
yum install vsftpd
```

立即启动
```
systemctl start vsftpd
```

跟随系统启动而启动
```
systemctl enable vsftpd
```

#### 配置

修改配置文件
```
vim /etc/vsftpd/vsftpd.conf
```

vsftpd 常用配置

* 匿名用户的常用配置
```
annoymous_enable=YES  #是否启用匿名用户
anno_upload_enable=YES #是否允许匿名用户上传权限
anno_mkdir_write_enable=YES #是否允许匿名用户可创建目录及其文件
anno_other_write_ebable=YES #匿名用户是否除了写权限是否拥有删除和修改的权限 
anno_world_readable_only=YES #匿名用户是否拥有只读权限  
no_anno_password=YES #匿名用户是否跳过密码检测  
anno_umask=077 #匿名用户创建文件的掩码权限  
```
* 系统用户的配置 
```
local_enable=YES #是否启用本地用户  
write_enable=YES #本地用户是否可写 
local_mask=022 #本地用户的掩码信息
```
* 禁锢所有ftp用户在其本地目录下  
```
chroot_local_user=YES  
```
* 禁锢文件中指定的ftp本地用户在其本地目录下  
```
chroot_list_enable=YES 
chroot_list_file=/etc/vsftpd/chroot_list
```
* 改变上传文件的用户 
```
chown_uploads=YES  
chown_username=whoever  
```
* 是否启用控制用户登录的列表信息  
```
userlist_enable=YES 
userlist_deny=YES|NO 
此配置文件默认为:/etc/vsftpd/user_list
```

重启 vsftp 服务
```
systemctl restart vsftpd.service
```

#### 添加用户

添加用户，并配置根目录为 /data/www 是用户访问目录
```
useradd -d /data/www -s /sbin/nologin liuyuftp
```

设置密码
```
passwd xxx
```

编辑 chroot_list 目录，将用户名添加上去即可     
```
vi /etc/vsftpd/chroot_list
```

修改目录权限
```
chown -R liuyuftp /data/www 将/data/www 目录的所有者改为liuyuftp
chmod -R 755 /data/www
```

![](https://i.loli.net/2019/03/16/5c8cfee4ad290.png)