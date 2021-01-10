## 安装 Redis

[Redis](https://redis.io/) 是完全开源免费、遵守 BSD 协议的一个高性能 key-value 数据库。

Redis 与其他 key - value 缓存产品有以下三个特点

* Redis 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用
* Redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储
* Redis 支持数据的备份，即 master-slave 模式的数据备份

### 安装 redis

#### 下载 redis 安装包
```
wget http://download.redis.io/releases/redis-5.0.4.tar.gz
```

#### 解压压缩包
```
tar -xzvf redis-5.0.4.tar.gz
```
#### yum 安装 gcc 依赖
```
yum install gcc
```
这边我在配置 nginx 服务器的时候安卓过，所以此步略过

#### 编译安装
先进入 redis 目录
```
cd redis-5.0.4
```
编译安装
```
make
```
测试编译结果
```
make test
```

### 启动 redis

切换到 redis/src 目录下

#### 直接启动

```
./redis-server
```

![](https://i.loli.net/2019/04/05/5ca63376c73be.png)

如上图：redis 启动成功，但这种启动方式需要一直打开窗口，不能进行其他操作。按 ctrl + c 可以关闭窗口。

#### 后台进程启动

修改 `redis.conf` 文件
```
daemonize no => daemonize yes
```

![](https://i.loli.net/2019/04/05/5ca63376af71a.png)

指定配置文件运行 redis
```
./redis-server ../redis.conf
```

使用自带的 `redis-cli` 客户端连接 `redis-server` 进行测试：
```
./redis-cli
```

![](https://i.loli.net/2019/04/05/5ca63376c7fc5.png)

关闭 redis 进程

首先使用 `ps -aux | grep redis` 查看 redis 进程

使用 `kill` 命令杀死进程

![](https://i.loli.net/2019/04/05/5ca63376bc930.png)


