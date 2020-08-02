---
title: Redis学习〖一〗分布式锁实现
date: 2020-3-21
tags: Redis
categories: Redis
keywords: Redis
description: Redis
---

# Redis学习〖一〗分布式锁实现

# 分布式锁实现

## 一.什么是分布式锁？

分布式锁是控制分布式系统或不同系统之间共同访问共享资源的一种锁实现，如果不同的系统或同一个系统的不同主机之间共享了某个资源时，往往需要互斥来防止彼此干扰来保证一致性。

## 二.分布式锁需要具备哪些条件

- **互斥性：在任意一个时刻，只有一个客户端持有锁。**
- **无死锁：即便持有锁的客户端崩溃或者其他意外事件，锁仍然可以被获取。**
- **容错：只要大部分Redis节点都活着，客户端就可以获取和释放锁**

## 三.使用Redis实现分布式锁

```
依赖于 Redis（setnx命令）
1
```

**我们直接来用Redis命令实现一个最简单的分布式锁(单机)**

- 多并发下确保只有一个进程拿到锁
- 保证不会误删锁
- 保证不会出现死锁

### 下面来简单写个Demo实现下功能

**写了个库存删减功能,类似于秒杀活动,用户进来加锁,库存减完就返回库存不足,代码注解很详细就不一一解释了**

```java
  @GetMapping("/getLock")
    public String lock(String shoesName){
        String lockName="product_lock:"+shoesName;
        String uuid = UUID.randomUUID().toString();//确保只有自己才可以释放锁,已用redisson解决可不写
        RLock redissonLock = redisson.getLock(lockName);
        try{
            //Jedis的setnx(K,V),加过期时间防止程序挂掉锁不释放,setnx与expire写成一条命令,由于redis的原子性要成功一起成功,要失败一起失败
            Boolean aBoolean = redisTemplate.opsForValue().setIfAbsent(lockName, uuid,1, TimeUnit.MINUTES);
            if(!aBoolean){//被锁住了,被一个用户拿到
                return "error";
            }
            redissonLock.lock(30,TimeUnit.MINUTES);//给锁续命
            //从redis得到鞋子的数量
            Integer showNumber=Integer.parseInt((String) redisTemplate.opsForValue().get(shoesName));//取到鞋子的数量
            if(showNumber>0){
                redisTemplate.opsForValue().set(shoesName,String.valueOf(showNumber-1));
                return "鞋子购买成功,剩余数量为:"+String.valueOf(showNumber-1);
            }else {
                return "库存不足!";
            }
        }finally {//解决死锁问题  防止程序出异常而死锁
            //释放锁
            redissonLock.unlock();
//            //判断是否为自己加的锁,
//            if(uuid.equals(redisTemplate.opsForValue().get(lockName))){
//                //释放锁
//                redisTemplate.delete(lockName);
//            }
        }
    }
123456789101112131415161718192021222324252627282930
```

- **这里给锁续命的意思是,这个锁必须等待你这个程序完成后才能释放掉,但是怎么知道这个程序能跑多长时间呢?,redisson用到了类似于当你运行你的程序时,开另外一个线程去续命,当你程序还没完成时,时间到了,那就继续给锁加上一个初始时间,直到程序跑完**