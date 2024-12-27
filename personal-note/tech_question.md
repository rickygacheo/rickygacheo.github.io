---
layout: default
title: 面试技术准备
nav_order: 3
---
## 讲讲Redis
### Redis的基础能力
Redis是分布式内存数据库，单节点能够满足10W+的QPS，是作为分布式缓存所常用的组件，它QPS强的原因有几个：
1、通信使用IO多路复用，请求不阻塞
    Redis网络通信通过底层使用IO多路复用，6.0版本以前使用单reactor单线程模型，6.0以后增加了单reactor多线程的处理模式(网络的io支持并发)，但是命令执行是单线程
为啥不用epoll？通过epoll可以增大连接数，epoll是将socket注册到epoll_ctl,通过epoll_wait函数等待返回已经就绪可以读取的数据socket，相比select不需要遍历。
2、数据全部在内存里，纯内存操作，非CPU密集型任务
3、数据结构合理

### JavaNIO
    Redis基于JavaNIO自己实现了简单的通信框架，通过Boss线程管理链接请求，并分配注册的channel到worker节点上，每个workder节点都能处理多路数据，执行回调处理方法。
    基于JavaNIO，使用了linux的select机制，多路的io流都是注册到selector的，然后所有的io流都会阻塞在selector的select内核方法上，当有数据IO就绪，唤醒select内核方法，
    select读取到有多少fd数据就绪，然后再从对应的socket上获取到数据，读取这个数据recvfrom是阻塞式的。
    至于为啥不用epoll？通过epoll可以增大连接数，epoll是将socket注册到epoll_ctl,通过epoll_wait函数等待返回已经就绪可以读取的数据socket，相比select不需要遍历。

### Redis应用
1、数据是先写入redis，还是先写入数据库。我们最后采取的是先写入redis，redis写入成功后，再写入数据库，这个考量是为了能够提升读写速度，即使是数据不一致，这个状态后续也会更新
2、数据缓存时效设置多久？以及怎么随机打散？因为上层的查询业务一般是按照一个集群id来批量获取，
3、redis缓存配置多大：
3c12g， 支持20w设备数据的缓存，最大750并发数，10000连接
redis高性能设计方案理解：

## 讲讲分库分表
水平分库分表，垂直分库分表
## 讲一讲JVM优化
垃圾回收算法：G1, 标注回收
存储里也有垃圾回收，通过LSMtree保存元数据，实际value需要通过GC算法进行回收
## 讲一讲Nginx

## 讲一讲Kudu
kudu在内存里是row式存储，在磁盘上是列式存储。我们做了什么

## 讲一讲Netty
netty是一个基于JavaNIO的通信框架，RPC是通过netty通信的，MP模块通过使用rpc通信提升图片传输速率


## 讲一讲Kafka

## 讲一讲系统演进的思考
1、缓存加速，使用redis，减少重复缓存使用量，检索性能提升（准备做）
2、VCM和VCN是使用两套统一鉴权
3、RPC消息统一，取代NSS消息体系
4、使用Hbase替换Mongodb，加速单数据或范围数据查找性能，工作量太大，这个一直没有实施

## 讲一讲存储架构
bloomfilter，lsmtree
ceph实现

### 讲讲树
B树：每个节点都有数据
B+树：只在叶子节点有数据，并且数据之间链路间是有跳转关系的，范围检索优势比较大
红黑树：一般是用在内存级的排序的map，内存操作快，相对排序层数不高
LSM-Tree：现在的kv型存储数据库的主流选择，能够极大的提升数据插入性能

