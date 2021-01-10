## MySQL 数据库的安装和配置

安装前，检测系统是否自带安装 MySQL，如果有安装，那可以选择进行卸载
```
rpm -qa | grep mysql
```

#### 安装

下载 mysql 的 repo 源，进行安装
```
#  wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm 
#  rpm -ivh mysql57-community-release-el7-8.noarch.rpm 
#  yum -y install mysql-server 
```

#### 配置

默认配置文件路径
``` 
配置文件：/etc/my.cnf 
日志文件：/var/log/mysqld.log 
服务启动脚本：/usr/lib/systemd/system/mysqld.service 
socket文件：/var/run/mysqld/mysqld.pid
```

配置 `my.cnf`        
```
vim /etc/my.cnf
```

```
[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
server_id = 1
expire_logs_days = 3
 
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
 
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

启动 mysql 服务
```
systemctl start mysqld.service 
```
```
systemctl restart mysqld.service #重启
systemctl stop mysqld.service   #停止运行
systemctl status mysqld     #查看状态
mysqladmin --version    #查看版本
```

#### 重置密码

*第一次登陆 ，需要重置密码，要不什么也不能操作*

* 查看默认密码
```
grep "password" /var/log/mysqld.log 
```

* 输入 `mysql -u root -p` 进入

* 最后记得刷新权限 `flush privileges` 

#### 远程连接设置

把在所有数据库的所有表的所有权限赋值给位于所有 IP 地址的 root 用户

```
mysql> grant all privileges on *.* to root@'%'identified by 'password';
```

记得在阿里云实例的安全组规则中添加入站规则！

![](https://i.loli.net/2019/03/16/5c8cff0e555af.png)

<!-- https://blog.csdn.net/z13615480737/article/details/78907697（密码改简单的方法） -->
