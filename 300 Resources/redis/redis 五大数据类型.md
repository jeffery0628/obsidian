---
Create: 2021年 十二月 12日, 星期日 15:49
tags: 
  - Engineering/redis
  - 大数据
---

[官方文档](http://redisdoc.com/)

## string

string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。
string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。string类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M。

### 操作：
1. set : set k1 v1
2. get: get k1
3. del : del k1
4. append : append k1 xx --> k1:v1xx
5. strlen: strlen k1 --> 4

### 数值加减操作
set v1 1:
1. incr ： incr k1 --> 2
2. decr:   decr k1 --> 1
3. incrby : incrby k1 2 --> 3
4. decrby: decrby k1 2 -- > 1

### 字符串截取替换操作
set k4 "hello word"
1. getrange: getrange k4 0 5 --> "hello "
2. setrange: setrange k4 6 'redis'  --> "hello redis"

### 设置键的超时时间
1. setex(set with expire): setex k5 3 k5  设置k5 3秒后过期
2. setnx(set if not exist): setnx k5 555,setnx k5 6666, get k5 --> "555"

### 多值的设置
1. mset:同时设置一个或多个 key-value 对。
2. mget:获取所有(一个或多个)给定 key 的值。
3. msetnx:同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。

### 修改value
getset:将给定 key 的值设为 value ，并返回 key 的旧值(old value)。简单一句话，先get然后立即set

getset k5 666 --> "555"
get k5 --> "666"


## hash
KV模式不变，但V是一个键值对。
Hash（哈希）Redis hash 是一个键值对集合。Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。类似Java里面的Map

1. hset
2. hget
3. hmset
4. hmget
5. hgetall
6. hdel
7.  hlen
8.   hexists ：key 在key里面的某个值的key
9.    hkeys
10.  hvals
11.   hincrby
12.   hincrbyfloat
13.   hsetnx：不存在赋值，存在了无效。


## list
单值多value

List（列表）Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。它的底层实际是个链表
1. lpush
2. rpush
3. lrange
4. lpop
5. rpop
6. lindex，按照索引下标获得元素(从上到下)
7. llen
8. lrem key 删N个value
9. ltrim key start_index end_index，截取指定范围的值后再赋值给key
10. rpoplpush 源列表 目的列表:移除列表的最后一个元素，并将该元素添加到另一个列表并返回
11. lset key index value
12. linsert key  before/after 值1 值2:在list某个已有值的前后再添加具体值

总结：它是一个字符串链表，left、right都可以插入添加；如果键不存在，创建新的链表；如果键已存在，新增内容；如果值全移除，对应的键也就消失了。链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。


## set
单值多value
Set（集合）Redis的Set是string类型的无序集合。它是通过HashTable实现实现的。
1.  sadd
2.  smembers
3.  sismember
4. scard，获取集合里面的元素个数
5. srem key value 删除集合中元素
6. srandmember key 某个整数(随机出几个数)
7. spop key 随机出栈
8.  smove key1 key2 在key1里某个值      作用是将key1里的某个值赋给key2
9.   数学集合类
	1.   差集：sdiff
	2.   交集：sinter
	3.   并集：sunion


## zset
zset(sorted set：有序集合)Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

在set基础上，加一个score值。之前set是k1 v1 v2 v3，现在zset是k1 score1 v1 score2 v2
1. zadd/zrange withscores
2. zrangebyscore key start_score end_score 
3. zrem key 某score下对应的value值，作用是删除元素
4. zcard/zcount key score区间/zrank key values值，作用是获得下标值/zscore key 对应值,获得分数
5. zrevrank key values值，作用是逆序获得下标值
6. zrevrange
7. zrevrangebyscore  key start_score end_score