### 讲一讲Solr
solr的日期检索优化range检索优化：
通过将solr的年月作为倒排的term，日作为payload存储，提升检索性能
用了什么样的分词器：分词器这个没有去研究，用的 默认的标准分词器，实际上我们存储的数据不需要分词
solr中做了哪些优化?
omitNorms设置为true，归一化参数，反应了词频和文档频率：
默认solr里会对因子做归一化处理？什么是归一化？对文档的权重，term的权重，term在文档长度中的频率等进行归一化的打分，取消这些可以节省空间和计算
omitPositions=true不涉及高亮的显示，不需要索引term的位置信息
通过设置docValue=true，提升facet查询的性能
solr权威指南2.5.2章节
Solr的Replica分别有哪几种模式：NRT, TLOG, PULL，分别适用于什么场景？
Solr的Document Routing机制是什么：暂时未懂 // TODO
Ignoring Commits from client applications in solrcloud
Achieved Replication Factor:solr会返回写入成功的replica数量，如果不满足要求，客户短根据这个数量将异常写入日志，等到恢复，再重新写入
distrib.singlePass Parameter：
Avoiding Distributed Deadlock：最大线程数要确保能够满足客户端所发起的线程数，比如：如果最大2个线程，那么客户端发起两个请求后，没有其他线程可用，则会触发死锁，等待可用线程
NodeRole有哪些：
    DataNode：数据节点
    OverseerNode：集群管理，分片管理，任务调度，配置管理；可以和DataNode同时存在，也可以
    CoordinateNode：接收请求，索引分片，无状态
Index更新，通过Json方式更新
Partial Document Updates：
Boosting a Term with "^": 通过^提升相关性，
DisMax Query Parser:支持自定义score，以及一般不返回错误，不需要特别规范的格式

solr做过什么优化？？

遗留问题：
1、怎么支持phrase检索？

### 讲一讲ElasticSearch

### 讲一讲Mongo
mongo设计：按照 每条数据大小评估总容量，按照索引占比，评估索引内存，按照缓存数据量，评估缓存大小
然后按照，服务器支持的磁盘和内存来评估服务器数量
怎么设置分区数？通过numInitTrunks来配置分片数
mongodb的分片大小建议控制在50GB以内，过大不利于分片迁移恢复

mongo优化：
查询性能优化：
1、SDK查询动态库，采用时间轴模式，优化查询性能。由于之前动态库数量较小直接使用skip+limit分页模式，在后来系统支持的数据规模到几十亿，在跳转到很大的页数时，查询非常耗时，改变了分页的方案，采用常用的时间轴模式，
按照页面数量+GroupSize进行分组，每组提前获取最后一个的id，组内的数据依旧按照skip+limit，这样会跳过较少的数据，从而提升10倍-100倍的查询性能。

### 短链高并发设计方案
发号：给每个长连接发放一个唯一的id，然后将id转为62进制或者128进制，即为短链
存储：存储每个短链和长链的映射关系。
发号器：通过Snowflake算法，0+时间+机器id+序列号生成唯一id，id作为短链url
建议每台机器上本地缓存映射关系，通过lru算法淘汰old url，再通过bloomfilter确认是否分配过，分配过先从redis获取映射关系，再从数据库获取映射关系，
如果没有分配过，通过算法分配，写入到数据库中，写入到redis中，需要通过redis分布式锁确保长链分配时锁定

### 设备介入模块内存优化
缓存30w设备接入，60w摄像机推送记录：
设备缓存在系统启动完成预热后，dump的设备缓存总保留大小低于1GB，相比当前占用2GB内存，减少50%
缓存预热时间不能高于当前耗时3min
优化点：
1、字符串编码优化：JDK8是如何保存String的：32位对象头+4bit*n个char+对齐8bit; JDK9:32位对象头+1-2bit*n+8bit对齐位，1个bit为Latin1的字符集，2bit为utf-16字符集
    优化方案：由于我们的编码全部为us_ascii字符集的标准，可以在1个bit表示，所以我们使用ascii编码后保存到bit中
2、重复字符串引用优化：外域设备信息是目录树的形式，域编码，目录编码deviceid，等都是重复的。重复的字符串改为引用，通过自定义AsciiCode类，在设备信息加载实例化时，new AsciiCode时通过内部的弱引用缓存<AsciiCode, WeakReference<AsciiCode>>获取，没有时创新新的，有的话就直接返回缓存，如果gc回收时，asciicode没有直接引用关系，weakhashmap可以直接回收掉。
3、JVM开启引用的指针压缩

### 检索链路性能优化
1、检索一年的动态库数据，MCS检索慢，修改索引结构
2、返回大量相似度90以上的动态库数据，如何在mongo里查询返回快速？

----