---
title: redis安装及使用
date: 2019-06-22 15:16:51
tags: Redis
categories: 中间件
---
在上一节中已经初步了解了Redis的作用以及为什么要使用Redis。下面开始学习如何安装使用Redis。

## Redis安装
### Windows环境下安装
安装教程：[https://jingyan.baidu.com/article/0f5fb099045b056d8334ea97.html](https://jingyan.baidu.com/article/0f5fb099045b056d8334ea97.html)
### Linux环境下安装
<!--more-->
Redis作为一个消息缓存中间件，是必定要部署在Linux服务器中的。因此我使用Centos7系统来安装Redis并学习其基本使用方法。
**首先去官网下载Redis**
官方网站：[http://redis.io/](http://redis.io/) 
官方下载：[http://redis.io/download](http://redis.io/download) *可根据需要下载不同版本*
我下载的是4.0版本
{% img redis2-1 /images/Redis-pic/Redis2-1.PNG 700 500%}
**Linux解压安装**
打开xftp传输工具将下载好的安装包复制到`/usr/local/`目录下
{% img redis2-2 /images/Redis-pic/Redis2-2.PNG 700 500%}
打开`xshell`工具远程访问服务器，执行命令
```
cd /usr/local
```
进入`local`目录下
```
ll
```
查看当前目录下的文件
{% img redis2-3 /images/Redis-pic/Redis2-3.PNG 700 500%}
可以看到`redis-4.0.14.tar.gz`这个压缩文件，执行命令解压
```
tar zxvf redis-4.0.14.tar.gz
```
解压完成后在当前目录下使用`ll`命令可查看多出一个`redis-4.0.14`文件，这是Redis的安装文件。由于Redis是使用C语言编写的，要使其运行还需编译。执行以下命令
```
cd /redis-4.0.14
make MALLOC=libc
```
看到如下图所示，即编译成功
{% img redis2-4 /images/Redis-pic/Redis2-4.PNG %}
如果出错，执行以下命令安装gcc，编译C语言需依赖gcc环境
```
yum -y install gcc automake autoconf libtool make
```
接着，安装编译后的文件，执行命令,其中`/usr/local/redis`为安装后的文件位置，我们将其与Redis安装文件放在一起
```
make PREFIX=/usr/local/redis install
```
此时，在`/usr/local/`目录下就存在Redis文件和redis-4.0.14这两个文件了，我们进入Redis文件中的bin目录查看文件
{% img redis2-5 /images/Redis-pic/Redis2-5.PNG 700 500%}
其中`redis-server`为Redis服务端启动程序，`redis-cli`为Redis客户端启动程序。此时Redis就已经安装完成了！

## Redis启动
在启动前，先进入目录`/usr/local/redis-4.0.14`，发现此安装文件下有一个`redis-conf`文件，此文件为Redis的启动配置文件，我们需要将它复制到编译后的Redis安装文件中去。
{% img redis2-6 /images/Redis-pic/Redis2-6.PNG 700 500%}
在此目录下执行复制命令`cp redis.conf /usr/local/redis`，此时`/usr/local/redis`目录下就有了bin文件夹和redis.conf这两个文件了。
进入`/usr/local/redis`文件中bin目录执行`./redis-server ./redis.conf`命令启动Redis服务端，由于Redis默认不是以守护进程的方式运行，启动服务端后此命令窗口将被占用，我们启动或复制一个新的窗口用于启动Redis客户端，执行命令`./redis-cli -h ip地址 -p 端口号 -a 密码`，在本机运行可不写ip地址，端口号默认为6379，无密码可不写（redis3.0版本之后必须使用密码！）。
这样Redis不是以守护进程的方式运行，它将独占窗口使我们需要新打开窗口进行客户端的操作非常麻烦。这时我们可以对Redis进行相关的配置，即`redis-conf`这个文件。

## Redis相关配置
进入`/usr/local/redis`目录编辑redis-conf文件，执行命令
```
cd /usr/local/redis
vi redis-conf
```
按i进入文本编辑模式，修改完成后按ESC，输入:w保存不退出文件或输入:wq保存并退出

redis-conf配置项说明如下:
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    `daemonize no`
	Redis采用的是单进程多线程的模式。当redis.conf中选项daemonize设置成yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。但当daemonize选项设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出。服务端开发的大部分应用都是采用后台运行的模式。
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    `pidfile /var/run/redis.pid`
3. 指定Redis监听端口，默认端口为6379，为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    `port 6379`
4. 绑定可访问的主机地址（一般需要将其注释，保证远程客户端可访问！！！）
    `bind 127.0.0.1`
5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    `timeout 300`
6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
    `loglevel verbose`
7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
    `logfile stdout`
8. 设置数据库的总数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    `databases 16`
9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    `save <seconds> <changes>`
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
 
10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    `rdbcompression yes`
11. 指定本地数据库文件名，默认值为dump.rdb
    `dbfilename dump.rdb`
12. 指定本地数据库存放目录
    `dir ./`
13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    `slaveof <masterip> <masterport>`
14. 当master服务设置了密码保护时，slav服务连接master的密码
    `masterauth <master-password>`
15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    `requirepass foobared`
16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    `maxclients 128`
17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    `maxmemory <bytes>`
18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    `appendonly no`
19. 指定更新日志文件名，默认为appendonly.aof
    `appendfilename appendonly.aof`
20. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折中，默认值）
    `appendfsync everysec`
 
21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
     `vm-enabled no`
22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
     `vm-swap-file /tmp/redis.swap`
23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
     `vm-max-memory 0`
24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
     `vm-page-size 32`
25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
     `vm-pages 134217728`
26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
     `vm-max-threads 4`
27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    `glueoutputbuf yes`
28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    `hash-max-zipmap-entries 64`
    `hash-max-zipmap-value 512`
29. 指定是否激活重置哈希，默认为开启
    `activerehashing yes`
30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    `include /path/to/local.conf`

## Redis中的内存维护策略
redis作为优秀的中间缓存件，时常会存储大量的数据，即使采取了集群部署来动态扩容，也应该即使的整理内存，维持系统性能。在redis中有两种解决方案，
一.为数据设置超时时间，

二.采用LRU算法动态将不用的数据删除。内存管理的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做LRU，操作系统会根据哪些数据属于LRU而将其移出内存而腾出空间来加载另外的数据。

**Redis内存不足的缓存淘汰策略:**
```
1. volatile-lru：设定超时时间的数据中,删除最不常使用的数据.

2. allkeys-lru：查询所有的key中最近最不常使用的数据进行删除，这是应用最广泛的策略.

3. volatile-random：在已经设定了超时的数据中随机删除.

4. allkeys-random：查询所有的key,之后随机删除.

5. volatile-ttl：查询全部设定超时时间的数据,之后排序,将马上将要过期的数据进行删除操作.

6. noeviction：如果设置为该属性,则不会进行删除操作,如果内存溢出则报错返回.（Redis的默认属性）

7. volatile-lfu：从所有配置了过期时间的键中驱逐使用频率最少的键

8. allkeys-lfu：从所有键中驱逐使用频率最少的键
```
## Redis相关命令
* [菜鸟教程-Redis命令](https://www.runoob.com/redis/redis-commands.html)












