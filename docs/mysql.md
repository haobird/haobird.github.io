# mysql

## 基础


## 高可用

[参考链接](https://www.cnblogs.com/rouqinglangzi/p/10921982.html)

### 方案

#### 方案一：共享存储
一般共享存储采用比较多的是 SAN/NAS 方案。

#### 方案二：操作系统实时数据块复制
这个方案的典型场景是 DRBD，DRBD架构(MySQL+DRBD+Heartbeat)

#### 方案三：主从复制架构
主从复制(一主多从)

MMM架构(双主多从)

MHA架构(多主多从)

#### 方案四：数据库高可用架构

这种方式比较经典的案例包括 MGR(MySQL Group Replication)和 Galera 等，最近业内也有一些类似的尝试，如使用一致性协议算法，自研高可用数据库的架构等。

1.MGR(MySQL Group Replication，MySQL官方开发的一个实现MySQL高可用集群的一个工具。第一个GA版本正式发布于MySQL5.7.17中)

2.Galera


#### 其它方案：MySQL Cluster和PXC


![方案图](https://pic2.zhimg.com/v2-b7c3b1826ed555d03fc77a40a61c04cd_b.jpg)

### 二、部分常见方案的简介

#### 1.Mysql主从架构

![主从架构](https://pic1.zhimg.com/v2-12e7074fb005ca86a08b8c16b9386c4c_b.jpg)

#### 2.MHA 架构(Master High Availability Manager and Toolsfor MySQL)

MHA(Master High Availability Manager and Toolsfor MySQL)目前在Mysql高可用方面是一个相对成熟的解决方案。它是日本的一位MySQL专家采用Perl语言编写的一个脚本管理工具，该工具仅适用于MySQLReplication 环境，目的在于维持Master主库的高可用性。

MHA是基于标准的MySQL复制(异步/半同步)。

MHA是由管理节点(MHA Manager)和数据节点(MHA Node)两部分组成。

MHA Manager可以单独部署在一台独立机器,也可以部署在一台slave上。

![MHA架构](https://pic2.zhimg.com/v2-8b64ed083048bef3eaa58d902c7ec9ad_b.jpg)

#### 3.MMM 架构(Master-Master replication manager for Mysql)
可参考：MySQL-MMM实现MySQL高可用

MMM，全称为Master-Master replication manager for Mysql，是一套支持双主故障切换和双主日常管理的脚本程序，MMM使用Perl语言开发。主要用来监控和管理MySQL Master-Master(双)复制。特别适合DBA做维护等需要主从复制的场景，通过双主架构避免了重复搭建从库的麻烦。虽然叫做双主复制，但是业务上同一时刻只允许对一个主进行写入，另一台备选主上提供部分读服务，以加速在主主切换时备选主的预热。

(MMM好像不靠谱，据说不稳定，但还是有人在用)

![MMM架构](https://pic2.zhimg.com/v2-9dcca4e963f5f4728224b39c3fa410a5_b.jpg)

MMM优缺点
　　优点：高可用性，扩展性好，出现故障自动切换，对于主主同步，在同一时间只提供一台数据库写操作，保证的数据的一致性。
　　缺点：Monitor节点是单点，可以结合Keepalived实现高可用。

#### 4.DRBD 架构(MySQL+DRBD+Heartbeat)

### 三、读写分离解决方案

- 客户端解决方案（应用层）：TDDL、 Sharding-Jdbc (常用shardding-jdbc)
- 中间件解决方案（代理层）：mysql proxy、mycat、altas (常用mycat)

![](https://pic4.zhimg.com/v2-c44381145ae2cfd91223d86b57e54ddb_b.jpg)

客户端解决方案的特点：

优点：

　　1、程序自动完成，数据源方便管理

　　2、不需要维护，因为没用中间件

　　3、理论支持任何数据库 （sql标准）

缺点：

　　1、增加了开发成本、代码有入侵

　　2、不能做到动态增加数据源

　　3、程序员开发完成，运维参与不了。

中间件解决方案的特点：

优点：

　　1、数据增加了都程序没用任何影响

　　2、应用层（程序）不需要管数据库方面的事情

　　3、增加数据源不需要重启程序

缺点：

　　1、程序依赖中间件，导致切换数据库变的困难

　　2、增加了proxy 性能下降

　　3、增加了维护工作、高可用问题。