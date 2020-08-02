---
title: Redis学习〖二〗Redis实战场景大全
date: 2020-3-21
tags: Redis
categories: Redis
keywords: Redis
description: Redis
---

# Redis学习〖二〗Redis实战场景大全



### Redis实战场景

- - - [Tip:Redis的数据类型模型](https://blog.csdn.net/kingtok/article/details/103446956#TipRedis_2)

- [一.String实战场景](https://blog.csdn.net/kingtok/article/details/103446956#String_6)

- - [1. 对象缓存](https://blog.csdn.net/kingtok/article/details/103446956#1__8)
  - [2. 分布式锁](https://blog.csdn.net/kingtok/article/details/103446956#2__13)
  - [3. 计数器](https://blog.csdn.net/kingtok/article/details/103446956#3__20)
  - [4.分布式系统全局序列号](https://blog.csdn.net/kingtok/article/details/103446956#4_26)

- [二.Hash实战场景](https://blog.csdn.net/kingtok/article/details/103446956#Hash_30)

- - - [Tip: Hash基本命令](https://blog.csdn.net/kingtok/article/details/103446956#Tip_Hash_31)

  - [1. 对象缓存](https://blog.csdn.net/kingtok/article/details/103446956#1__34)

  - - - [存进去的值](https://blog.csdn.net/kingtok/article/details/103446956#_41)

  - [2.电商购物车](https://blog.csdn.net/kingtok/article/details/103446956#2_43)

- [三.List结构](https://blog.csdn.net/kingtok/article/details/103446956#List_53)

- - - [Tip:List基本命令](https://blog.csdn.net/kingtok/article/details/103446956#TipList_54)

  - [1.实现数据结构](https://blog.csdn.net/kingtok/article/details/103446956#1_56)

  - [2.阻塞队列](https://blog.csdn.net/kingtok/article/details/103446956#2_61)

  - [3.消息流](https://blog.csdn.net/kingtok/article/details/103446956#3_65)

- [四.Set集合](https://blog.csdn.net/kingtok/article/details/103446956#Set_70)

- - - [Tip: Set基本命令](https://blog.csdn.net/kingtok/article/details/103446956#Tip_Set_71)

  - [1.抽奖](https://blog.csdn.net/kingtok/article/details/103446956#1_73)

  - [2.点赞收藏标签实现](https://blog.csdn.net/kingtok/article/details/103446956#2_80)

  - [3.集合操作](https://blog.csdn.net/kingtok/article/details/103446956#3_82)

  - [4.微博关注模型](https://blog.csdn.net/kingtok/article/details/103446956#4_85)

- [五.有序集合ZSet](https://blog.csdn.net/kingtok/article/details/103446956#ZSet_87)

- - - [Tip: 有序集合操作](https://blog.csdn.net/kingtok/article/details/103446956#Tip__88)

  - [1.实现微博热搜](https://blog.csdn.net/kingtok/article/details/103446956#1_90)

  - - [以上为我之前学习课程的一些总结,现在拿出来给大家分享,制作不易,转载请标注.](https://blog.csdn.net/kingtok/article/details/103446956#_93)



### Tip:Redis的数据类型模型

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191208192818550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

# 一.String实战场景

## 1. 对象缓存

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191208173248936.png)

- **使用set 存一个对象的值,不如把他拆分开来比如: KEY->user:1(id):name(字段) Value->kaikai 这样我们取出值比较方便且不用json序列化回去.**

## 2. 分布式锁

**简单的分布式锁实现**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191208173753939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**由于之前写过一点分布式锁的用法,这里简单提一下,想要了解的可以点下方链接**
[之前写过的分布式锁链接](https://blog.csdn.net/kingtok/article/details/103440435)

## 3. 计数器

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191208190511801.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- **比如文章的阅读数量等等 例如用 incr article:readcount:1002来实现文章的计数器**
- **用get article:readcount:{文章id}获取文章的阅读数**

## 4.分布式系统全局序列号

由于Redis是单线程的,如果用每增加一个序列号就**incr**一下,等到并发量一旦大起来可能就会出错,比如我们现在有三个web应用,我们可以一次分配1000个序列给它,让它慢慢来用,等到用完后继续给他1000个,这样就比一条一条给性能好得多,拿给1000个就比如计数器记了1000个**就用到了 INCRBY orderId 1000 这条命令**

# 二.Hash实战场景

### Tip: Hash基本命令

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209162409878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 1. 对象缓存

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209160528637.png)

- **对象缓存除了用String,也可以用Hash**
- **批量设置: hmset user 1:name kaikai 1:balance 1888 1:phone 111111111**
- **批量取值: hmget user 1:name 1:balance**

#### 存进去的值

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209161311671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 2.电商购物车

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209161525560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- **加入购物车实现: 以 hset cart:{用户ID} {商品ID} 数量 ,为格式设置进去**
- **购物车单个商品数量实现: hincrby cart:{用户ID} {商品ID} 数量 ,可以设置数量的增加**.
- **购物车总数量实现: hlen cart:{用户ID},获取总的数量,返回相同filed的总数量**
- **购物车商品删除实现: hdel cart:{用户ID},删除某个key的值**
- **获取到购物车中所有商品的ID: hkeys cart:{用户ID}**

# 三.List结构

### Tip:List基本命令

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209171925502.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 1.实现数据结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209172258904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- **栈,先进后出,redis实现栈只需从同一边PUSH+POP命令即可**
- **队列,先进先出,调用LPUSH+RPOP或RPUSH+LPOP可实现队列**

## 2.阻塞队列

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209173315272.png)
**客户端实现监听模式,只要有值进队,立马拿到这个值**

## 3.消息流

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209173519153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- **实现公众号发消息给关注他的人: 加入消息ID为1001,只要公众号发消息后,立马后台开启一个定时任务,一直给关注他的人的队列里面放这个消息ID,例如LPUSH msg:{用户ID} {消息ID},这样信息就直接加到用户list里面**
- **页面获取消息,当我们一打开页面肯定是一些消息按时间顺序推送给我们的,那么最新的肯定是队列开头的值,这里用到了LRANG msg:{用户ID} 0 {要取到的消息下标},比如取5条就是 0 5,**

# 四.Set集合

### Tip: Set基本命令

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209183936893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 1.抽奖

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209184301100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- **实现参与抽奖功能: SADD activity:{活动ID} {参与用户ID}**
- **获取所有的参与人, 就是查看当前集合key的集合元素有哪些: SMEMBERS activity:{活动ID}**
- **实现开奖,从集合中抽几个人中奖(不删除): SRANDMEMBER activity:{活动ID} 中奖人数**
- **实现开奖,从集合中抽几个人中奖(删除),比如要抽123等奖,一等奖就不能抽二等奖需要删除: SPOP activity:{活动ID} 中奖人数**

## 2.点赞收藏标签实现

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209190210322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 3.集合操作

**实现集合中的并,交,差集运算**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019120919145317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 4.微博关注模型

![在这里插入图片描述](https://img-blog.csdnimg.cn/201912091920393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

# 五.有序集合ZSet

### Tip: 有序集合操作

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209192939799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 1.实现微博热搜

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191209192904575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)