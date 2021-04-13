> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/hebtu666/article/details/114827837)

> 我摊牌了，这篇文章，值得 99% 的人收藏

**此文后续会改为粉丝可见，所以喜欢的请提前关注和收藏，不迷路。**

**最近有五本我喜欢的 redis 实体新书，想要的去评论，我写个随机数抽奖包邮送给你。**

![](https://img-blog.csdnimg.cn/20210315163834206.png)

那么，准备好了吗？我们开始吧。

> 《三天给你聊清楚 redis》第 1 天先唠唠 redis 是个啥（18629 字）

一、入门
====

Redis 是一款基于键值对的 NoSQL 数据库，它的值支持多种数据结构：  
字符串 (strings)、哈希(hashes)、列表(lists)、集合(sets)、有序集合(sorted sets) 等。  
• Redis 将所有的数据都存放在内存中，所以它的读写性能十分惊人，用作数据库，缓存和消息代理。

Redis 具有内置的复制，Lua 脚本，LRU 逐出，事务和不同级别的磁盘持久性，并通过 Redis Sentinel 和 Redis Cluster 自动分区提供了高可用性。  
• Redis 典型的应用场景包括：缓存、排行榜、计数器、社交网络、消息队列等

1.1NoSql 入门概述
-------------

1）单机 Mysql 的美好时代

瓶颈：

*   数据库总大小一台机器硬盘内存放不下
*   数据的索引（B + tree）一个机器的运行内存放不下
*   访问量（读写混合）一个实例不能承受

![](https://img-blog.csdnimg.cn/20191012200654387.png)

2）Memcached（缓存）+ MySql + 垂直拆分

通过缓存来缓解数据库的压力，优化数据库的结构和索引

垂直拆分指的是：分成多个数据库存储数据（如：卖家库与买家库）

![](https://img-blog.csdnimg.cn/20191012200703545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

3）MySql 主从复制读写分离

1.  主从复制：主库来一条数据，从库立刻插入一条。
2.  读写分离：读取（从库 Master），写（主库 Slave）

![](https://img-blog.csdnimg.cn/20191012200717471.png)

4）分表分库 + 水平拆分 + MySql 集群

1.  主库的写压力出现瓶颈（行锁 InnoDB 取代表锁 MyISAM）
2.  分库：根据业务相关紧耦合在同一个库，对不同的数据读写进行分库（如注册信息等不常改动的冷库与购物信息等热门库分开）
3.  分表：切割表数据（例如 90W 条数据，id 1-30W 的放在 A 库，30W-60W 的放在 B 库，60W-90W 的放在 C 库）

![](https://img-blog.csdnimg.cn/201910122007321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

MySql 扩展的瓶颈

1.  大数据下 IO 压力大
2.  表结构更改困难

常用的 Nosql

Redis  
memcache  
Mongdb  
以上几种 Nosql 请到各自的官网上下载并参考使用

Nosql 的核心功能点

KV(存储)  
Cache(缓存)  
Persistence(持久化)  
……

1.2redis 的介绍和特点:
----------------

       问题：  
               传统数据库：持久化存储数据。  
               solr 索引库: 大量的数据的检索。  
               在实际开发中，高并发环境下，不同的用户会需要相同的数据。因为每次请求，  
               在后台我们都会创建一个线程来处理，这样造成，同样的数据从数据库中查询了 N 次。  
               而数据库的查询本身是ＩＯ操作，效率低，频率高也不好。  
               总而言之，一个网站总归是有大量的数据是用户共享的，但是如果每个用户都去数据库查询  
               效率就太低了。  
       解决：  
               将用户共享数据缓存到服务器的内存中。          
       特点：  
               1、基于键值对  
               2、非关系型 (redis)  
                       关系型数据库: 存储了数据以及数据之间的关系, oracle,mysql  
                       非关系型数据库: 存储了数据, redis,mdb.  
               3、数据存储在内存中，服务器关闭后，持久化到硬盘中  
               4、支持主从同步  
               实现了缓存数据和项目的解耦。  
       redis 存储的数据特点：  
               大量数据  
               用户共享数据  
               数据不经常修改。  
               查询数据  
       redis 的应用场景：  
               网站高并发的主页数据  
               网站数据的排名  
               消息订阅

  
1.3redis——数据结构和对象的使用介绍      
       
---------------------------------------

[redis 官网](https://redis.io/)

[微软写的 windows 下的 redis](https://github.com/microsoftarchive/redis)

![](https://img-blog.csdnimg.cn/20191016202558480.png)

我们下载第一个

额案后基本一路默认就行了

安装后，服务自动启动，以后也不用自动启动。

![](https://img-blog.csdnimg.cn/20191016202822741.png)

出现这个表示我们连接上了。

[redis 命令参考链接](http://redisdoc.com/string/index.html)

### **1.3.1String**

**数据结构**

```
struct sdshdr{
    //记录buf数组中已使用字节的数量
    int len;
    //记录buf数组中未使用的数量
    int free;
    //字节数组，用于保存字符串
    char buf[];
}
```

**常见操作**

```
127.0.0.1:6379> set hello world
OK
127.0.0.1:6379> get hello
"world"
127.0.0.1:6379> del hello
(integer) 1
127.0.0.1:6379> get hello
(nil)
127.0.0.1:6379>
```

**应用场景**

String 是最常用的一种数据类型，普通的 key/value 存储都可以归为此类，value 其实不仅是 String，也可以是数字：比如想知道什么时候封锁一个 IP 地址（访问超过几次）。INCRBY 命令让这些变得很容易，通过原子递增保持计数。

**1.3.2LIST**
-------------

**数据结构**

```
typedef struct listNode{
    //前置节点
    struct listNode *prev;
    //后置节点
    struct listNode *next;
    //节点的值
    struct value;
}
```

**常见操作**

```
> lpush list-key item
(integer) 1
> lpush list-key item2
(integer) 2
> rpush list-key item3
(integer) 3
> rpush list-key item
(integer) 4
> lrange list-key 0 -1
1) "item2"
2) "item"
3) "item3"
4) "item"
> lindex list-key 2
"item3"
> lpop list-key
"item2"
> lrange list-key 0 -1
1) "item"
2) "item3"
3) "item"
```

**应用场景**

Redis list 的应用场景非常多，也是 Redis 最重要的数据结构之一。  
我们可以轻松地实现最新消息排行等功能。  
Lists 的另一个应用就是消息队列，可以利用 Lists 的 PUSH 操作，将任务存在 Lists 中，然后工作线程再用 POP 操作将任务取出进行执行。

**1.3.3HASH**
-------------

**数据结构**

dictht 是一个散列表结构，使用拉链法保存哈希冲突的 dictEntry。

```
typedef struct dictht{
    //哈希表数组
    dictEntry **table;
    //哈希表大小
    unsigned long size;
    //哈希表大小掩码，用于计算索引值
    unsigned long sizemask;
    //该哈希表已有节点的数量
    unsigned long used;
}
 
typedef struct dictEntry{
    //键
    void *key;
    //值
    union{
        void *val;
        uint64_tu64;
        int64_ts64;
    }
    struct dictEntry *next;
}
```

Redis 的字典 dict 中包含两个哈希表 dictht，这是为了方便进行 rehash 操作。在扩容时，将其中一个 dictht 上的键值对 rehash 到另一个 dictht 上面，完成之后释放空间并交换两个 dictht 的角色。

```
typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    unsigned long iterators; /* number of iterators currently running */
} dict;
```

rehash 操作并不是一次性完成、而是采用渐进式方式，目的是为了避免一次性执行过多的 rehash 操作给服务器带来负担。

渐进式 rehash 通过记录 dict 的 rehashidx 完成，它从 0 开始，然后没执行一次 rehash 例如在一次 rehash 中，要把 dict[0] rehash 到 dict[1]，这一次会把 dict[0] 上 table[rehashidx] 的键值对 rehash 到 dict[1] 上，dict[0] 的 table[rehashidx] 指向 null，并令 rehashidx++。

在 rehash 期间，每次对字典执行添加、删除、查找或者更新操作时，都会执行一次渐进式 rehash。

采用渐进式 rehash 会导致字典中的数据分散在两个 dictht 中，因此对字典的操作也会在两个哈希表上进行。  
例如查找时，先从 ht[0] 查找，没有再查找 ht[1]，添加时直接添加到 ht[1] 中。

**常见操作**

```
> hset hash-key sub-key1 value1
(integer) 1
> hset hash-key sub-key2 value2
(integer) 1
> hset hash-key sub-key1 value1
(integer) 0
> hgetall hash-key
1) "sub-key1"
2) "value1"
3) "sub-key2"
4) "value2"
> hdel hash-key sub-key2
(integer) 1
> hdel hash-key sub-key2
(integer) 0
> hget hash-key sub-key1
"value1"
> hgetall hash-key
1) "sub-key1"
2) "value1"
```

**1.3.4SET**
------------

**常见操作**

```
> sadd set-key item
(integer) 1
> sadd set-key item2
(integer) 1
> sadd set-key item3
(integer) 1
> sadd set-key item
(integer) 0
> smembers set-key
1) "item2"
2) "item"
3) "item3"
> sismember set-key item4
(integer) 0
> sismember set-key item
(integer) 1
> srem set-key item
(integer) 1
> srem set-key item
(integer) 0
> smembers set-key
1) "item2"
2) "item3"
```

**应用场景**

Redis 为集合提供了求交集、并集、差集等操作，故可以用来求共同好友等操作。

**1.3.5ZSET**
-------------

**数据结构**

```
typedef struct zskiplistNode{
        //后退指针
        struct zskiplistNode *backward;
        //分值
        double score;
        //成员对象
        robj *obj;
        //层
        struct zskiplistLever{
            //前进指针
            struct zskiplistNode *forward;
            //跨度
            unsigned int span;
        }lever[];
    }
    
    typedef struct zskiplist{
        //表头节点跟表尾结点
        struct zskiplistNode *header, *tail;
        //表中节点的数量
        unsigned long length;
        //表中层数最大的节点的层数
        int lever;
    }
```

跳跃表，基于多指针有序链实现，可以看作多个有序链表。

与红黑树等平衡树相比，跳跃表具有以下优点：

*   插入速度非常快速，因为不需要进行旋转等操作来维持平衡性。
*   更容易实现。
*   支持无锁操作。

**常见操作**

```
> zadd zset-key 728 member1
(integer) 1
> zadd zset-key 982 member0
(integer) 1
> zadd zset-key 982 member0
(integer) 0
> zrange zset-key 0 -1
1) "member1"
2) "member0"
> zrange zset-key 0 -1 withscores
1) "member1"
2) "728"
3) "member0"
4) "982"
> zrangebyscore zset-key 0 800 withscores
1) "member1"
2) "728"
> zrem zset-key member1
(integer) 1
> zrem zset-key member1
(integer) 0
> zrange zset-key 0 -1 withscores
1) "member0"
2) "982"
```

**应用场景**

以某个条件为权重，比如按顶的次数排序  
ZREVRANGE 命令可以用来按照得分来获取前 100 名的用户，ZRANK 可以用来获取用户排名，非常直接而且操作容易。  
Redis sorted set 的使用场景与 set 类似，区别是 set 不是自动有序的，而 sorted set 可以通过用户额外提供一个优先级 (score) 的参数来为成员排序，并且是插入有序的，即自动排序。

[redis 命令参考链接](http://redisdoc.com/string/index.html)

1.4Spring 整合 Redis
------------------

**引入依赖**  
- spring-boot-starter-data-redis

```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>
```

  
**配置 Redis**  
- 配置数据库参数

```
# RedisProperties
spring.redis.database=11#第11个库，这个随便
spring.redis.host=localhost
spring.redis.port=6379#端口
```

  
- 编写配置类，构造 RedisTemplate

这个 springboot 已经帮我们配了，但是默认 object，我想改成 string

```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.RedisSerializer;
 
@Configuration
public class RedisConfig {
 
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
 
        // 设置key的序列化方式
        template.setKeySerializer(RedisSerializer.string());
        // 设置value的序列化方式
        template.setValueSerializer(RedisSerializer.json());
        // 设置hash的key的序列化方式
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置hash的value的序列化方式
        template.setHashValueSerializer(RedisSerializer.json());
 
        template.afterPropertiesSet();
        return template;
    }
 
}
```

  
**访问 Redis**  
- redisTemplate.opsForValue()  
- redisTemplate.opsForHash()  
- redisTemplate.opsForList()  
- redisTemplate.opsForSet()  
- redisTemplate.opsForZSet()  
![](https://img-blog.csdnimg.cn/20191016204545870.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

```
@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class RedisTests {
 
    @Autowired
    private RedisTemplate redisTemplate;
 
    @Test
    public void testStrings() {
        String redisKey = "test:count";
 
        redisTemplate.opsForValue().set(redisKey, 1);
 
        System.out.println(redisTemplate.opsForValue().get(redisKey));
        System.out.println(redisTemplate.opsForValue().increment(redisKey));
        System.out.println(redisTemplate.opsForValue().decrement(redisKey));
    }
 
    @Test
    public void testHashes() {
        String redisKey = "test:user";
 
        redisTemplate.opsForHash().put(redisKey, "id", 1);
        redisTemplate.opsForHash().put(redisKey, "username", "zhangsan");
 
        System.out.println(redisTemplate.opsForHash().get(redisKey, "id"));
        System.out.println(redisTemplate.opsForHash().get(redisKey, "username"));
    }
 
    @Test
    public void testLists() {
        String redisKey = "test:ids";
 
        redisTemplate.opsForList().leftPush(redisKey, 101);
        redisTemplate.opsForList().leftPush(redisKey, 102);
        redisTemplate.opsForList().leftPush(redisKey, 103);
 
        System.out.println(redisTemplate.opsForList().size(redisKey));
        System.out.println(redisTemplate.opsForList().index(redisKey, 0));
        System.out.println(redisTemplate.opsForList().range(redisKey, 0, 2));
 
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
    }
 
    @Test
    public void testSets() {
        String redisKey = "test:teachers";
 
        redisTemplate.opsForSet().add(redisKey, "刘备", "关羽", "张飞", "赵云", "诸葛亮");
 
        System.out.println(redisTemplate.opsForSet().size(redisKey));
        System.out.println(redisTemplate.opsForSet().pop(redisKey));
        System.out.println(redisTemplate.opsForSet().members(redisKey));
    }
 
    @Test
    public void testSortedSets() {
        String redisKey = "test:students";
 
        redisTemplate.opsForZSet().add(redisKey, "唐僧", 80);
        redisTemplate.opsForZSet().add(redisKey, "悟空", 90);
        redisTemplate.opsForZSet().add(redisKey, "八戒", 50);
        redisTemplate.opsForZSet().add(redisKey, "沙僧", 70);
        redisTemplate.opsForZSet().add(redisKey, "白龙马", 60);
 
        System.out.println(redisTemplate.opsForZSet().zCard(redisKey));
        System.out.println(redisTemplate.opsForZSet().score(redisKey, "八戒"));
        System.out.println(redisTemplate.opsForZSet().reverseRank(redisKey, "八戒"));
        System.out.println(redisTemplate.opsForZSet().reverseRange(redisKey, 0, 2));
    }
 
    @Test
    public void testKeys() {
        redisTemplate.delete("test:user");
 
        System.out.println(redisTemplate.hasKey("test:user"));
 
        redisTemplate.expire("test:students", 10, TimeUnit.SECONDS);
    }
}
```

这样还是稍微有点麻烦，我们其实可以绑定 key

```
// 多次访问同一个key
    @Test
    public void testBoundOperations() {
        String redisKey = "test:count";
        BoundValueOperations operations = redisTemplate.boundValueOps(redisKey);
        operations.increment();
        operations.increment();
        operations.increment();
        operations.increment();
        operations.increment();
        System.out.println(operations.get());
    }
```

二、数据结构原理总结
==========

这部分在我看来是最有意思的，我们有必要了解底层数据结构的实现，这也是我最感兴趣的。

比如，你知道 redis 中的字符串怎么实现的吗？为什么这么实现？

你知道 redis 压缩列表是什么算法吗？

你知道 redis 为什么抛弃了红黑树反而采用了跳表这种新的数据结构吗？

你知道 hyperloglog 为什么用如此小的空间就可以有这么好的统计性能和准确性吗？

你知道布隆过滤器为什么这么有效吗？有没有数学证明过？

你是否还能很快写出来快排？或者不断优化性能的排序？是不是只会调库了甚至库函数怎么实现的都不知道？真的就是快排？

包括数据库，持久化，处理事件、客户端服务端、事务的实现、发布和订阅等功能的实现，也需要了解。

  
2.1 数据结构和对象的实现
-----------------

*   **1） 字符串**

redis 并未使用传统的 c 语言字符串表示，它自己构建了一种简单的动态字符串抽象类型。

在 redis 里，c 语言字符串只会作为字符串字面量出现，用在无需修改的地方。

当需要一个可以被修改的字符串时，redis 就会使用自己实现的 SDS（simple dynamic string）。比如在 redis 数据库里，包含字符串的键值对底层都是 SDS 实现的，不止如此，SDS 还被用作缓冲区（buffer）：比如 AOF 模块中的 AOF 缓冲区以及客户端状态中的输入缓冲区。

下面来具体看一下 sds 的实现：

```
struct sdshdr
{
    int len;//buf已使用字节数量（保存的字符串长度）
    int free;//未使用的字节数量
    char buf[];//用来保存字符串的字节数组
};
```

sds 遵循 c 中字符串以'\0'结尾的惯例，这一字节的空间不算在 len 之内。

这样的好处是，我们可以直接重用 c 中的一部分函数。比如 printf；

 **sds 相对 c 的改进**

    获取长度：c 字符串并不记录自身长度，所以获取长度只能遍历一遍字符串，redis 直接读取 len 即可。

    缓冲区安全：c 字符串容易造成缓冲区溢出，比如：程序员没有分配足够的空间就执行拼接操作。而 redis 会先检查 sds 的空间是否满足所需要求，如果不满足会自动扩充。

    内存分配：由于 c 不记录字符串长度，对于包含了 n 个字符的字符串，底层总是一个长度 n+1 的数组，每一次长度变化，总是要对这个数组进行一次内存重新分配的操作。因为内存分配涉及复杂算法并且可能需要执行系统调用，所以它通常是比较耗时的操作。   

 **redis 内存分配：**

1、空间预分配：如果修改后大小小于 1MB，程序分配和 len 大小一样的未使用空间，如果修改后大于 1MB，程序分配  1MB 的未使用空间。修改长度时检查，够的话就直接使用未使用空间，不用再分配。 

2、惰性空间释放：字符串缩短时不需要释放空间，用 free 记录即可，留作以后使用。

 **二进制安全**

c 字符串除了末尾外，不能包含空字符，否则程序读到空字符会误以为是结尾，这就限制了 c 字符串只能保存文本，二进制文件就不能保存了。

而 redis 字符串都是二进制安全的，因为有 len 来记录长度。

*   **2） 链表**

作为一种常用数据结构，链表内置在很多高级语言中，因为 c 并没有，所以 redis 实现了自己的链表。

链表在 redis 也有一定的应用，比如列表键的底层实现之一就是链表。（当列表键包含大量元素或者元素都是很长的字符串时）

发布与订阅、慢查询、监视器等功能也用到了链表。

具体实现：

```
//redis的节点使用了双向链表结构
typedef struct listNode {
    // 前置节点
    struct listNode *prev;
    // 后置节点
    struct listNode *next;
    // 节点的值
    void *value;
} listNode;
```

```
//其实学过数据结构的应该都实现过
typedef struct list {
    // 表头节点
    listNode *head;
    // 表尾节点
    listNode *tail;
    // 链表所包含的节点数量
    unsigned long len;
    // 节点值复制函数
    void *(*dup)(void *ptr);
    // 节点值释放函数
    void (*free)(void *ptr);
    // 节点值对比函数
    int (*match)(void *ptr, void *key);
} list;
```

总结一下 redis 链表特性：

双端、无环、带长度记录、

多态：使用 `void*` 指针来保存节点值， 可以通过 `dup` 、 `free` 、 `match` 为节点值设置类型特定函数， 可以保存不同类型的值。

*   **3）字典**

其实字典这种数据结构也内置在很多高级语言中，但是 c 语言没有，所以 redis 自己实现了。

应用也比较广泛，比如 redis 的数据库就是字典实现的。不仅如此，当一个哈希键包含的键值对比较多，或者都是很长的字符串，redis 就会用字典作为哈希键的底层实现。

来看看具体是实现：

```
//redis的字典使用哈希表作为底层实现
typedef struct dictht {
    // 哈希表数组
    dictEntry **table;
    // 哈希表大小
    unsigned long size;
    // 哈希表大小掩码，用于计算索引值
    // 总是等于 size - 1
    unsigned long sizemask;
 
    // 该哈希表已有节点的数量
    unsigned long used;
 
} dictht;
```

`table` 是一个数组， 数组中的每个元素都是一个指向`dictEntry` 结构的指针， **每个 `dictEntry` 结构保存着一个键值对**。

![](https://img-blog.csdnimg.cn/20190416175328381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

图为一个大小为 4 的空哈希表。

我们接着就来看`dictEntry的实现：`

```
typedef struct dictEntry {
    // 键
    void *key;
    // 值
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
    } v;
 
    // 指向下个哈希表节点，形成链表
    struct dictEntry *next;
} dictEntry;
```

（v 可以是一个指针， 或者是一个 `uint64_t` 整数， 又或者是一个 `int64_t` 整数。）

next 就是解决键冲突问题的，冲突了就挂后面，这个学过数据结构的应该都知道吧，不说了。

下面我们来说字典是怎么实现的了。

```
typedef struct dict {
    // 类型特定函数
    dictType *type;
    // 私有数据
    void *privdata;
    // 哈希表
    dictht ht[2];
    // rehash 索引
    int rehashidx; //* rehashing not in progress if rehashidx == -1 
} dict;
```

`type` 和 `privdata` 是对不同类型的键值对， 为创建多态字典而设置的：

`type` 指向 `dictType` ， 每个 `dictType` 保存了用于操作特定类型键值对的函数， 可以为用途不同的字典设置不同的类型特定函数。

而 `privdata` 属性则保存了需要传给那些类型特定函数的可选参数。

```
typedef struct intset {
    // 编码方式
    uint32_t encoding;
    // 集合包含的元素数量
    uint32_t length;
    // 保存元素的数组
    int8_t contents[];
} intset;
```

 **rehash（重新散列）**

随着我们不断的操作，哈希表保存的键值可能会增多或者减少，为了让哈希表的负载因子维持在合理的范围内，有时需要对哈希表进行合理的扩展或者收缩。 一般情况下， 字典只使用 `ht[0]` 哈希表， `ht[1]` 哈希表只会在对 `ht[0]` 哈希表进行 rehash 时使用。

redis 字典哈希 rehash 的步骤如下：

1）为 ht[1] 分配合理空间：如果是扩展操作，大小为第一个大于等于 ht[0]*used*2 的，2 的 n 次幂。

                                           如果是收缩操作，大小为第一个大于等于 ht[0]*used 的，2 的 n 次幂。

2）将 ht[0] 中的数据 rehash 到 ht[1] 上。

3）释放 ht[0]，将 ht[1] 设置为 ht[0]，ht[1] 创建空表，为下次做准备。

 **渐进 rehash**

数据量特别大时，rehash 可能对服务器造成影响。为了避免，服务器不是一次性 rehash 的，而是分多次。

我们维持一个变量 rehashidx，设置为 0，代表 rehash 开始，然后开始 rehash，在这期间，每个对字典的操作，程序都会把索引 rehashidx 上的数据移动到 ht[1]。

随着操作不断执行，最终我们会完成 rehash，设置 rehashidx 为 - 1.

需要注意：rehash 过程中，每一次增删改查也是在两个表进行的。

*   **4）整数集合**

整数集合（intset）是 Redis 用于保存整数值的集合抽象数据结构， 可以保存 `int16_t` 、 `int32_t` 、 `int64_t` 的整数值， 并且保证集合中不会出现重复元素。

实现较为简单：

```
randomLevel()
level := 1
// random()返回一个[0...1)的随机数
while random() < p and level < MaxLevel do
level := level + 1
return level
```

各个项在数组中从小到大有序地排列， 并且数组中不包含任何重复项。

虽然 `intset` 结构将 `contents` 属性声明为 `int8_t` 类型的数组， 但实际上 `contents` 数组并不保存任何 `int8_t` 类型的值 —— `contents` 数组的真正类型取决于 `encoding` 属性的值：

如果 `encoding` 属性的值为 `INTSET_ENC_INT16` ， 那么 `contents` 就是一个 `int16_t` 类型的数组， 数组里的每个项都是一个 `int16_t` 类型的整数值 （最小值为 `-32,768` ，最大值为 `32,767` ）。

如果 `encoding` 属性的值为 `INTSET_ENC_INT32` ， 那么 `contents` 就是一个 `int32_t` 类型的数组， 数组里的每个项都是一个 `int32_t` 类型的整数值 （最小值为 `-2,147,483,648` ，最大值为 `2,147,483,647` ）。

如果 `encoding` 属性的值为 `INTSET_ENC_INT64` ， 那么 `contents` 就是一个 `int64_t` 类型的数组， 数组里的每个项都是一个 `int64_t` 类型的整数值 （最小值为 `-9,223,372,036,854,775,808` ，最大值为 `9,223,372,036,854,775,807` ）。

 **升级**

c 语言是静态类型语言，不允许不同类型保存在一个数组。这样第一，灵活性较差，第二，有时会用掉不必要的内存

比如用 long long 储存 1

为了提高整数集合的灵活性和节约内存，我们引入升级策略。

当我们要将一个新元素添加到集合里， 并且新元素类型比集合现有元素的类型都要长时， 集合需要先进行升级。

分为三步进行：

1.  根据新元素的类型， 扩展整数集合底层数组的空间大小， 并为新元素分配空间。
2.  将底层数组现有的所有元素都转换成与新元素相同的类型， 并将类型转换后的元素放置到正确的位上
3.  将新元素添加到底层数组里面。

因为每次添加新元素都可能会引起升级， 每次升级都要对已有元素类型转换， 所以添加新元素的时间复杂度为 O(N) 。

因为引发升级的新元素比原数据都长，所以要么他是最大的，要么他是最小的。我们把它放在开头或结尾即可。

 **降级**

略略略，不管你们信不信，整数集合不支持降级操作。。我也不知道为啥

*   **5）压缩列表**

压缩列表是列表键和哈希键的底层实现之一。

当一个列表键只包含少量列表项，并且列表项都是小整数或者短字符串，redis 就会用压缩列表做列表键底层实现。

压缩列表是 Redis 为了节约内存而开发的， 由一系列特殊编码的连续内存块组成的顺序型（sequential）数据结构。

一个压缩列表可以包含任意多个节点（entry）， 每个节点可以保存一个字节数组或者一个整数值。

具体实现：

![](https://img-blog.csdnimg.cn/20190417154409102.png)

![](https://img-blog.csdnimg.cn/20190417154422149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

具体说一下 entry：

由三个部分组成：

1、previous_entry_length: 记录上一个节点的长度，这样我们就可以从最后一路遍历到开头。

2、encoding：记录了 content 所保存的数据类型和长度。（具体编码不写了，不重要）

3、content：保存节点值，可以是字节数组或整数。（具体怎么压缩的等我搞明白再补）

 **连锁更新**

前面说过， 每个节点的 `previous_entry_length` 属性都记录了前一个节点的长度：

*   如果前一节点的长度 < `254` KB， 那么 `previous_entry_length` 需要用 `1` 字节长的空间
*   如果前一节点的长度 >=`254` KB， 那么 `previous_entry_length` 需要用 `5` 字节长的空间

现在， 考虑这样一种情况： 在一个压缩列表中， 有多个连续的、长度介于 `250` 字节到 `253` 字节之间的节点 ，这时， 如果我们将一个长度大于等于 `254` 字节的新节点 `new` 设置为压缩列表的表头节点。。。。

然后脑补一下，就会导致连锁扩大每个节点的空间对吧？e(i) 因为 e(i-1) 的扩大而扩大，i+1 也是如此，以此类推。。。

删除节点同样会导致连锁更新。

这个事情只是想说明一个问题：插入删除操作的最坏时间复杂度其实是 o(n*n)，因为每更新一个节点都要 o(n)。

但是，也不用太过担心，因为这种特殊情况并不多见，这些命令的平均复杂度依旧是 o(n)。

2.2 跳表专栏
--------

### 2.2.1 跳表是啥

为什么选择了跳表而不是红黑树？

[跳表是个啥东西](https://blog.csdn.net/hebtu666/article/details/89370679)请看这个文章。

我们知道，节点插入时随机出一个层数，仅仅依靠一个简单的随机数操作而构建出来的多层链表结构，能保证它有一个良好的查找性能吗？为了回答这个疑问，我们需要分析 skiplist 的统计性能。

在分析之前，我们还需要着重指出的是，执行插入操作时计算随机数的过程，是一个很关键的过程，它对 skiplist 的统计特性有着很重要的影响。这并不是一个普通的服从均匀分布的随机数，它的计算过程如下：

*   首先，每个节点肯定都有第 1 层指针（每个节点都在第 1 层链表里）。
*   如果一个节点有第 i 层 (i>=1) 指针（即节点已经在第 1 层到第 i 层链表中），那么它有第 (i+1) 层指针的概率为 p。
*   节点最大的层数不允许超过一个最大值，记为 MaxLevel。

这个计算随机层数的伪码如下所示：

```
p = 1/4
MaxLevel = 32
```

randomLevel() 的伪码中包含两个参数，一个是 p，一个是 MaxLevel。在 Redis 的 skiplist 实现中，这两个参数的取值为：

```
C(0)=0
C(k)=(1-p)×(上图中情况b的查找长度) + p×(上图中情况c的查找长度)
```

### 2.2.2skiplist 的算法性能分析

在这一部分，我们来简单分析一下 skiplist 的时间复杂度和空间复杂度，以便对于 skiplist 的性能有一个直观的了解。如果你不是特别偏执于算法的性能分析，那么可以暂时跳过这一小节的内容。

我们先来计算一下每个节点所包含的平均指针数目（概率期望）。节点包含的指针数目，相当于这个算法在空间上的额外开销 (overhead)，可以用来度量空间复杂度。

根据前面 randomLevel() 的伪码，我们很容易看出，产生越高的节点层数，概率越低。定量的分析如下：

*   节点层数至少为 1。而大于 1 的节点层数，满足一个概率分布。
*   节点层数恰好等于 1 的概率为 1-p。
*   节点层数大于等于 2 的概率为 p，而节点层数恰好等于 2 的概率为 p(1-p)。
*   节点层数大于等于 3 的概率为 p^2，而节点层数恰好等于 3 的概率为 p^2(1-p)。
*   节点层数大于等于 4 的概率为 p^3，而节点层数恰好等于 4 的概率为 p^3(1-p)。
*   ......

因此，一个节点的平均层数（也即包含的平均指针数目），计算如下：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi00NWUwNTIwZGJhMjk3MDE3NjlkYTZkZjU0OTgyZGI1N19oZC5qcGc?x-oss-process=image/format,png)

现在很容易计算出：

*   当 p=1/2 时，每个节点所包含的平均指针数目为 2；
*   当 p=1/4 时，每个节点所包含的平均指针数目为 1.33。这也是 Redis 里的 skiplist 实现在空间上的开销。

接下来，为了分析时间复杂度，我们计算一下 skiplist 的平均查找长度。查找长度指的是查找路径上跨越的跳数，而查找过程中的比较次数就等于查找长度加 1。以前面图中标出的查找 23 的查找路径为例，从左上角的头结点开始，一直到结点 22，查找长度为 6。

为了计算查找长度，这里我们需要利用一点小技巧。我们注意到，每个节点插入的时候，它的层数是由随机函数 randomLevel() 计算出来的，而且随机的计算不依赖于其它节点，每次插入过程都是完全独立的。所以，从统计上来说，一个 skiplist 结构的形成与节点的插入顺序无关。

这样的话，为了计算查找长度，我们可以将查找过程倒过来看，从右下方第 1 层上最后到达的那个节点开始，沿着查找路径向左向上回溯，类似于爬楼梯的过程。我们假设当回溯到某个节点的时候，它才被插入，这虽然相当于改变了节点的插入顺序，但从统计上不影响整个 skiplist 的形成结构。

现在假设我们从一个层数为 i 的节点 x 出发，需要向左向上攀爬 k 层。这时我们有两种可能：

*   如果节点 x 有第 (i+1) 层指针，那么我们需要向上走。这种情况概率为 p。
*   如果节点 x 没有第 (i+1) 层指针，那么我们需要向左走。这种情况概率为(1-p)。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS92Mi00YWY2ZWNiYmFmOGU0NjI1YzYxZjBiNjEzOWMyOTYwNl9yLmpwZw?x-oss-process=image/format,png)

用 C(k) 表示向上攀爬 k 个层级所需要走过的平均查找路径长度（概率期望），那么：

```
C(k)=(1-p)(C(k)+1) + p(C(k-1)+1)
C(k)=1/p+C(k-1)
C(k)=k/p
```

代入，得到一个差分方程并化简：

```
> PFADD visitors alice bob carol
 
(integer) 1
 
> PFCOUNT visitors
 
(integer) 3
```

这个结果的意思是，我们每爬升 1 个层级，需要在查找路径上走 1/p 步。而我们总共需要攀爬的层级数等于整个 skiplist 的总层数 - 1。

那么接下来我们需要分析一下当 skiplist 中有 n 个节点的时候，它的总层数的概率均值是多少。这个问题直观上比较好理解。根据节点的层数随机算法，容易得出：

*   第 1 层链表固定有 n 个节点；
*   第 2 层链表平均有 n*p 个节点；
*   第 3 层链表平均有 n*p^2 个节点；
*   ...

所以，从第 1 层到最高层，各层链表的平均节点数是一个指数递减的等比数列。容易推算出，总层数的均值为 log1/pn，而最高层的平均节点数为 1/p。

综上，粗略来计算的话，平均查找长度约等于：

*   C(log1/pn-1)=(log1/pn-1)/p

即，平均时间复杂度为 O(log n)。

当然，这里的时间复杂度分析还是比较粗略的。比如，沿着查找路径向左向上回溯的时候，可能先到达左侧头结点，然后沿头结点一路向上；还可能先到达最高层的节点，然后沿着最高层链表一路向左。但这些细节不影响平均时间复杂度的最后结果。另外，这里给出的时间复杂度只是一个概率平均值，但实际上计算一个精细的概率分布也是有可能的。

详情还请参见 [William Pugh](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/William_Pugh) 的论文《[Skip Lists: A Probabilistic Alternative to Balanced Trees](https://link.zhihu.com/?target=ftp%3A//ftp.cs.umd.edu/pub/skipLists/skiplists.pdf)》。

### 2.2.3skiplist 与平衡树、哈希表的比较

*   skiplist 和各种平衡树（如 AVL、红黑树等）的元素是有序排列的，而哈希表不是有序的。因此，在哈希表上只能做单个 key 的查找，不适宜做范围查找。所谓范围查找，指的是查找那些大小在指定的两个值之间的所有节点。
*   在做范围查找的时候，平衡树比 skiplist 操作要复杂。在平衡树上，我们找到指定范围的小值之后，还需要以中序遍历的顺序继续寻找其它不超过大值的节点。如果不对平衡树进行一定的改造，这里的中序遍历并不容易实现。而在 skiplist 上进行范围查找就非常简单，只需要在找到小值之后，对第 1 层链表进行若干步的遍历就可以实现。
*   平衡树的插入和删除操作可能引发子树的调整，逻辑复杂，而 skiplist 的插入和删除只需要修改相邻节点的指针，操作简单又快速。
*   从内存占用上来说，skiplist 比平衡树更灵活一些。一般来说，平衡树每个节点包含 2 个指针（分别指向左右子树），而 skiplist 每个节点包含的指针数目平均为 1/(1-p)，具体取决于参数 p 的大小。如果像 Redis 里的实现一样，取 p=1/4，那么平均每个节点包含 1.33 个指针，比平衡树更有优势。
*   查找单个 key，skiplist 和平衡树的时间复杂度都为 O(log n)，大体相当；而哈希表在保持较低的哈希值冲突概率的前提下，查找时间复杂度接近 O(1)，性能更高一些。所以我们平常使用的各种 Map 或 dictionary 结构，大都是基于哈希表实现的。
*   从算法实现难度上来比较，skiplist 比平衡树要简单得多。

### 2.2.4Redis 中的 skiplist 和经典有何不同

*   分数 (score) 允许重复，即 skiplist 的 key 允许重复。这在最开始介绍的经典 skiplist 中是不允许的。
*   在比较时，不仅比较分数（相当于 skiplist 的 key），还比较数据本身。在 Redis 的 skiplist 实现中，数据本身的内容唯一标识这份数据，而不是由 key 来唯一标识。另外，当多个元素分数相同的时候，还需要根据数据内容来进字典排序。
*   第 1 层链表不是一个单向链表，而是一个双向链表。这是为了方便以倒序方式获取一个范围内的元素。
*   在 skiplist 中可以很方便地计算出每个元素的排名 (rank)。

### 2.2.5 作者的话

最后我们看看，对于这个问题，Redis 的作者 @antirez 是怎么说的：

There are a few reasons:

1) They are not very memory intensive. It's up to you basically. Changing parameters about the probability of a node to have a given number of levels will make then less memory intensive than btrees.

2) A sorted set is often target of many ZRANGE or ZREVRANGE operations, that is, traversing the skip list as a linked list. With this operation the cache locality of skip lists is at least as good as with other kind of balanced trees.

3) They are simpler to implement, debug, and so forth. For instance thanks to the skip list simplicity I received a patch (already in Redis master) with augmented skip lists implementing ZRANK in O(log(N)). It required little changes to the code.

有几个原因：

1）它们的记忆力不是很强。基本上由你决定。更改有关节点具有给定数量级别的概率的参数将使内存密集度低于 btree。

2）排序集通常是许多 Zrange 或 Zrevrange 操作的目标，即作为链表遍历跳过列表。通过此操作，跳过列表的缓存区域性至少与其他类型的平衡树一样好。

3）它们易于实现、调试等。例如，由于跳过列表的简单性，我收到了一个补丁（已经在 redis master 中），其中包含在 o（log（n））中实现 zrank 的扩展跳过列表。它只需要对代码稍作修改。

2.3HyperLogLog 专栏
-----------------

HyperLogLog 是一种概率数据结构，用来估算数据的基数。数据集可以是网站访客的 IP 地址，E-mail 邮箱或者用户 ID。

基数就是指一个集合中不同值的数目，比如 a, b, c, d 的基数就是 4，a, b, c, d, a 的基数还是 4。虽然 a 出现两次，只会被计算一次。

使用 Redis 统计集合的基数一般有三种方法，分别是使用 Redis 的 HashMap，BitMap 和 HyperLogLog。前两个数据结构在集合的数量级增长时，所消耗的内存会大大增加，但是 HyperLogLog 则不会。

Redis 的 HyperLogLog 通过牺牲准确率来减少内存空间的消耗，只需要 12K 内存，在标准误差 0.81% 的前提下，能够统计 2^64 个数据。所以 HyperLogLog 是否适合在比如统计日活月活此类的对精度要不不高的场景。

这是一个很惊人的结果，以如此小的内存来记录如此大数量级的数据基数。下面我们就带大家来深入了解一下 HyperLogLog 的使用，基础原理，源码实现和具体的试验数据分析。

### 2.3.1HyperLogLog 在 Redis 中的使用

Redis 提供了 `PFADD` 、 `PFCOUNT` 和 `PFMERGE` 三个命令来供用户使用 HyperLogLog。

`PFADD` 用于向 HyperLogLog 添加元素。

```
> PFADD customers alice dan
 
(integer) 1
 
> PFMERGE everyone visitors customers
 
OK
 
> PFCOUNT everyone
 
(integer) 4
```

如果 HyperLogLog 估计的近似基数在 `PFADD` 命令执行之后出现了变化， 那么命令返回 1 ， 否则返回 0 。 如果命令执行时给定的键不存在， 那么程序将先创建一个空的 HyperLogLog 结构， 然后再执行命令。

`PFCOUNT` 命令会给出 HyperLogLog 包含的近似基数。在计算出基数后， `PFCOUNT` 会将值存储在 HyperLogLog 中进行缓存，知道下次 `PFADD` 执行成功前，就都不需要再次进行基数的计算。

`PFMERGE` 将多个 HyperLogLog 合并为一个 HyperLogLog ， 合并后的 HyperLogLog 的基数接近于所有输入 HyperLogLog 的并集基数。

```
local key = KEYS[1]
 
local size = tonumber(ARGV[1])
 
local method = tonumber(ARGV[2])
 
 
 
for i=1,size,1 do
 
if (method == 0)
 
then
 
redis.call('hset',key,i,1)
 
elseif (method == 1)
 
then
 
redis.call('pfadd',key, i)
 
else
 
redis.call('setbit', key, i, 1)
 
end
 
end
```

### 2.3.2 内存消耗对比实验

我们下面就来通过实验真实对比一下下面三种数据结构的内存消耗，HashMap、BitMap 和 HyperLogLog。

我们首先使用 Lua 脚本向 Redis 对应的数据结构中插入一定数量的数，然后执行 bgsave 命令，最后使用 redis-rdb-tools 的 rdb 的命令查看各个键所占的内存大小。

下面是 Lua 的脚本

```
struct hllhdr {
 
char magic[4]; /* 魔法值 "HYLL" */
 
uint8_t encoding; /* 密集结构或者稀疏结构 HLL_DENSE or HLL_SPARSE. */
 
uint8_t notused[3]; /* 保留位, 全为0. */
 
uint8_t card[8]; /* 基数大小的缓存 */
 
uint8_t registers[]; /* 数据字节数组 */
 
};
```

我们在通过 redis-cli 的 `script load` 命令将 Lua 脚本加载到 Redis 中，然后使用 `evalsha` 命令分别向 HashMap、HyperLogLog 和 BitMap 三种数据结构中插入了一千万个数，然后使用 `rdb` 命令查看各个结构内存消耗。

我们进行了两轮实验，分别插入一万数字和一千万数字，三种数据结构消耗的内存统计如下所示。

![](https://img-blog.csdnimg.cn/20191014153632836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

从表中可以明显看出，一万数量级时 BitMap 消耗内存最小， 一千万数量级时 HyperLogLog 消耗内存最小，但是总体来看，HyperLogLog 消耗的内存都是 14392 字节，可见 HyperLogLog 在内存消耗方面有自己的独到之处。

### 2.3.3 基本原理

HyperLogLog 是一种概率数据结构，它使用概率算法来统计集合的近似基数。而它算法的最本源则是伯努利过程。

伯努利过程就是一个抛硬币实验的过程。抛一枚正常硬币，落地可能是正面，也可能是反面，二者的概率都是 1/2 。伯努利过程就是一直抛硬币，直到落地时出现正面位置，并记录下抛掷次数 k。比如说，抛一次硬币就出现正面了，此时 k 为 1; 第一次抛硬币是反面，则继续抛，直到第三次才出现正面，此时 k 为 3。

对于 n 次伯努利过程，我们会得到 n 个出现正面的投掷次数值 k1, k2 ... kn , 其中这里的最大值是 k_max。

根据一顿数学推导，我们可以得出一个结论： 2^{k_ max} 来作为 n 的估计值。也就是说你可以根据最大投掷次数近似的推算出进行了几次伯努利过程。

![](https://img-blog.csdnimg.cn/20191014160136142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

下面，我们就来讲解一下 HyperLogLog 是如何模拟伯努利过程，并最终统计集合基数的。

HyperLogLog 在添加元素时，会通过 Hash 函数，将元素转为 64 位比特串，例如输入 5，便转为 101(省略前面的 0，下同)。这些比特串就类似于一次抛硬币的伯努利过程。比特串中，0 代表了抛硬币落地是反面，1 代表抛硬币落地是正面，如果一个数据最终被转化了 10010000，那么从低位往高位看，我们可以认为，这串比特串可以代表一次伯努利过程，首次出现 1 的位数为 5，就是抛了 5 次才出现正面。

所以 HyperLogLog 的基本思想是利用集合中数字的比特串第一个 1 出现位置的最大值来预估整体基数，但是这种预估方法存在较大误差，为了改善误差情况，HyperLogLog 中引入分桶平均的概念，计算 m 个桶的调和平均值。

![](https://img-blog.csdnimg.cn/20191014161455583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

Redis 中 HyperLogLog 一共分了 2^14 个桶，也就是 16384 个桶。每个桶中是一个 6 bit 的数组。

HyperLogLog 将上文所说的 64 位比特串的低 14 位单独拿出，它的值就对应桶的序号，然后将剩下 50 位中第一次出现 1 的位置值设置到桶中。50 位中出现 1 的位置值最大为 50，所以每个桶中的 6 位数组正好可以表示该值。

在设置前，要设置进桶的值是否大于桶中的旧值，如果大于才进行设置，否则不进行设置。

此时为了性能考虑，是不会去统计当前的基数的，而是将 HyperLogLog 头的 card 属性中的标志位置为 1，表示下次进行 pfcount 操作的时候，当前的缓存值已经失效了，需要重新统计缓存值。在后面 pfcount 流程的时候，发现这个标记为失效，就会去重新统计新的基数，放入基数缓存。

在计算近似基数时，就分别计算每个桶中的值，带入到上文的 DV 公式中，进行调和平均和结果修正，就能得到估算的基数值。

### 2.3.4HyperLogLog 具体对象

我们首先来看一下 HyperLogLog 对象的定义

```
public static class Node<V> {
		public V value;
		public Node<V> last;//前
		public Node<V> next;//后
 
		public Node(V value) {
			this.value = value;
		}
	}
```

HyperLogLog 对象中的 `registers` 数组就是桶，它有两种存储结构，分别为密集存储结构和稀疏存储结构，两种结构只涉及存储和桶的表现形式，从中我们可以看到 Redis 对节省内存极致地追求。

我们先看相对简单的**密集存储结构**，它也是十分的简单明了，既然要有 2^14 个 6 bit 的桶，那么我就真使用足够多的 `uint8_t` 字节去表示，只是此时会涉及到字节位置和桶的转换，因为字节有 8 位，而桶只需要 6 位。

所以我们需要将桶的序号转换成对应的字节偏移量 offsetbytes 和其内部的位数偏移量 offsetbits。需要注意的是小端字节序，高位在右侧，需要进行倒转。

当 offset_bits 小于等于 2 时，说明一个桶就在该字节内，只需要进行倒转就能得到桶的值。

 offset_bits 大于 2 ，则说明一个桶分布在两个字节内，此时需要将两个字节的内容都进行倒置，然后再进行拼接得到桶的值。

Redis 为了方便表达**稀疏存储**，它将上面三种字节表示形式分别赋予了一条指令。

*   ZERO : 一字节，表示连续多少个桶计数为 0，前两位为标志 00，后 6 位表示有多少个桶，最大为 64。
    
*   XZERO : 两个字节，表示连续多少个桶计数为 0，前两位为标志 01，后 14 位表示有多少个桶，最大为 16384。
    
*   VAL : 一字节，表示连续多少个桶的计数为多少，前一位为标志 1，四位表示连桶内计数，所以最大表示桶的计数为 32。后两位表示连续多少个桶。
    

Redis 从稀疏存储转换到密集存储的条件是：

*   任意一个计数值从 32 变成 33，因为 VAL 指令已经无法容纳，它能表示的计数值最大为 32
    
*   稀疏存储占用的总字节数超过 3000 字节，这个阈值可以通过 hllsparsemax_bytes 参数进行调整。
    

2.4LRU 专栏
---------

### 2.4.1LRU 介绍和代码实现

LRU 全称是 Least Recently Used，即最近最久未使用的意思。

LRU 算法的设计原则是：**如果一个数据在最近一段时间没有被访问到，那么在将来它被访问的可能性也很小**。也就是说，当限定的空间已存满数据时，应当把最久没有被访问到的数据淘汰。（这一段是找的，让大家理解一下什么是 LRU）。

说一下我们什么时候见到过 LRU：其实老师们肯定都给大家举过这么个例子：你在图书馆，你把书架子里的书拿到桌子上。。但是桌子是有限的，你有时候不得不把一些书放回去。这就相当于内存和硬盘。这个例子都说过吧？

LRU 就是记录你最长时间没看过的书，就把它放回去。在 cache 那里见过吧

然后最近在研究 redis，又看到了这个 LRU，所以就想写一下吧。

题目：设计一个结构，这个结构可以查询 K-V，但是容量有限，当存不下的时候就要把用的年代最久远的那个东西扔掉。

其实思路很简单，我们维护一个双向链表即可，get 也就是使用了，我们就把把它提到最安全的位置。新来的 KV 就依次放即可。

我们就先写这个双向链表结构

先写节点结构：

```
public static class NodeDoubleLinkedList<V> {
		private Node<V> head;//头
		private Node<V> tail;//尾
 
		public NodeDoubleLinkedList() {
			this.head = null;
			this.tail = null;
		}
 
		public void addNode(Node<V> newNode) {
			if (newNode == null) {
				return;
			}
			if (this.head == null) {//头空
				this.head = newNode;
				this.tail = newNode;
			} else {//头不空
				this.tail.next = newNode;
				newNode.last = this.tail;//注意让本节点前指针指向旧尾
				this.tail = newNode;//指向新尾
			}
		}
/*某个点移到最后*/
		public void moveNodeToTail(Node<V> node) {
			if (this.tail == node) {//是尾
				return;
			}
			if (this.head == node) {//是头
				this.head = node.next;
				this.head.last = null;
			} else {//中间
				node.last.next = node.next;
				node.next.last = node.last;
			}
			node.last = this.tail;
			node.next = null;
			this.tail.next = node;
			this.tail = node;
		}
/*删除第一个*/
		public Node<V> removeHead() {
			if (this.head == null) {
				return null;
			}
			Node<V> res = this.head;
			if (this.head == this.tail) {//就一个
				this.head = null;
				this.tail = null;
			} else {
				this.head = res.next;
				res.next = null;
				this.head.last = null;
			}
			return res;
		}
 
	}
```

然后写双向链表结构： 我们没必要把链表操作都写了，分析一下，我们只有三个操作：

1、加节点

2、使用了某个节点就把它调到尾，代表优先级最高

3、把优先级最低的移除，也就是去头部

（不会的，翻我之前的链表操作都有写）

```
public static class MyCache<K, V> {
		//为了kv or vk都能查
		private HashMap<K, Node<V>> keyNodeMap;
		private HashMap<Node<V>, K> nodeKeyMap;
		//用来做优先级
		private NodeDoubleLinkedList<V> nodeList;
		private int capacity;//容量
 
		public MyCache(int capacity) {
			if (capacity < 1) {//你容量连1都不给，捣乱呢
				throw new RuntimeException("should be more than 0.");
			}
			this.keyNodeMap = new HashMap<K, Node<V>>();
			this.nodeKeyMap = new HashMap<Node<V>, K>();
			this.nodeList = new NodeDoubleLinkedList<V>();
			this.capacity = capacity;
		}
 
		public V get(K key) {
			if (this.keyNodeMap.containsKey(key)) {
				Node<V> res = this.keyNodeMap.get(key);
				this.nodeList.moveNodeToTail(res);//使用过了就放到尾部
				return res.value;
			}
			return null;
		}
 
		public void set(K key, V value) {
			if (this.keyNodeMap.containsKey(key)) {
				Node<V> node = this.keyNodeMap.get(key);
				node.value = value;//放新v
				this.nodeList.moveNodeToTail(node);//我们认为放入旧key也是使用过
			} else {
				Node<V> newNode = new Node<V>(value);
				this.keyNodeMap.put(key, newNode);
				this.nodeKeyMap.put(newNode, key);
				this.nodeList.addNode(newNode);//加进去
				if (this.keyNodeMap.size() == this.capacity + 1) {
					this.removeMostUnusedCache();//放不下就去掉优先级最低的
				}
			}
		}
 
		private void removeMostUnusedCache() {
			//删除头
			Node<V> removeNode = this.nodeList.removeHead();
			K removeKey = this.nodeKeyMap.get(removeNode);
			//删除掉两个map中的记录
			this.nodeKeyMap.remove(removeNode);
			this.keyNodeMap.remove(removeKey);
		}
	}
```

链表操作封装完了就要实现这个结构了。

具体思路代码注释

```
~~~~~A~~~~~A~~~~~A~~~~A~~~~~A~~~~~A~~|
~~B~~B~~B~~B~~B~~B~~B~~B~~B~~B~~B~~B~|
~~~~~~~~~~C~~~~~~~~~C~~~~~~~~~C~~~~~~|
~~~~~D~~~~~~~~~~D~~~~~~~~~D~~~~~~~~~D|
```

### 2.4.2Redis 中的 LRU 算法改进

redis 通常使用缓存，是使用一种固定最大内存的使用。当数据达到可使用的最大固定内存时，我们需要通过移除老数据来获取空间。redis 作为缓存是否有效的重要标志是如何寻找一种好的策略：删除即将需要使用的数据是一种糟糕的策略，而删除那些很少再次请求的数据则是一种好的策略。  
在其他的缓存组件还有个命中率，仅仅表示读请求的比例。访问一个缓存中的 keys 通常不是分布式的。然而访问经常变化，这意味着不经常访问，相反，有些 keys 一旦不流行可能会转向最经常访问的 keys。 因此，通常一个缓存系统应该尽可能保留那些未来最有可能被访问的 keys。针对 keys 淘汰的策略是：那些未来极少可能被访问的数据应该被移除。  
但有一个问题：redis 和其他缓存系统不能够预测未来。

**LRU 算法**

缓存系统不能预测未来，原因是：那些很少再次被访问的 key 也很有可能最近访问相当频繁。如果经常被访问的模式不会突然改变，那么这是一种很有效的策略。然而，“最近经常被访问” 似乎更隐晦地标明一种 理念。这种算法被称为 LRU 算法。最近访问频繁的 key 相比访问少的 key 有更高的可能性。  
举个例子，这里有 4 个不同访问周期的 key，每一个 “~” 字符代表一秒，结尾的 “|” 表示当前时刻。

```
uint8_t LFULogIncr(uint8_t counter) {
      if (counter == 255) return 255;
      double r = (double)rand()/RAND_MAX;
      double baseval = counter - LFU_INIT_VAL;
      if (baseval < 0) baseval = 0;
      double p = 1.0/(baseval*server.lfu_log_factor+1);
      if (r < p) counter++;
      return counter;
  }
```

A key 每 5 秒请求一次，B 周期是 2 秒，C、D 都是 10 秒。  
访问频率最高的是 B，因为它的空闲时间最短，这意味着 B 是 4 个 key 中未来最有可能被访问的 key。  
同样的 A 和 C 目前的空闲时间是 2s 和 6s 也能很好地反映它们本身的周期。然而你可以看到不够严谨：D 的访问周期是 10 秒，但它却是 4 个 key 中最近被访问的。  
当然，在一个很长的运行周期中，LRU 算法能工作得很好。通常有一个更高访问频率的 key 当然有一个更低的空闲周期。LRU 算法淘汰最少被访问 key，那些有最大空闲周期的 key。实现上也相当容易，只需要额外跟踪最近被访问的 key 即可，有时甚至都需要：把所有我们想要淘汰的对象放到一个链表中，当一个对象访问就移除链表头部元素，当我们要淘汰元素是就直接淘汰链表尾部开始。

**redis 中的 LRU: 起因**

最初，redis 不支持 LRU 算法。当内存有效性成为一个必须被解决的问题时，后来才加上了。通过修改 redis 对象结构，在每个 key 对象增加 24bit 的空间。没有额外的空间使用链表把所有对象放到一个链表中（大指针），因此需要实现得更加有效，不能因为 key 淘汰算法而让整个服务改动太大。  
24bits 的对象已经足够去存储当前的 unxi 时间戳。这个表现，被称为 “LRU 时钟”，key 元数据经常被更新，所以它是一个有效的算法。  
然后，有另一个更加复杂的问题需要解决：如何选择访问间隔最长的 key，然后淘汰它。  
redis 内部采用一个庞大的 hash table 来保存，添加另外一个数据结构存储时间间隔显然不是一个好的选择。然而我们希望能达到一个 LRU 本身是一个近似的，通过 LRU 算法本身来实现。

redis 原始的淘汰算法简单实现：** 当需要淘汰一个 key 时，随机选择 3 个 key，淘汰其中间隔时间最长的 key。** 基本上，我们随机选择 key，淘汰 key 效果很好。后来随机 3 个 key 改成一个配置项 "N 随机 key"。但把默认值提高改成 5 个后效果大大提高。考虑到它的效果，你根本不用修改他。

然而，你可能会想这个算法如何有效执行，你可以看到我们如何捣毁了很多有趣的数据。也许简单的 N key，我们会遇到很多好的决策，但是当我们淘汰最好的，下一个周期又开始抓。

**验证规则第一条：用肉眼观察你的算法**

其中有一个观点已经应用到 Redis 3.0 正式版中了。在 redis2.8 中一个 LRU 缓存经常被使用在多个环境，用户关于淘汰的没有抱怨太多，但是很明显我可以提高它，通过不仅仅是增加额外的空间，还有额外的 CPU 时间。  
然而为了提高某项功能，你必须观察它。有多个不同的方式去观察 LRU 算法。你可以通过写工具观察，例如模拟不同的工作负载、校验命中率和失误率。  
程序非常简单：增加一些指定的 keys，然后频繁地访问这些 keys 以至于每一个 key 都有一个下降的空闲时间。最终超过 50% 的 keys 被增加，一半的老 key 需要被淘汰。  
一个完美理想的 LRU 实现，应该是没有最新加的 key 被淘汰，而是淘汰最初的 50% 的老 key。

**规则二：不要丢弃重要信息**

借助最新的可视化工具，我可以在尝试新的方法观察和测试几分钟。使用 redis 最明显有效的提高算法就是，积累对立的垃圾信息在一个淘汰池中。  
基本上，当 N keys 算法被采用时，通常会分配一个很大的线程 pool（默认为 16key），这个池按照空闲时间排序，所以只有当有一个大于池中的一个或者池为空的时候，最新的 key 只会进入到这个池中。  
同时，一个新的 redis-cli 模式去测量 LRU 算法也增加了 (看这个 - lru-test 选项)。  
还有另外一个方式去检验 LRU 算法的好坏，通过一个幂等访问模式。这个工具通常校验用一个不同的测试，新算法工作工作效果好于真实世界负载。它也同样使用流水线和每秒打印访问日志，因此可以被使用不用为了基准不同的思想，至少可以校验和观察明显的速度回归。

**规则三、最少使用原则（LFU 算法）**

  
一切源于一个开放性问题：但你有多个 redis 3.2 数据库时，而淘汰算法只能在本机选择。因此，假如你全部空闲小的 key 都是 DB0 号机器，空闲时间长的 key 都是 1 号机器，redis 每台机器都会淘汰各自的 key。一个更好的选择当然是先淘汰 DB1，最后再淘汰 DB0。  
当 redis 被当作缓存使用时很少有情况被分成不同的 db 上，这不是一个好的处理方式。然而这也是我为什么我再一次修改淘汰代码的原因。最终，我能够修改缓存池包括数据库 id，使用单缓存池为多个 db，代替多缓存池。这种实现很麻烦，但是通过优化和修改代码，最终它比普通实现要快到 20%。  
然而这时候，我对这个 redis 缓存淘汰算法的好奇心又被点燃。我想要提升它。我花费了几天想要提高 LRU 算法实现：或许可以使用更大的缓存池？通过历史时间选择最合适被淘汰的 key？  
经过一段时间，通过优化我的工具，我理解到：LRU 算法受限于数据库中的数据样本，有时可能相反的场景效果非常好，因此要想提高非常非常难。实际上，能通过展示不同算法的图片上看这有点非常明显：每个周期 10 个 keys 几乎和理论的 LRU 算法表现一致。  
当原始算法很难提高时，我开始测试新的算法。 如果我们倒回到博客开始，我们说过 LRU 实际上有点严格。哪些 key 需要我们真正想要保留：将来有最大可能被访问，最频繁被访问，而不是最近被访问的 key。  
淘汰最少被访问的 key 算法成为：LFU（Least Frequently Used），将来要被淘汰腾出新空间给新 key。  
理论上 LFU 的思想相当简单，只需要给每个 key 加一个访问计数器。每次访问就自增 1，所以也就很容易知道哪些 key 被访问更频繁。  
当然，LFU 也会带起其他问题，不单单是针对 redis，对于 LFU 实现：  
1、不能使用 “移除顶部元素” 的方式，keys 必须要根据访问计数器进行排序。每访问一次就得遍历所有 key 找出访问次数最少的 key。  
2、LFU 不能仅仅是只增加每一访问的计数器。正如我们所讲的，访问模式改变随时变化，因此一个有高访问次数的 key，后面很可能没有人继续访问它，因此我们的算法必须要适应超时的情况。  
在 redis 中，第一个问题很好解决：我们可以在 LRU 的方式一样：随机在缓存池中选举，淘汰其中某项。第二个问题 redis 还是存在，因此一般对于 LFU 的思想必须使用一些方式进行减少，或者定期把访问计数器减半。

**24 位的 LFU 实现**

LFU 有它本身的实现，在 redis 中我们使用自己的 24bit 来记录 LRU。  
为了实现 LFU 仅仅需要在每个对象额外新增 24bit：  
1、一部分用于保存访问计数器；  
2、足够用于决定什么时候将计数器减半的信息；

我的解决方法是把 24bit 分成两列：

16bits8bitslast decr timeLOG_C

16 位记录最后一次减半时间，那样 redis 知道上一次减半时间，另外 8bit 作为访问计数器。  
你可能会想 8 位的计数器很快就会溢出，是的，相对于简单计数器，我采用逻辑计数器。逻辑计数器的实现：

```
# 键为字符串对象，值为列表对象
 
redis> RPUSH numbers 1 3 5
(integer) 6
 
redis> TYPE numbers
list
```

基本上计数器的较大者，更小的可能计数器会增加：上面的代码计算 p 位于 0~1 之间，但计数器增长时会越来越小，位于 0-1 的随机数 r，只会但满足 r<p 时计数器才会加一。  
你可以配置计数器增长的速率，如果使用默认配置，会发生：

*   100 次访问后，计数器 = 10；
*   1000 次访问是是 18；
*   10 万次访问是 142；
*   100 万次访问后达到 255，并不在继续增长；

下面，让我们看看计数器如果进行衰减。16 位的被储存为 unix 时间戳保留到分钟级别，redis 会随机扫描 key 填充到缓存池中，如果最后一个下降的时间大于 N 分钟前（可配置化），如果计数器的值很大就减半，或者对于值小的就直接简单减半。  
这里又衍生出另外一个问题，就是新进来的 key 是需要有机会被保留的。由于 LFU 新增是得分都是 0，非常容易被选举替换掉。在 redis 中，开始默认值为 5。这个初始值是根据增长数据和减半算法来估算的。模拟显示得分小于 5 的 key 是首选。

**代码和性能**

上面描述的算法已经提交到一个非稳定版的 redis 分支上。我最初的测试显示：它在幂等模式下优于 LRU 算法，测试情况是每个 key 使用用相同数量的内存，然而真实世界的访问可能会有很大不同。时间和空间都可能改变得很不同，所以我会很开心去学习观察现实世界中 LFU 的性能如何，两种方式在 redis 实现中对性能的改变。  
因此，新增了一个 OBJECT FREQ 子命令，用于报告给定 key 的访问计数器，不仅仅能有效提观察一个计数器，而且还能调试 LFU 实现中的 bug。  
注意运行中切换 LRU 和 LFU，刚开始会随机淘汰一些 key，随着 24bit 不能匹配上，然而慢慢会适应。 还有几种改进实现的可能。Ben Manes 发给我这篇感兴趣的文章，描述了一种叫 TinyLRU 算法。[链接](http://arxiv.org/pdf/1512.00727.pdf)

这篇文章包含一个非常厉害的观点：相比于记录当前对象的访问频率，让我们（概率性地）记录全部对象的访问频率，看到了，这种方式我们甚至可以拒绝新 key，同样，我们相信这些 key 很可能得到很少的访问，所以一点也不需要淘汰，如果淘汰一个 key 意味着降低命中 / 未命中率。  
我的感觉这种技术虽然很感兴趣 GET/SET LFU 缓存，但不适用与 redis 性质的数据服务器：用户期望 keys 被创建后至少存在几毫秒。拒绝 key 的创建似乎在 redis 上就是一种错误。  
然而，redis 保留了 LFU 信息，当一个 key 被覆盖时，举个例子：

```
typedef struct redisObject {
    // 类型
    unsigned type:4;
    // 编码
    unsigned encoding:4;
    // 指向底层实现数据结构的指针
    void *ptr;
    // ...
} robj;
```

24 位的 LFU 计数器会从老的 key 复制到新对象中。

新的 redis 淘汰算法不稳定版本还有以下几个好消息：  
1、跨 DB 策略。在过去的 redis 只是基于本地的选举，现在修复为所有策略，不仅仅是 LRU。  
2、易变 ttl 策略。基于 key 预期淘汰存活时间，如今就像其他策略中的使用缓存池。  
3、在缓存池中重用了 sds 对象，性能更好。

这篇博客比我预期要长，但是我希望它反映出一个见解：在创新和对于已经存在的事物实现上，一种解决方案去解决一个特定问题，一个基础工具。由开发人员以正确的方式使用它。许多 redis 的用户把 redis 作为一个缓存的解决方案，因此提高淘汰策略这一块经常一次又一次被拿出来探讨。

2.6 对象
------

刚写了 redis 主要的数据结构：

动态字符串、双端链表、字典、压缩列表、整数集合、跳表等

redis 肯定不能直接使用这些数据结构来实现数据库，它用这些数据库建立了一个对象系统，包含：

字符串对象、列表对象、哈希对象、集合对象、有序集合对象

我们可以针对不同的使用场景，为对象设置多种分不同的数据结构实现，从而优化对象在不同场景下的效率。

**1）键值对**

对于 redis 的键值对来说：key 只有字符串类型，而 v 可以是各种类型，

我们习惯把 “这个键所对应的值是一个列表” 表达为这是一个“列表键。

TYPE 命令的实现方式也与此类似， 当我们对一个数据库键执行 TYPE 命令时， 命令返回的结果为数据库键对应的值对象的类型， 而不是键对象的类型：

```
struct redisServer {
    // ...
    // AOF 缓冲区
    sds aof_buf;
    // ...
};
```

**2）对象**

我们看一下 redis 对象的组成：

```
def eventLoop():
 
    while True:
 
        # 处理文件事件，接收命令请求以及发送命令回复
        # 处理命令请求时可能会有新内容被追加到 aof_buf 缓冲区中
        processFileEvents()
 
        # 处理时间事件
        processTimeEvents()
 
        # 考虑是否要将 aof_buf 中的内容写入和保存到 AOF 文件里面
        flushAppendOnlyFile()
```

**通过 `encoding` 属性来设定对象所使用的编码， 而不是为特定类型的对象关联一种固定的编码， 极大地提升了 Redis 的灵活性和效率， 因为 Redis 可以根据不同的使用场景来为一个对象设置不同的编码， 从而优化对象在某一场景下的效率。**

字符串对象

字符串对象的编码可以是 `int` 、 `raw` 或者 `embstr` 。

如果一个字符串对象保存的是整数值， 并且这个整数值可以用 `long` 类型来表示， 那么字符串对象会将整数值保存在字符串对象结构的 `ptr`属性里面（将 `void*` 转换成 `long` ）， 并将字符串对象的编码设置为 `int` 。

如果字符串对象保存的是一个字符串值， 并且这个字符串值的长度大于 `39` 字节， 那么字符串对象将使用一个简单动态字符串（SDS）来保存这个字符串值， 并将对象的编码设置为 `raw` 。

如果字符串对象保存的是一个字符串值， 并且这个字符串值的长度小于等于 `39` 字节， 那么字符串对象将使用 `embstr` 编码的方式来保存这个字符串值。

`embstr` 编码是专门用于保存短字符串的一种优化编码方式， 这种编码和 `raw` 编码一样， 都使用 `redisObject` 结构和 `sdshdr` 结构来表示字符串对象，但 `raw` 编码会调用两次内存分配函数来分别创建 `redisObject` 结构和 `sdshdr` 结构，而 `embstr` 编码则通过调用一次内存分配函数来分配一块连续的空间， 空间中依次包含 `redisObject` 和 `sdshdr` 两个结构。

 `embstr` 编码有以下好处：

1.  `embstr` 编码创建删除字符串对象只需操作一次内存
2.  因为数据都保存在一块连续的内存， 所以这种编码的字符串对象比 `raw` 编码字符串对象能更好地利用缓存带来的优势。

**3）列表对象**

列表对象的编码可以是 `ziplist` 或者 `linkedlist` 。

当列表对象可以同时满足以下两个条件时， 列表对象使用 `ziplist` 编码：

1.  列表对象保存的所有字符串元素的长度都小于 `64` 字节；
2.  列表对象保存的元素数量小于 `512` 个；

不能满足这两个条件的列表对象需要使用 `linkedlist` 编码。

**4）哈希对象**

哈希对象的编码可以是 `ziplist` 或者 `hashtable` 。

当哈希对象可以同时满足以下两个条件时， 哈希对象使用 `ziplist` 编码：

1.  哈希对象保存的所有键值对的键和值的字符串长度都小于 `64` 字节；
2.  哈希对象保存的键值对数量小于 `512` 个；

不能满足这两个条件的哈希对象需要使用 `hashtable` 编码。

**5）集合对象**

集合对象的编码可以是 `intset` 或者 `hashtable` 。

当集合对象可以同时满足以下两个条件时， 对象使用 `intset` 编码：

1.  集合对象保存的所有元素都是整数值；
2.  集合对象保存的元素数量不超过 `512` 个；

不能满足这两个条件的集合对象需要使用 `hashtable` 编码。

**6）有序集合对象**

有序集合的编码可以是 `ziplist` 或者 `skiplist` 。

当有序集合对象可以同时满足以下两个条件时， 对象使用 `ziplist` 编码：

1.  有序集合保存的元素数量小于 `128` 个；
2.  有序集合保存的所有元素成员的长度都小于 `64` 字节；

不能满足以上两个条件的有序集合对象将使用 `skiplist` 编码。

**这里多说两句，各个语言的对象其实都差不多，底层实现也就那几个，比如 java 中的容器，c++ 的 STL。java 的 hashset 就是一个哈希而已，hashmap 就是 k 带了一个 v，而” 有序的 “Treemap 使用了红黑树这种有平衡性的搜索二叉树。**

**redis 的有序集合并没有再采取 hash + 红黑树的操作，而是把平衡树换成了跳表，实际上性能真的没差多少，甚至有时比红黑树有优势，比如跳表的性能较为平均，红黑树攒了很多次不平衡要调整可能会带来资源需求的一个高峰，再加上跳表实现简单的优点，红黑树真的没什么优势。**

并且就算是真的想用一种带平衡性的搜索树，现在竞赛也是用的华人之光发明的 SB 树。

有序集合的优点就是它的有序操作，比如拿最大最小值，红黑树时间 o(logN), 而哈希表只能一个一个遍历。缺点在于插入一个值的时间也是 o(logN), 跳表也是。而哈希表插入数是 o(1).

要了解底层和这些优缺点

> 《三天给你聊清楚 redis》第 2 天看看 redis 怎么被搞出来的（22036 字）

三、单机实现
======

3.1、数据库概述
---------

redis 服务器将所有数据库都保存在 redis/redisServer 中，数组 db 存放所有数据库，每一项是一个 redisdb 结构。dbnum 代表数据库数量。

客户端有一个指针指向当前数据库，可以切换，也就是移动指针。

### 3.1.1 键空间

现在稍微介绍一下 redisdb 结构，它的字典保存了所有键值对

键空间的键也就是数据库的键， 每个键都是一个字符串对象。

键空间的值也就是数据库的值， 每个值可以是字符串对象、列表对象、哈希表对象、集合对象、有序集合对象

所有数据库的操作，添加一个键值对， 删除一个键值对， 获取某个键值对， 等等，都是通过对键空间字典进行操作来实现的。

### 3.1.2 维护

读写键空间的时候，服务器会执行一些额外操作，比如：

*   读一个键后（读操作写操作都要对键读取）， 会根据键是否存在， 更新键空间命中（hit）次数或不命中（miss）次数。
*   读取一个键后， 服务器会更新键的 LRU （最后一次使用）时间， 这个值可以用于计算键的闲置时间。
*   如果服务器在读一个键时， 该键已经过期， 服务器会删除这个键， 然后执行其他操作。
*   如果客户使用 WATCH 监视某个键，在对这个键进行修改之后， 会将这个键记为脏（dirty），让事务程序知到这个键被修改
*   服务器每次修改一个键之后， 都会对脏（dirty）键计数器的值增一， 这个计数器会触发服务器的持久化以及复制操作执行
*   如果服务器开启了数据库通知功能， 那么在对键进行修改之后， 服务器将按配置发送相应的数据库通知。

### 3.1.3 时间

用户可以给某个键设置生存时间，过期时间是一个 UNIX 时间戳，到时间自动删除这个键。

redisdb 结构的 expires 字典保存了所有的键的过期时间，我们称这个字典为过期字典。

### 3.1.4 三种过期键删除策略

1）定时删除：创建一个定时器，到时间立即执行删除操作（对内存友好，因为能保证过期了立马删除，但是对 cpu 不友好）

2）惰性删除：键过期不管，每次获取键时检查是否过期，过期就删除（对 cpu 友好，但是只有在使用的时候才可能删除，对内存不友好）

3）定期删除：隔一段时间检查一次（具体算法决定检查多少删多少，需要合理设置）

### 3.1.5 淘汰策略

当 Redis 占用内存超出最大限制 (maxmemory) 时，可采用如下策略 (maxmemory-policy) ，让 Redis 淘汰一些数据，以腾出空间继续提供读写服务 :

noeviction: 对可能导致增大内存的命令返回错误 (大多数写命令，DEL 除外) ;

volatile-ttl: 在设置了过期时间的 key 中，选择剩余寿命 (TTL) 最短的 key，将其淘汰;

volatile-lru: 在设置了过期时间的 key 中，选择最少使用的 key (RU) ，将其淘汰;

volatile-random: 在设置了过期时间的 key 中，随机选择一些 key，将其淘汰;

allkeys-1Lru: 在所有的 key 中，选择最少使用的 key (LRU) ，将其淘汰;

allkeys-random: 在所有的 key 中，随机选择一些 key，将其淘汰;

3.2、持久化
-------

因为 redis 是内存数据库，他把数据都存在内存里，所以要想办法实现持久化功能。

### 3.2.1、RDB

RDB 持久化可以手动执行，也可以配置定期执行，可以把某个时间的数据状态保存到 RDB 文件中，反之，我们可以用 RDB 文件还原数据库状态。

 **生成**

有两个命令可以生成 RDB 文件：

*   SAVE 命令由服务器进程直接执行保存操作，所以该命令会阻塞服务器，服务器不能接受其他指令。
*   BGSAVE 命令由子进程执行保存操作，所以该命令不会阻塞服务器，服务器可以接受其他指令。。

禁止 BGSAVE 和 SAVE 同时执行，也就是说执行其中一个就会拒绝另一个，这是为了避免父进程和子进程同时执行两个 rdbsave，防止产生竞争条件。

 **载入**

    RDB 载入工作是服务器启动时自动执行的。

 **自动保存**

用户可以通过 save 选项设置多个保存条件，服务器状态中会保存所有用 `save` 选项设置的保存条件，当任意一个保存条件被满足时，服务器会自动执行 BGSAVE 命令。

比如

save 900 1

save 300 10

满足：服务器在 900 秒之内被修改至少一次或者 300 秒内修改至少十次。就会执行 BGSAVE。

当服务器启动时，用户可以通过指定配置文件或者传入启动参数来设置 save 选项，服务器会把条件放到一个结构体里，结构体有一个数组，保存了所有条件。

serverCron 函数默认 100 毫秒检查一次，他会遍历数组依次检查，符合条件就会执行 BGSAVE。

 **RDB 文件结构**

一个完整 RDB 文件所包含的各个部分：

![](https://img-blog.csdnimg.cn/20190418191142434.png)

`REDIS，`长度`5`字节， 保存着 `"REDIS"` 五个字符。 通过这五个字符， 可以在载入文件时， 快速检查载入文件是否 RDB 文件。

`db_version` ，长度 `4` 字节， 它的值是一个字符串表示的整数， 这个整数记录了 RDB 文件的版本号

`databases` 部分包含着零个或任意多个数据库， 以及各个数据库中的键值对数据

`EOF` 常量的长度为 `1` 字节， 这个常量标志着 RDB 文件正文内容的结束

`check_sum` 是一个 `8` 字节长的无符号整数， 保存着一个校验和，以此来检查 RDB 文件是否出错或损坏

我并不想深入探究 databases 的组成。就是知道

*   RDB 文件是一个经过压缩的二进制文件，由多个部分组成。
*   对于不同类型的键值对， RDB 文件会使用不同的方式来保存它们即可。

3.2.2、AOF
---------

AOF 持久化是通过保存服务器执行的命令来记录状态的。还原的时候再执行一遍即可。

功能的实现可以分为命令追加、文件写入、文件同步三个步骤。

当 AOF 持久化功能处于打开状态时， 服务器在执行完一个写命令之后， 会以协议格式将被执行的写命令追加到服务器状态的 `aof_buf` 缓冲区的末尾：

```
# 假设服务器对键list执行了以下命令s;
127.0.0.1:6379> RPUSH list "A" "B"
(integer) 2
127.0.0.1:6379> RPUSH list "C"
(integer) 3
127.0.0.1:6379> RPUSH list "D" "E"
(integer) 5
127.0.0.1:6379> LPOP list
"A"
127.0.0.1:6379> LPOP list
"B"
127.0.0.1:6379> RPUSH list "F" "G"
(integer) 5
127.0.0.1:6379> LRANGE list 0 -1
1) "C"
2) "D"
3) "E"
4) "F"
5) "G"
127.0.0.1:6379>
```

Redis 服务器进程就是一个事件循环

循环中的文件事件负责接收客户端的命令请求， 以及向客户端发送命令回复，

而时间事件则负责执行像 `serverCron` 函数这样需要定时运行的函数。

因为服务器在处理文件事件时可能会执行写命令， 使得一些内容被追加到 `aof_buf` 缓冲区里面， 所以在服务器每次结束一个事件循环之前， 它都会调用 `flushAppendOnlyFile` 函数， 考虑是否需要将 `aof_buf` 缓冲区中的内容写入和保存到 AOF 文件里面， 这个过程可以用伪代码表示：

```
def AOF_REWRITE(tmp_tile_name):
 
  f = create(tmp_tile_name)
 
  # 遍历所有数据库
  for db in redisServer.db:
 
    # 如果数据库为空，那么跳过这个数据库
    if db.is_empty(): continue
 
    # 写入 SELECT 命令，用于切换数据库
    f.write_command("SELECT " + db.number)
 
    # 遍历所有键
    for key in db:
 
      # 如果键带有过期时间，并且已经过期，那么跳过这个键
      if key.have_expire_time() and key.is_expired(): continue
 
      if key.type == String:
 
        # 用 SET key value 命令来保存字符串键
 
        value = get_value_from_string(key)
 
        f.write_command("SET " + key + value)
 
      elif key.type == List:
 
        # 用 RPUSH key item1 item2 ... itemN 命令来保存列表键
 
        item1, item2, ..., itemN = get_item_from_list(key)
 
        f.write_command("RPUSH " + key + item1 + item2 + ... + itemN)
 
      elif key.type == Set:
 
        # 用 SADD key member1 member2 ... memberN 命令来保存集合键
 
        member1, member2, ..., memberN = get_member_from_set(key)
 
        f.write_command("SADD " + key + member1 + member2 + ... + memberN)
 
      elif key.type == Hash:
 
        # 用 HMSET key field1 value1 field2 value2 ... fieldN valueN 命令来保存哈希键
 
        field1, value1, field2, value2, ..., fieldN, valueN =\
        get_field_and_value_from_hash(key)
 
        f.write_command("HMSET " + key + field1 + value1 + field2 + value2 +\
                        ... + fieldN + valueN)
 
      elif key.type == SortedSet:
 
        # 用 ZADD key score1 member1 score2 member2 ... scoreN memberN
        # 命令来保存有序集键
 
        score1, member1, score2, member2, ..., scoreN, memberN = \
        get_score_and_member_from_sorted_set(key)
 
        f.write_command("ZADD " + key + score1 + member1 + score2 + member2 +\
                        ... + scoreN + memberN)
 
      else:
 
        raise_type_error()
 
      # 如果键带有过期时间，那么用 EXPIREAT key time 命令来保存键的过期时间
      if key.have_expire_time():
        f.write_command("EXPIREAT " + key + key.expire_time_in_unix_timestamp())
 
    # 关闭文件
    f.close()
```

`flushAppendOnlyFile` 函数的行为由服务器配置的 `appendfsync` 选项的值来决定

![](https://img-blog.csdnimg.cn/20190418205342258.png)

值为 `always` 时， 服务器在每个事件循环都要将 `aof_buf` 缓冲区中的所有内容写入到 AOF 文件并且同步 AOF 文件， 所以 `always` 的效率最慢的一个， 但从安全性来说， `always` 是最安全的， 因为即使出现故障停机， AOF 持久化也只会丢失一个事件循环中所产生的命令数据。

值为 `everysec` 时， 服务器在每个事件循环都要将 `aof_buf` 缓冲区中的所有内容写入到 AOF 文件， 每隔超过一秒就要在子线程中对 AOF 文件进行一次同步： 从效率上来讲， `everysec` 模式足够快， 并且就算出现故障停机， 数据库也只丢失一秒钟的命令数据。

值为 `no` 时， 服务器在每个事件循环都要将 `aof_buf` 缓冲区中的所有内容写入到 AOF 文件， 至于何时对 AOF 文件进行同步， 则由操作系统控制。

因为处于 `no` 模式下的 `flushAppendOnlyFile` 调用无须执行同步操作， 所以该模式下的 AOF 文件写入速度总是最快的， 不过因为这种模式会在系统缓存中积累一段时间的写入数据， 所以该模式的单次同步时长通常是三种模式中时间最长的： 从平摊操作的角度来看，`no` 模式和 `everysec` 模式的效率类似， 当出现故障停机时， 使用 `no` 模式的服务器将丢失上次同步 AOF 文件之后的所有写命令数据。

 **重写**

AOF 持久化是保存了一堆命令来恢复数据库，随着时间流逝，存的会越来越多，如果不加以控制，文件过大可能影响服务器甚至计算机。而且文件过大，恢复时需要时间也太长。

所以 redis 提供了重写功能，写出的新文件不会包含任何浪费时间的冗余命令。

接下来，我们就介绍重写的原理。

其实重写不会对现有的 AOF 文件进行读取分析等操作，而是通过当前服务器的状态来实现。

```
typedef struct redisClient {
    // ...
 
    int fd;
 
    // ...
} redisClient;
```

当前列表键 list 在数据库中的值就为 ["C", "D", "E", "F", "G"]。要使用尽量少的命令来记录 list 键的状态，最简单的方式不是去读取和分析现有 AOF 文件的内容，，而是直接读取 list 键在数据库中的当前值，然后用一条 RPUSH list "C" "D" "E" "F" "G" 代替前面的 6 条命令。

*   伪代码表示如下

```
typedef struct redisClient {
    // ...
 
    int flags;
 
    // ...
 
} redisClient;
```

 **AOF 后台重写**

  
aof_rewrite 函数可以创建新的 AOF 文件，但是这个函数会进行大量的写入操作，所以调用这个函数的线程被长时间的阻塞，因为服务器使用单线程来处理命令请求；所以如果直接是服务器进程调用 AOF_REWRITE 函数的话，那么重写 AOF 期间，服务器将无法处理客户端发送来的命令请求；

  
Redis 不希望 AOF 重写会造成服务器无法处理请求，所以将 AOF 重写程序放到子进程（后台）里执行。这样处理的好处是：   
1）子进程进行 AOF 重写期间，主进程可以继续处理命令请求；  
2）子进程带有主进程的数据副本，使用子进程而不是线程，可以避免在锁的情况下，保证数据的安全性。

还有一个问题，可能重写的时候又有新的命令过来，造成信息不对等，所以 redis 设置了一个缓冲区，重写期间把命令放到重写缓冲区。  
 

 **总结**  
AOF 重写的目的是为了解决 AOF 文件体积膨胀的问题，使用更小的体积来保存数据库状态，整个重写过程基本上不影响 Redis 主进程处理命令请求；  
AOF 重写其实是一个有歧义的名字，实际上重写工作是针对数据库的当前状态来进行的，重写过程中不会读写、也不适用原来的 AOF 文件；  
AOF 可以由用户手动触发，也可以由服务器自动触发。

3.3、事件
------

**redis 服务器是一个事件驱动程序。**

需要处理两类事件：

1）文件事件：redis 是通过套接字与客户端或者其他服务器连接的，而文件事件就是服务器对套接字操作的抽象。

2）时间事件：服务器对一些定时操作的抽象。

### 3.3.1、文件事件

redis 基于 reactor 模式开发了自己的网络事件处理器，这个处理器被称作文件事件处理器，它使用 IO 多路复用程序来同时监听多个套接字， 并根据套接字目前执行的任务来为套接字关联不同的事件处理器，当被监听的套接字准备好执行连接应答（accept）、读取（read）、写入（write）、关闭（close）等操作时， 与操作相对应的文件事件就会产生， 这时文件事件处理器就会调用套接字之前关联好的事件处理器来处理这些事件。

**虽然文件事件处理器以单线程方式运行， 但通过使用 I/O 多路复用程序来监听多个套接字， 文件事件处理器既实现了高性能的网络通信模型， 又可以很好地与 Redis 服务器中其他同样以单线程方式运行的模块进行对接， 这保持了 Redis 内部单线程设计的简单性。**

文件事件处理器的构成：

![](https://img-blog.csdnimg.cn/20190611162420275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

I/O 多路复用程序负责监听多个套接字， 并向文件事件分派器传送那些产生了事件的套接字。

 I/O 多路复用程序会把所有产生事件的套接字放到一个队列， 以有序（sequentially）、同步（synchronously）、每次一个套接字的方式，向文件事件分派器传送套接字。

![](https://img-blog.csdnimg.cn/20190611162618893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

I/O 多路复用程序可以监听多个套接字的 `ae.h/AE_READABLE` 事件和 `ae.h/AE_WRITABLE` 事件

1）当套接字变得可读时（客户端对套接字执行 `write` 操作，或者执行 `close` 操作）， 或者有新的可应答（acceptable）套接字出现时（客户端对服务器的监听套接字执行 `connect` 操作）， 套接字产生 `AE_READABLE` 事件。

2）当套接字变得可写时（客户端对套接字执行 `read` 操作）， 套接字产生 `AE_WRITABLE` 事件。

**如果一个套接字又可读又可写的话， 那么服务器将先读套接字， 后写套接字。**

下面介绍各种处理器：

1）连接应答处理器：**服务器进行初始化时**， 程序会将连接应答处理器和服务器监听套接字的 `AE_READABLE` 事件关联， 当有客户端连接（`connect`）服务器监听套接字的时候， 套接字就会产生 `AE_READABLE` 事件， 引发连接应答处理器执行， 并执行相应的套接字应答操作。

2）命令请求处理器：**客户端连接到服务器后**， 服务器会将客户端套接字的 `AE_READABLE` 事件和命令请求处理器关联起来， 当客户端发送命令请求时， 套接字就会产生 `AE_READABLE` 事件， 引发命令请求处理器执行， 并执行相应的套接字读入操作

3）命令回复处理器：**服务器有命令回复需要传送给客户端**， 服务器会将客户端套接字的 `AE_WRITABLE` 事件和命令回复处理器关联起来， 当客户端准备好接收服务器传回的命令回复时， 就会产生 `AE_WRITABLE` 事件， 引发命令回复处理器执行， 并执行相应的套接字写入操作。

**一次完整的连接事件实例：**

![](https://img-blog.csdnimg.cn/20190611163753399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

### 3.3.2、时间事件

redis 时间事件可以分为两类：定时事件、周期性事件，他们的特点就像他们的名字一样。

而一个时间事件主要有三部分：

id：服务器为时间事件创建的全局唯一 id，按时间递增，越新的越大

when：unix 时间戳，记录到达时间

timeProc：时间事件处理器，是一个函数，时间事件到达时，服务器就会调用处理器来处理事件。

**目前版本的 redis 只使用周期性事件**

来看看实现：

服务器把所有时间事件放在一个链表中，每当时间事件执行器执行时，它就遍历链表，调用相应的事件处理器。

**但是注意：链表是无序的，不按 when 属性来排序，当时间事件执行器运行时，必须遍历整个链表。但是，无序链表并不影响时间事件处理器的性能，因为在目前版本中，redis 服务器只使用 serverCron 一个时间事件，就算在 benchmark 模式下也只有两个事件，服务器几乎是把链表退化成指针使用了。**

### 3.3.3、事件的调度和执行

文件事件和时间事件之间是合作关系， 服务器会轮流处理这两种事件，对两种事件的处理都是同步、有序、原子地进行的，处理事件的过程中也不会进行抢占，所以**时间事件的实际处理时间通常会比设定的到达时间晚一些**。

大概流程为：

是否关闭服务器？----> 等待文件事件产生 ----> 处理已经产生的文件事件 ----> 处理已经达到的时间事件 ----> 是否关闭服务器？........

3.4、客户端
-------

redis 服务器是典型的一对多服务器，通过使用由 IO 多路复用技术实现的文件事件处理器，redis 服务器使用了单线程单进程的方式来处理请求。

### 3.4.1 客户端的属性

*   **描述符**

客户端状态的 `fd` 属性记录了客户端正在使用的套接字描述符：

```
以下内容为摘抄
REDIS_MONITOR 标志表示客户端正在执行 MONITOR 命令。
 
REDIS_UNIX_SOCKET 标志表示服务器使用 UNIX 套接字来连接客户端。
 
REDIS_BLOCKED 标志表示客户端正在被 BRPOP 、 BLPOP 等命令阻塞。
 
REDIS_UNBLOCKED 标志表示客户端已经从 REDIS_BLOCKED 标志所表示的阻塞状态中脱离出来， 
不再阻塞。 REDIS_UNBLOCKED 标志只能在 REDIS_BLOCKED 标志已经打开的情况下使用。
 
REDIS_MULTI 标志表示客户端正在执行事务。
 
REDIS_DIRTY_CAS 标志表示事务使用 WATCH 命令监视的数据库键已经被修改， 
REDIS_DIRTY_EXEC 标志表示事务在命令入队时出现了错误， 
以上两个标志都表示事务的安全性已经被破坏， 只要这两个标记中的任意一个被打开， 
EXEC 命令必然会执行失败。 
这两个标志只能在客户端打开了 REDIS_MULTI 标志的情况下使用。
 
REDIS_CLOSE_ASAP 标志表示客户端的输出缓冲区大小超出了服务器允许的范围， 
服务器会在下一次执行 serverCron 函数时关闭这个客户端， 
以免服务器的稳定性受到这个客户端影响。 
积存在输出缓冲区中的所有内容会直接被释放， 不会返回给客户端。
 
REDIS_CLOSE_AFTER_REPLY 标志表示有用户对这个客户端执行了 CLIENT_KILL 命令， 
或者客户端发送给服务器的命令请求中包含了错误的协议内容。 
服务器会将客户端积存在输出缓冲区中的所有内容发送给客户端， 然后关闭客户端。
 
REDIS_ASKING 标志表示客户端向集群节点（运行在集群模式下的服务器）发送了 ASKING 命令。
 
REDIS_FORCE_AOF 标志强制服务器将当前执行的命令写入到 AOF 文件里面，
REDIS_FORCE_REPL 标志强制主服务器将当前执行的命令复制给所有从服务器。 
执行 PUBSUB 命令会使客户端打开 REDIS_FORCE_AOF 标志， 
执行 SCRIPT_LOAD 命令会使客户端打开 
REDIS_FORCE_AOF标志和 REDIS_FORCE_REPL 标志。
 
在主从服务器进行命令传播期间， 从服务器需要向主服务器发送 REPLICATION ACK 命令， 
在发送这个命令之前， 从服务器必须打开主服务器对应的客户端的 
REDIS_MASTER_FORCE_REPLY 标志， 否则发送操作会被拒绝执行。
```

*   伪客户端`fd` 值为 `-1` ： 伪客户端处理的命令请求来源于 AOF 文件或者 Lua 脚本， 而不是网络， 所以这种客户端不需要套接字连接。
*   普通客户端 `fd` 值为大于 `-1` 的整数： 普通客户端使用套接字来与服务器进行通讯， 所以服务器会用 `fd` 属性来记录客户端套接字的描述符。 

*   **标志**

客户端的标志属性 `flags` 记录了客户端的角色（role）， 以及客户端目前所处的状态：

```
typedef struct redisClient {
 
    // ...
 
    sds querybuf;
 
    // ...
 
} redisClient;
```

`flags` 属性的值可以是单个标志：

```
typedef struct redisClient {
 
    // ...
 
    robj **argv;
 
    int argc;
 
    // ...
 
} redisClient;
```

也可以是多个标志的二进制或， 比如：

```
redis> SET msg "hello world"
OK

redis> set msg "hello world"
OK

redis> SeT msg "hello world"
OK

redis> sEt msg "hello world"
OK
```

每个标志使用一个常量表示， 一部分标志记录了客户端的角色：

*   在主从服务器进行复制操作时， 主服务器会成为从服务器的客户端， 而从服务器也会成为主服务器的客户端。 `REDIS_MASTER` 标志表示客户端代表的是一个主服务器， `REDIS_SLAVE` 标志表示客户端代表的是一个从服务器。
*   `REDIS_LUA_CLIENT` 标识表示客户端是专门用于处理 Lua 脚本里面包含的 Redis 命令的伪客户端。

另一部分标志记录了客户端目前所处的状态：

```
// client 是指向客户端状态的指针
 
client->cmd->proc(client);
```

以上提到的所有标志都定义在 `redis.h` 文件里面。

**`PUBSUB` 命令和 `SCRIPT LOAD` 命令的特殊性**

**通常情况下， Redis 只会将那些对数据库进行了修改的命令写入到 AOF 文件， 并复制到各个从服务器**： 如果一个命令没有对数据库进行任何修改， 那么它就会被认为是只读命令， 这个命令不会被写入到 AOF 文件， 也不会被复制到从服务器。

以上规则适用于绝大部分 Redis 命令， 但 PUBSUB 命令和 SCRIPT_LOAD 命令是其中的例外。

PUBSUB 命令虽然没有修改数据库， 但 PUBSUB 命令向频道的所有订阅者发送消息这一行为带有副作用， 接收到消息的所有客户端的状态都会因为这个命令而改变。 因此， 服务器需要使用 `REDIS_FORCE_AOF` 标志， 强制将这个命令写入 AOF 文件， 这样在将来载入 AOF 文件时， 服务器就可以再次执行相同的 PUBSUB 命令， 并产生相同的副作用。

SCRIPT_LOAD 命令的与 PUBSUB 命令类似

### 3.4.2 输入缓冲区

客户端状态的输入缓冲区用于保存客户端发送的命令请求：

```
redis 127.0.0.1:6379> MULTI
OK
 
redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"
QUEUED
 
redis 127.0.0.1:6379> GET book-name
QUEUED
 
redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"
QUEUED
 
redis 127.0.0.1:6379> SMEMBERS tag
QUEUED
 
redis 127.0.0.1:6379> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
   2) "C++"
   3) "Programming"
```

 redisClient 实例：

![](https://img-blog.csdnimg.cn/20190611174647766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

### 3.4.3 命令相关

在服务器将客户端发送的命令请求保存到客户端状态的 `querybuf` 属性之后， 服务器将对命令请求的内容进行分析， 并将得出的命令参数以及命令参数的个数分别保存到客户端状态的 `argv` 属性和 `argc` 属性：

```
redis> MULTI
OK
```

`argv` 属性是一个数组， 数组中的每个项都是一个字符串对象： 其中 `argv[0]` 是要执行的命令， 而之后的其他项则是传给命令的参数。

`argc` 属性则负责记录 `argv` 数组的长度。

### 3.3.4 实现函数

当服务器从协议内容中分析并得出 `argv` 属性和 `argc` 属性的值之后， 服务器将根据项 `argv[0]` 的值， 在命令表中查找命令所对应的命令实现函数。

（命令表是一个字典，字典的键是一个 SDS 结构， 保存了命令的名字， 字典的值是命令所对应的 `redisCommand` 结构， 这个结构保存了命令的实现函数、 命令的标志、 命令应该给定的参数个数、 命令的总执行次数和总消耗时长等统计信息。）

### 3.3.5、输出缓冲区

执行命令所得的命令回复会被保存在客户端状态的输出缓冲区里面， 每个客户端都有两个输出缓冲区：

*   固定大小的缓冲区用于保存那些长度比较小的回复， 比如 `OK` 、简短的字符串值、整数值、错误回复，等等。
*   可变大小的缓冲区用于保存那些长度比较大的回复， 比如一个非常长的字符串值， 一个由很多项组成的列表， 一个包含了很多元素的集合， 等等。

### 3.3.6、其它

客户端状态的 `authenticated` 属性用于记录客户端是否通过了身份验证，还有几个和时间有关的属性，叙述是一件挺无聊的事情，不再写。

3.4、命令的执行过程
-----------

### 3.4.1 发送命令请求

当用户在客户端中键入一个命令请求时， 客户端会将这个命令请求转换成协议格式， 然后通过连接到服务器的套接字， 将协议格式的命令请求发送给服务器。

### 3.4.2 读取命令请求

当客户端与服务器之间的连接套接字因为客户端的写入而变得可读时， 服务器将调用命令请求处理器来执行以下操作：

1.  读取套接字中协议格式的命令请求， 并将其保存到客户端状态的输入缓冲区里面。
2.  对输入缓冲区中的命令请求进行分析， 提取出命令请求中包含的命令参数， 以及命令参数的个数， 然后分别将参数和参数个数保存到客户端状态的 `argv` 属性和 `argc` 属性里面。
3.  调用命令执行器， 执行客户端指定的命令。

### 3.4.3 命令执行器：查找命令实现

命令执行器要做的第一件事就是根据客户端状态的 `argv[0]` 参数， 在命令表（command table）中查找参数所指定的命令， 并将找到的命令保存到客户端状态的 `cmd` 属性里面。

命令表是一个字典， 字典的键是一个个命令名字，比如 `"set"` 、 `"get"` 、 `"del"` ，等等； 而字典的值是一个个 `redisCommand` 结构， 每个 `redisCommand` 结构记录了一个 Redis 命令的实现信息。

![](https://img-blog.csdnimg.cn/20191011182302116.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

命令名字的大小写不影响命令表的查找结果

因为命令表使用的是大小写无关的查找算法， 无论输入的命令名字是大写、小写或者混合大小写， 只要命令的名字是正确的， 就能找到相应的 redisCommand 结构。

比如说， 无论用户输入的命令名字是 "SET" 、 "set" 、 "SeT" 又或者 "sEt" ， 命令表返回的都是同一个 redisCommand 结构。

```
def MULTI():
 
    # 打开事务标识
    client.flags |= REDIS_MULTI
 
    # 返回 OK 回复
    replyOK()
```

### 3.4.4 命令执行器：执行预备操作

到目前为止， 服务器已经将执行命令所需的命令实现函数（保存在客户端状态的 `cmd` 属性）、参数（保存在客户端状态的 `argv` 属性）、参数个数（保存在客户端状态的 `argc` 属性）都收集齐了， 但是在真正执行命令之前， 程序还需要进行一些预备操作， 从而确保命令可以正确、顺利地被执行， 这些操作包括：

*   检查客户端状态的 `cmd` 指针是否指向 `NULL` ， 如果是的话， 那么说明用户输入的命令名字找不到相应的命令实现， 服务器不再执行后续步骤， 并向客户端返回一个错误。
*   根据客户端 `cmd` 属性指向的 `redisCommand` 结构的 `arity` 属性， 检查命令请求所给定的参数个数是否正确， 当参数个数不正确时， 不再执行后续步骤， 直接向客户端返回一个错误。 比如说， 如果 `redisCommand` 结构的 `arity` 属性的值为 `-3` ， 那么用户输入的命令参数个数必须大于等于 `3` 个才行。
*   检查客户端是否已经通过了身份验证， 未通过身份验证的客户端只能执行 AUTH 命令， 如果未通过身份验证的客户端试图执行除 AUTH 命令之外的其他命令， 那么服务器将向客户端返回一个错误。
*   如果服务器打开了 `maxmemory` 功能， 那么在执行命令之前， 先检查服务器的内存占用情况， 并在有需要时进行内存回收， 从而使得接下来的命令可以顺利执行。 如果内存回收失败， 那么不再执行后续步骤， 向客户端返回一个错误。
*   如果服务器上一次执行 BGSAVE 命令时出错， 并且服务器打开了 `stop-writes-on-bgsave-error` 功能， 而且服务器即将要执行的命令是一个写命令， 那么服务器将拒绝执行这个命令， 并向客户端返回一个错误。
*   如果客户端当前正在用 SUBSCRIBE 命令订阅频道， 或者正在用 PSUBSCRIBE 命令订阅模式， 那么服务器只会执行客户端发来的 SUBSCRIBE 、 PSUBSCRIBE 、 UNSUBSCRIBE 、 PUNSUBSCRIBE 四个命令， 其他别的命令都会被服务器拒绝。
*   如果服务器正在进行数据载入， 那么客户端发送的命令必须带有 `l` 标识（比如 INFO 、 SHUTDOWN 、 PUBLISH ，等等）才会被服务器执行， 其他别的命令都会被服务器拒绝。
*   如果服务器因为执行 Lua 脚本而超时并进入阻塞状态， 那么服务器只会执行客户端发来的 SHUTDOWN nosave 命令和 SCRIPT KILL 命令， 其他别的命令都会被服务器拒绝。
*   如果客户端正在执行事务， 那么服务器只会执行客户端发来的 EXEC 、 DISCARD 、 MULTI 、 WATCH 四个命令， 其他命令都会被放进事务队列中。
*   如果服务器打开了监视器功能， 那么服务器会将要执行的命令和参数等信息发送给监视器。

当完成了以上预备操作之后， 服务器就可以开始真正执行命令了。

### 3.4.5 命令执行器：调用命令的实现函数

在前面的操作中， 服务器已经将要执行命令的实现保存到了客户端状态的 `cmd` 属性里面， 并将命令的参数和参数个数分别保存到了客户端状态的 `argv` 属性和 `argc` 属性里面， 当服务器决定要执行命令时， 它只要执行以下语句就可以了：

```
redis> SET "name" "Practical Common Lisp"
OK
 
redis> GET "name"
"Practical Common Lisp"
 
redis> SET "author" "Peter Seibel"
OK
 
redis> GET "author"
"Peter Seibel"
```

因为执行命令所需的实际参数都已经保存到客户端状态的 `argv` 属性里面了， 所以命令的实现函数只需要一个指向客户端状态的指针作为参数即可。

### 3.4.6 命令执行器：执行后续工作

在执行完实现函数之后， 服务器还需要执行一些后续工作：

*   如果服务器开启了慢查询日志功能， 那么慢查询日志模块会检查是否需要为刚刚执行完的命令请求添加一条新的慢查询日志。
*   根据刚刚执行命令所耗费的时长， 更新被执行命令的 `redisCommand` 结构的 `milliseconds` 属性， 并将命令的 `redisCommand` 结构的 `calls` 计数器的值增一。
*   如果服务器开启了 AOF 持久化功能， 那么 AOF 持久化模块会将刚刚执行的命令请求写入到 AOF 缓冲区里面。
*   如果有其他从服务器正在复制当前这个服务器， 那么服务器会将刚刚执行的命令传播给所有从服务器。

当以上操作都执行完了之后， 服务器对于当前命令的执行到此就告一段落了， 之后服务器就可以继续从文件事件处理器中取出并处理下一个命令请求了。

### 3.4.7 将命令回复发送给客户端

前面说过， 命令实现函数会将命令回复保存到客户端的输出缓冲区里面， 并为客户端的套接字关联命令回复处理器， 当客户端套接字变为可写状态时， 服务器就会执行命令回复处理器， 将保存在客户端输出缓冲区中的命令回复发送给客户端。

当命令回复发送完毕之后， 回复处理器会清空客户端状态的输出缓冲区， 为处理下一个命令请求做好准备。

### 3.4.8 客户端接收并打印命令回复

当客户端接收到协议格式的命令回复之后， 它会将这些回复转换成人类可读的格式， 并打印给用户观看（假设使用的是 Redis 自带的 客户端）

3.5、事务
------

Redis 事务可以一次执行多个命令， 并且带有以下三个重要的保证：

*   批量操作在发送 EXEC 命令前被放入队列缓存。
*   收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
*   在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

一个事务从开始到执行会经历以下三个阶段：

*   开始事务。
*   命令入队。
*   执行事务。

以下是一个事务的例子， 它先以 **MULTI** 开始一个事务， 然后将多个命令入队到事务中， 最后由 **EXEC** 命令触发事务， 一并执行事务中的所有命令：

```
typedef struct redisClient {
 
    // ...
 
    // 事务状态
    multiState mstate;      /* MULTI/EXEC state */
 
    // ...
 
} redisClient;
```

详细介绍：

### 3.5.1 事务开始

MULTI 命令的执行标志着事务的开始：

```
typedef struct multiState {
 
    // 事务队列，FIFO 顺序
    multiCmd *commands;
 
    // 已入队命令计数
    int count;
 
} multiState;
```

MULTI 命令可以将执行该命令的客户端从非事务状态切换至事务状态， 这一切换是通过在客户端状态的 `flags` 属性中打开 `REDIS_MULTI` 标识来完成的， MULTI 命令的实现可以用以下伪代码来表示：

```
typedef struct multiCmd {
 
    // 参数
    robj **argv;
 
    // 参数数量
    int argc;
 
    // 命令指针
    struct redisCommand *cmd;
 
} multiCmd;
```

### 3.5.2 命令入队

当一个客户端处于非事务状态时， 这个客户端发送的命令会立即被服务器执行：

```
redis> MULTI
OK

redis> SET "name" "Practical Common Lisp"
QUEUED

redis> GET "name"
QUEUED

redis> SET "author" "Peter Seibel"
QUEUED

redis> GET "author"
QUEUED
```

与此不同的是， 当一个客户端切换到事务状态之后， 服务器会根据这个客户端发来的不同命令执行不同的操作：

*   如果客户端发送的命令为 EXEC 、 DISCARD 、 WATCH 、 MULTI 四个命令的其中一个， 那么服务器立即执行这个命令。
*   与此相反， 如果客户端发送的命令是 EXEC 、 DISCARD 、 WATCH 、 MULTI 四个命令以外的其他命令， 那么服务器并不立即执行这个命令， 而是将这个命令放入一个事务队列里面， 然后向客户端返回 `QUEUED` 回复。

### 3.5.3 事务队列

每个 Redis 客户端都有自己的事务状态， 这个事务状态保存在客户端状态的 `mstate` 属性里面：

```
def EXEC():
 
    # 创建空白的回复队列
    reply_queue = []
 
    # 遍历事务队列中的每个项
    # 读取命令的参数，参数的个数，以及要执行的命令
    for argv, argc, cmd in client.mstate.commands:
 
        # 执行命令，并取得命令的返回值
        reply = execute_command(cmd, argv, argc)
 
        # 将返回值追加到回复队列末尾
        reply_queue.append(reply)
 
    # 移除 REDIS_MULTI 标识，让客户端回到非事务状态
    client.flags &= ~REDIS_MULTI
 
    # 清空客户端的事务状态，包括：
    # 1）清零入队命令计数器
    # 2）释放事务队列
    client.mstate.count = 0
    release_transaction_queue(client.mstate.commands)
 
    # 将事务的执行结果返回给客户端
    send_reply_to_client(client, reply_queue)
```

事务状态包含一个事务队列， 以及一个已入队命令的计数器 （也可以说是事务队列的长度）：

```
/* Redis database representation. There are multiple databases identified
 * by integers from 0 (the default database) up to the max configured
 * database. The database number is the 'id' field in the structure. */
typedef struct redisDb {
    dict *dict;                 /* The keyspace for this DB 数据库键空间，保存数据库中所有的键值对*/
    dict *expires;              /* Timeout of keys with a timeout set 保存过期时间*/
    dict *blocking_keys;        /* Keys with clients waiting for data (BLPOP) */
    dict *ready_keys;           /* Blocked keys that received a PUSH 已经准备好数据的阻塞状态的key*/
    dict *watched_keys;         /* WATCHED keys for MULTI/EXEC CAS 事物模块，用于保存被WATCH命令所监控的键*/
     // 当内存不足时，Redis会根据LRU算法回收一部分键所占的空间，而该eviction_pool是一个长为16数组，保存可能被回收的键
    // eviction_pool中所有键按照idle空转时间，从小到大排序，每次回收空转时间最长的键
    struct evictionPoolEntry *eviction_pool;    /* Eviction pool of keys */
       // 数据库ID
    int id;                     /* Database ID */
     // 键的平均过期时间
    long long avg_ttl;          /* Average TTL, just for stats */
} redisDb;
```

事务队列是一个 `multiCmd` 类型的数组， 数组中的每个 `multiCmd` 结构都保存了一个已入队命令的相关信息， 包括指向命令实现函数的指针， 命令的参数， 以及参数的数量：

```
/* "Touch" a key, so that if this key is being WATCHed by some client the
 * next EXEC will fail. */
// Touch 一个 key，如果该key正在被监视，那么客户端会执行EXEC失败 
void touchWatchedKey(redisDb *db, robj *key) {
    list *clients;
    listIter li;
    listNode *ln;
    
    // 字典为空，没有任何键被监视
    if (dictSize(db->watched_keys) == 0) return;
    // 获取所有监视这个键的客户端 	
    clients = dictFetchValue(db->watched_keys, key);
    // 没找到返回
    if (!clients) return;
 
    /* Mark all the clients watching this key as CLIENT_DIRTY_CAS */
    /* Check if we are already watching for this key */
    // 遍历所有客户端，打开他们的 REDIS_DIRTY_CAS 标识
    listRewind(clients,&li);
    while((ln = listNext(&li))) {
        client *c = listNodeValue(ln);
        
         // 设置CLIENT_DIRTY_CAS标识
        c->flags |= CLIENT_DIRTY_CAS;
    }
}
```

事务队列以先进先出（FIFO）的方式保存入队的命令： 较先入队的命令会被放到数组的前面， 而较后入队的命令则会被放到数组的后面。

举个例子， 如果客户端执行以下命令：

```
struct redisServer {
 
    // ...
 
    // 保存所有频道的订阅关系
    dict *pubsub_channels;
 
    // ...
 
};
```

那么服务器将为客户端创建事务状态：

*   最先入队的 SET 命令被放在了事务队列的索引 `0` 位置上。
*   第二入队的 GET 命令被放在了事务队列的索引 `1` 位置上。
*   第三入队的另一个 SET 命令被放在了事务队列的索引 `2` 位置上。
*   最后入队的另一个 GET 命令被放在了事务队列的索引 `3` 位置上。

### 3.5.4 执行事务

当一个处于事务状态的客户端向服务器发送 EXEC 命令时， 这个 EXEC 命令将立即被服务器执行： 服务器会遍历这个客户端的事务队列， 执行队列中保存的所有命令， 最后将执行命令所得的结果全部返回给客户端。

EXEC 命令的实现原理可以用以下伪代码来描述：

```
def subscribe(*all_input_channels):
 
    # 遍历输入的所有频道
    for channel in all_input_channels:
 
        # 如果 channel 不存在于 pubsub_channels 字典（没有任何订阅者）
        # 那么在字典中添加 channel 键，并设置它的值为空链表
        if channel not in server.pubsub_channels:
            server.pubsub_channels[channel] = []
 
        # 将订阅者添加到频道所对应的链表的末尾
        server.pubsub_channels[channel].append(client)
```

### 3.5.5WATCH 命令的实现

WATCH 命令是一个乐观锁，它可以在 EXEC 命令执行之前，监视任意数量的数据库键，并在 EXEC 执行后，检查被监视的键是否至少有一个被修改，如果是，服务器拒绝执行事务，并向客户端返回代表事务执行失败的回复。

```
def unsubscribe(*all_input_channels):
 
    # 遍历要退订的所有频道
    for channel in all_input_channels:
 
        # 在订阅者链表中删除退订的客户端
        server.pubsub_channels[channel].remove(client)
 
        # 如果频道已经没有任何订阅者了（订阅者链表为空）
        # 那么将频道从字典中删除
        if len(server.pubsub_channels[channel]) == 0:
            server.pubsub_channels.remove(channel)
```

在每个代表数据库的 server`.h/redisDb` 结构类型中， 都保存了一个 `watched_keys` 字典， 字典的键是这个数据库被监视的键， 而字典的值则是一个链表， 链表中保存了所有监视这个键的客户端。比如说，以下字典就展示了一个 `watched_keys` 字典的例子：

![](https://img-blog.csdnimg.cn/2019101018255149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

每个 key 后挂着监视自己的客户端。

### 3.5.6 监控的触发

在任何对数据库键空间（key space）进行修改的命令成功执行之后 （比如 FLUSHDB 、 SET 、 DEL 、 LPUSH 、 SADD 、 ZREM ，诸如此类）， multi.c/touchWatchedKey 函数都会被调用 （修改命令会调用 signalModifiedKey() 函数来处理数据库中的键被修改的情况，该函数直接调用 touchWatchedKey() 函数）—— 它检查数据库的 watched_keys 字典， 看是否有客户端在监视已经被命令修改的键， 如果有的话， 程序将所有监视这个 / 这些被修改键的客户端的 REDIS_DIRTY_CAS 选项打开：  
 

```
struct redisServer {
 
    // ...
 
    // 保存所有频道的订阅关系
    list *pubsub_patterns;
 
    // ...
 
};
```

### 3.5.7 事务的 ACID 性质

 在传统的关系式数据库中，常常用 ACID 性质来检验事务功能的安全性。

redis 事物总是具有前三个性质。

**a）原子性 atomicity：redis 事务保证事务中的命令要么全部执行要不全部不执行。**

但是 redis 不同于传统关系型数据库，不支持回滚，即使出现了错误，事务也会继续执行下去。

因为 redis 作者认为，这种复杂的机制和 redis 追求的简单高效不符。并且，redis 事务错误通常是编程错误，只会出现在开发环境中，而不会出现在实际生产环境中，所以没必要支持回滚。

**b）一致性 consistency：redis 事务可以保证命令失败的情况下得以回滚，数据能恢复到没有执行之前的样子，是保证一致性的，除非 redis 进程意外终结。**

Redis 的一致性问题可以分为三部分来讨论：入队错误、执行错误、Redis 进程被终结。

**入队错误**

在命令入队的过程中，如果客户端向服务器发送了错误的命令，比如命令的参数数量不对，等等， 那么服务器将向客户端返回一个出错信息， 并且将客户端的事务状态设为 REDIS_DIRTY_EXEC 。  
因此，带有不正确入队命令的事务不会被执行，也不会影响数据库的一致性。

**执行错误**

如果命令在事务执行的过程中发生错误，比如说，对一个不同类型的 key 执行了错误的操作， 那么 Redis 只会将错误包含在事务的结果中， 这不会引起事务中断或整个失败，不会影响已执行事务命令的结果，也不会影响后面要执行的事务命令， 所以它对事务的一致性也没有影响。

**Redis 进程被终结**

如果 Redis 服务器进程在执行事务的过程中被其他进程终结，或者被管理员强制杀死，那么根据 Redis 所使用的持久化模式，可能有以下情况出现：

**内存模式：**如果 Redis 没有采取任何持久化机制，那么重启之后的数据库总是空白的，所以数据总是一致的。

**RDB 模式：**在执行事务时，Redis 不会中断事务去执行保存 RDB 的工作，只有在事务执行之后，保存 RDB 的工作才有可能开始。所以当 RDB 模式下的 Redis 服务器进程在事务中途被杀死时，事务内执行的命令，不管成功了多少，都不会被保存到 RDB 文件里。恢复数据库需要使用现有的 RDB 文件，而这个 RDB 文件的数据保存的是最近一次的数据库快照（snapshot），所以它的数据可能不是最新的，但只要 RDB 文件本身没有因为其他问题而出错，那么还原后的数据库就是一致的。

**AOF 模式：**因为保存 AOF 文件的工作在后台线程进行，所以即使是在事务执行的中途，保存 AOF 文件的工作也可以继续进行，因此，根据事务语句是否被写入并保存到 AOF 文件，有以下两种情况发生：

1）如果事务语句未写入到 AOF 文件，或 AOF 未被 SYNC 调用保存到磁盘，那么当进程被杀死之后，Redis 可以根据最近一次成功保存到磁盘的 AOF 文件来还原数据库，只要 AOF 文件本身没有因为其他问题而出错，那么还原后的数据库总是一致的，但其中的数据不一定是最新的。

2）如果事务的部分语句被写入到 AOF 文件，并且 AOF 文件被成功保存，那么不完整的事务执行信息就会遗留在 AOF 文件里，当重启 Redis 时，程序会检测到 AOF 文件并不完整，Redis 会退出，并报告错误。需要使用 redis-check-aof 工具将部分成功的事务命令移除之后，才能再次启动服务器。还原之后的数据总是一致的，而且数据也是最新的（直到事务执行之前为止）。  
 

**c）隔离性 Isolation：redis 事务是严格遵守隔离性的，原因是 redis 是单进程单线程模式，可以保证命令执行过程中不会被其他客户端命令打断。**

因为 redis 使用单线程执行事务，并且保证不会中断，所以肯定有隔离性。

**d）持久性 Durability：持久性是指：当一个事务执行完毕，结果已经保存在永久介质里，比如硬盘，所以即使服务器后来停机了，结果也不会丢失**

redis 事务是不保证持久性的，这是因为 redis 持久化策略中不管是 RDB 还是 AOF 都是异步执行的，不保证持久性是出于对性能的考虑。

###   
3.5.8 重点提炼

*   事务提供了一种将多个命令打包， 然后一次性、有序地执行的机制。
*   多个命令会被入队到事务队列中， 然后按先进先出（FIFO）的顺序执行。
*   事务在执行过程中不会被中断， 当事务队列中的所有命令都被执行完毕之后， 事务才会结束。
*   带有 WATCH 命令的事务会将客户端和被监视的键在数据库的 `watched_keys` 字典中进行关联， 当键被修改时， 程序会将所有监视被修改键的客户端的 `REDIS_DIRTY_CAS` 标志打开。
*   只有在客户端的 `REDIS_DIRTY_CAS` 标志未被打开时， 服务器才会执行客户端提交的事务， 否则的话， 服务器将拒绝执行客户端提交的事务。
*   Redis 的事务总是保证 ACID 中的原子性、一致性和隔离性， 当服务器运行在 AOF 持久化模式下， 并且 `appendfsync` 选项的值为 `always` 时， 事务也具有耐久性。

以上就是 Redis 客户端和服务器执行命令请求的整个过程了。

3.6、发布和订阅
---------

### 3.6.1 频道的订阅和退订

当一个客户端执行 SUBSCRIBE 命令， 订阅某个或某些频道的时候， 这个客户端与被订阅频道之间就建立起了一种订阅关系。

Redis 将所有频道的订阅关系都保存在服务器状态的 `pubsub_channels` 字典里面， 这个字典的键是某个被订阅的频道， 而键的值则是一个链表， 链表里面记录了所有订阅这个频道的客户端：

```
typedef struct pubsubPattern{
    //订阅模式的客户端
    redisClient *client;
    //被订阅的模式
    robj *pattern;
}pubsubPattern;
```

![](https://img-blog.csdnimg.cn/20191010145640311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

每当客户端执行 SUBSCRIBE 命令， 订阅某个或某些频道的时候， 服务器都会将客户端与被订阅的频道在 `pubsub_channels` 字典中进行关联。

根据频道是否已经有其他订阅者， 关联操作分为两种情况执行：

*   如果频道已经有其他订阅者， 那么它在 `pubsub_channels` 字典中必然有相应的订阅者链表， 程序唯一要做的就是将客户端添加到订阅者链表的末尾。
*   如果频道还未有任何订阅者， 那么它必然不存在于 `pubsub_channels` 字典， 程序首先要在 `pubsub_channels` 字典中为频道创建一个键， 并将这个键的值设置为空链表， 然后再将客户端添加到链表， 成为链表的第一个元素。

SUBSCRIBE 命令的实现可以用以下伪代码来描述：

```
def osubscribe(*all_input_patterns):
    #遍历所有输入的模式
    #记录被订阅的模式和对应的客户端
    pubsubPattern=create()
    pubsubPattern.client=client
    pubsubPattern.pattern=pattern
 
    #插入链表末尾
    server.pub_patterns.append(pubsubPattern)
```

UNSUBSCRIBE 命令的行为和 SUBSCRIBE 命令的行为正好相反 —— 当一个客户端退订某个或某些频道的时候， 服务器将从 `pubsub_channels` 中解除客户端与被退订频道之间的关联：

*   程序会根据被退订频道的名字， 在 `pubsub_channels` 字典中找到频道对应的订阅者链表， 然后从订阅者链表中删除退订客户端的信息。
*   如果删除退订客户端之后， 频道的订阅者链表变成了空链表， 那么说明这个频道已经没有任何订阅者了， 程序将从 `pubsub_channels` 字典中删除频道对应的键。

UNSUBSCRIBE 命令的实现可以用以下伪代码来描述：

```
def osubscribe(*all_input_patterns):
    #遍历所有退订的模式
    for pattern in all_input_patterns:
        #遍历每一个节点
        for pubsubPattern in server.pubsub_patterns:
            #如果客户端和模式都相同
            if client==pubsubPattern.client:
                if pattern==pubsubPattern.pattern:
                    #删除
                    server.pub_patterns.remove(pubsubPattern)
```

### 3.6.2 模式的订阅和退订

前面说过，服务器将所有频道的订阅关系保存起来，与此类似，服务器也将所有模式的订阅关系存在了 pubsub_Patterns 属性里。

```
$ redis-server /path/to/your/sentinel.conf --sentinel
```

pubsub_Patterns 属性是一个链表，每个结点是被订阅的模式，节点内记录了模式，节点内的 client 属性记录了订阅模式的客户端。

```
struct sentinelState {
 
    // 当前纪元，用于实现故障转移
    uint64_t current_epoch;
 
    // 保存了所有被这个 sentinel 监视的主服务器
    // 字典的键是主服务器的名字
    // 字典的值则是一个指向 sentinelRedisInstance 结构的指针
    dict *masters;
 
    // 是否进入了 TILT 模式？
    int tilt;
 
    // 目前正在执行的脚本的数量
    int running_scripts;
 
    // 进入 TILT 模式的时间
    mstime_t tilt_start_time;
 
    // 最后一次执行时间处理器的时间
    mstime_t previous_time;
 
    // 一个 FIFO 队列，包含了所有需要执行的用户脚本
    list *scripts_queue;
 
} sentinel;
```

每当客户端执行 PSUBSCRIBE 这个命令来订阅某个或某些模式时，服务器会对每个被订阅的模式执行下面的操作：

1）新建一个 pubsubPattern 结构，设置好两个属性

2）将新节点加到 pubsub_patterns 尾部

伪代码实现：

```
typedef struct sentinelRedisInstance {
 
    // 标识值，记录了实例的类型，以及该实例的当前状态
    int flags;
 
    // 实例的名字
    // 主服务器的名字由用户在配置文件中设置
    // 从服务器以及 Sentinel 的名字由 Sentinel 自动设置
    // 格式为 ip:port ，例如 "127.0.0.1:26379"
    char *name;
 
    // 实例的运行 ID
    char *runid;
 
    // 配置纪元，用于实现故障转移
    uint64_t config_epoch;
 
    // 实例的地址
    sentinelAddr *addr;
 
    // SENTINEL down-after-milliseconds 选项设定的值
    // 实例无响应多少毫秒之后才会被判断为主观下线（subjectively down）
    mstime_t down_after_period;
 
    // SENTINEL monitor <master-name> <IP> <port> <quorum> 选项中的 quorum 参数
    // 判断这个实例为客观下线（objectively down）所需的支持投票数量
    int quorum;
 
    // SENTINEL parallel-syncs <master-name> <number> 选项的值
    // 在执行故障转移操作时，可以同时对新的主服务器进行同步的从服务器数量
    int parallel_syncs;
 
    // SENTINEL failover-timeout <master-name> <ms> 选项的值
    // 刷新故障迁移状态的最大时限
    mstime_t failover_timeout;
 
    // ...
 
} sentinelRedisInstance;
```

模式退订命令 PUNSUBSCRIBE 是 PSUBSCRIBE 的反操作

服务器将找到并删除那些被退订的模式

伪代码如下：（我想吐槽一下这样时间复杂度。。。没有更好的办法吗？）

```
为什么有两个连接？
 
在 Redis 目前的发布与订阅功能中， 被发送的信息都不会保存在
 Redis 服务器里面， 如果在信息发送时， 想要接收信息的客户
端不在线或者断线， 那么这个客户端就会丢失这条信息。
 
因此， 为了不丢失 __sentinel__:hello 频道的任何信息， 
Sentinel 必须专门用一个订阅连接来接收该频道的信息。
 
而另一方面， 除了订阅频道之外， Sentinel 还又必须向主服务
器发送命令， 以此来与主服务器进行通讯， 所以 Sentinel 还
必须向主服务器创建命令连接。
 
并且因为 Sentinel 需要与多个实例创建多个网络连接， 所以
 Sentinel 使用的是异步连接。
```

### 3.6.3、发送消息

当一个客户端执行 PUBLISH<channel> <message > 命令将消息发送给频道时，服务器需要：

1）把消息发送给所有本频道的订阅者

具体做法就是去 pubsub_channels 字典找到本频道的链表，也就是订阅名单，然后发消息

2）将消息发给，包含本频道的所有模式中的所有订阅者

具体做法就是去 pubsub_patterns 查找包含本频道的模式，并且把消息发送给订阅它们的客户端。

### 3.6.4、查看订阅信息

redis2.8 新增三个命令，用来查看频道和模式的相关信息。

PUBLISH CHANNELS[pattern] 用于返回服务器当前被订阅的频道，pattern 可写可不写，不写就查看所有，否则查看与 pattern 匹配的对应频道

这个子命令是通过遍历 pubsub_channels 字典实现的。

PUBLISH NUMSUB[CHANNEL-1 CHANNEL-2.....] 返回这些频道的订阅者数量

这个子命令是通过遍历 pubsub_channels 字典，查看对应链表长度实现的。

PUBLISH NUMPAT 返回被订阅模式数量

这个子命令是通过返回 pubsub_patterns 的长度实现的。

总而言之，PUBSUB 命令的三个子命令都是通过读取 `pubsub_channels` 字典和 `pubsub_patterns` 链表中的信息来实现的。

四、多机实现
======

4.1、旧版复制
--------

Redis 的复制功能分为同步（sync）和命令传播（command propagate）两个操作：

*   同步操作用于将从服务器的数据库状态更新至主服务器当前所处的数据库状态。
*   命令传播操作用于在主服务器的数据库状态被修改， 导致主从服务器的数据库状态出现不一致时， 让主从服务器的数据库重新回到一致状态。

### 同步

当客户端向从服务器发送 SLAVEOF 命令， 要求从服务器复制主服务器时， 从服务器首先需要执行同步操作， 也即是， 将从服务器的数据库状态更新至主服务器当前所处的数据库状态。

从服务器对主服务器的同步操作需要通过向主服务器发送 SYNC 命令来完成， 以下是 SYNC 命令的执行步骤：

1.  从服务器向主服务器发送 SYNC 命令。
2.  收到 SYNC 命令的主服务器执行 BGSAVE 命令， 在后台生成一个 RDB 文件， 并使用一个缓冲区记录从现在开始执行的所有写命令。
3.  当主服务器的 BGSAVE 命令执行完毕时， 主服务器会将 BGSAVE 命令生成的 RDB 文件发送给从服务器， 从服务器接收并载入这个 RDB 文件， 将自己的数据库状态更新至主服务器执行 BGSAVE 命令时的数据库状态。
4.  主服务器将记录在缓冲区里面的所有写命令发送给从服务器， 从服务器执行这些写命令， 将自己的数据库状态更新至主服务器数据库当前所处的状态。

![](https://img-blog.csdnimg.cn/20191015212220865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)。

### 命令传播

在同步操作执行完毕之后， 主从服务器两者的数据库将达到一致状态， 但这种一致并不是一成不变的 —— 每当主服务器执行客户端发送的写命令时， 主服务器的数据库就有可能会被修改， 并导致主从服务器状态不再一致。

举个例子， 假设一个主服务器和一个从服务器刚刚完成同步操作， 它们的数据库都保存了相同的五个键 `k1` 至 `k5`

![](https://img-blog.csdnimg.cn/20191015212341838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

如果这时， 客户端向主服务器发送命令 `DEL k3` ， 那么主服务器在执行完这个 DEL 命令之后， 主从服务器的数据库将出现不一致： 主服务器的数据库已经不再包含键 `k3` ， 但这个键却仍然包含在从服务器的数据库里面

![](https://img-blog.csdnimg.cn/2019101521240477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

为了让主从服务器再次回到一致状态， 主服务器需要对从服务器执行命令传播操作： 主服务器会将自己执行的写命令 —— 也即是造成主从服务器不一致的那条写命令 —— 发送给从服务器执行， 当从服务器执行了相同的写命令之后， 主从服务器将再次回到一致状态。

![](https://img-blog.csdnimg.cn/20191015212602835.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

### 缺陷

![](https://img-blog.csdnimg.cn/20191015213003244.png)

![](https://img-blog.csdnimg.cn/20191015213253233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)。

　其中可以明显看出重新连接主服务器之后，**SYNC 命令创建包含 k1-k10089 的 RDB 文件。而事实上只需要再同步断线后的 k10087-k10089 即可。SYNC 的 “全同步” 对于从服务来说是不必要的。**

           SYNC 命令非常消耗资源，原因有三点：

　　　　**1）主服务器执行 BGSAVE 命令生成 RDB 文件，这个生成过程会大量消耗主服务器资源（CPU、内存和磁盘 I/O 资源）**

 **2）主服务器需要将自己生成的 RBD 文件发送给从从服务器，这个发送操作会消耗主从服务器大量的网络资源（带宽与流量）**

 **3）接收到 RDB 文件你的从服务器需要载入 RDB 文件，载入期间从服务器会因为阻塞而导致没办法处理命令请求。**

4.2 新版复制
--------

sync 虽然解决了数据同步问题，但是在数据量比较大情况下，从库断线从来依然采用全量复制机制，无论是从数据恢复、宽带占用来说，sync 所带来的问题还是很多的。于是 redis 从 2.8 开始，引入新的命令 psync。

psync 有两种模式：完整重同步和部分重同步。

部分重同步主要依赖三个方面来实现，依次介绍。

### **offset（复制偏移量）：**

主库和从库分别各自维护一个复制偏移量（可以使用 info replication 查看），用于标识自己复制的情况：

在主库中代表主节点向从节点传递的字节数，在从库中代表从库同步的字节数。

每当主库向从节点发送 N 个字节数据时，主节点的 offset 增加 N

从库每收到主节点传来的 N 个字节数据时，从库的 offset 增加 N。

因此 offset 总是不断增大，这也是判断主从数据是否同步的标志，若主从的 offset 相同则表示数据同步量，不通则表示数据不同步。

### **replication backlog buffer（复制积压缓冲区）：**

 复制积压缓冲区是一个固定长度的 FIFO 队列，大小由配置参数 repl-backlog-size 指定，默认大小 1MB。

需要注意的是该缓冲区由 master 维护并且有且只有一个，所有 slave 共享此缓冲区，其作用在于备份最近主库发送给从库的数据。

　　**在主从命令传播阶段，主节点除了将写命令发送给从节点外，还会发送一份到复制积压缓冲区，作为写命令的备份。**

除了存储最近的写命令，复制积压缓冲区中还存储了每个字节相应的复制偏移量，由于复制积压缓冲区固定大小先进先出的队列，所以它总是保存的是最近 redis 执行的命令。

![](https://img-blog.csdnimg.cn/2019102309101993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

所以，重连服务器后，从服务器会发送自己的复制偏移量 offset 给主服务器，

如果 offset 偏移量之后的数据仍然存在于复制挤压缓冲区，就执行部分重同步操作。

相反，执行完整重同步操作。

### **run_id(服务器运行的唯一 ID)** 

　　每个 redis 实例在启动时候，都会随机生成一个长度为 40 的唯一字符串来标识当前运行的 redis 节点，查看此 id 可通过命令 info server 查看。

　　当主从复制在初次复制时，主节点将自己的 runid 发送给从节点，从节点将这个 runid 保存起来, 当断线重连时，从节点会将这个 runid 发送给主节点。主节点根据 runid 判断能否进行部分复制：

*   如果从节点保存的 runid 与主节点现在的 runid 相同，说明主从节点之前同步过，主节点会更具 offset 偏移量之后的数据判断是否执行部分复制，如果 offset 偏移量之后的数据仍然都在复制积压缓冲区里，则执行部分复制，否则执行全量复制；
*   如果从节点保存的 runid 与主节点现在的 runid 不同，说明从节点在断线前同步的 redis 节点并不是当前的主节点，只能进行全量复制;

### psync 流程：

![](https://img-blog.csdnimg.cn/20191023092925540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

### 复制

客户端向服务器端发送：SLAVEOF

**1、设置主服务器的地址和端口**

**存到 masterhost 和 mastterport 两个属性里之后，向客户端发送 ok，然后开始复制工作。**

**2、建立套接字链接**

**从服务器根据命令设置的地址和端口，创建链接，并且为这个套接字创建一个专门处理复制工作的文件事件处理器。**

**主服务器也会为套接字创建相应的客户端状态，并且把从服务器当作一个客户端来对待。**

**3、发送 ping 命令（检查）**

**检查套接字状态是否正常**

**检查主服务器是否能正确处理请求。（如果不能，就重连）**

**4、身份认证**

**5、发送端口信息**

**从服务器向主服务器发送信息，主服务器记录。**

**6、同步**

**从服务器向主服务器发送 psync 命令。（主服务器也成为从服务器的客户端，因为主服务器会发送写命令给从服务器）**

**7、命令传播**

**完成同步后，进入传播阶段，主服务器一直发送写命令，从服务器一直接受，保证和主服务器一致。**

### **心跳检测**

默认一秒一次，从服务器向主服务器发送命令：REPLCONF ACK <offset>

三个作用：

检测网络连接状态：如果主服务器一秒没收到命令，就说明出问题了

辅助实现 min-slaves 配置：min-slaves-to-write 3   min-slaves-max-log 10: 当从服务器小于 3 个或延迟都大于 10，主服务器拒绝写命令。

检测命令丢失：如果命令丢失，主服务器会发现偏移量不一样，然后它就会根据偏移量，去积压缓冲区找到缺少的数据并发给从服务器。

4.3、哨兵
------

### **4.3.1 什么是哨兵机制**

Redis 的哨兵 (sentinel) 系统用于管理 / 多个 Redis 服务器, 该系统执行以下三个任务:

·        监控: 哨兵 (sentinel) 会不断地检查你的 Master 和 Slave 是否运作正常。

·        提醒: 当被监控的某个 Redis 出现问题时, 哨兵 (sentinel) 可以通过 API 向管理员或者其他应用程序发送通知。

·        自动故障迁移: 当一个 Master 不能正常工作时，哨兵 (sentinel) 会开始一次自动故障迁移操作, 它会将失效 Master 的其中一个 Slave 升级为新的 Master, 并让失效 Master 的其他 Slave 改为复制新的 Master; 当客户端试图连接失效的 Master 时, 集群也会向客户端返回新 Master 的地址, 使得集群可以使用 Master 代替失效 Master。

例如下图所示：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvOTA3NTk2LzIwMTgwNS85MDc1OTYtMjAxODA1MDcxOTA5MzI0NzctMjA1Mzg4Nzg5NS5wbmc?x-oss-process=image/format,png)

在 Server1 掉线后：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvOTA3NTk2LzIwMTgwNS85MDc1OTYtMjAxODA1MDcxOTA5NDcwNTMtMTExNDUyNjAzMC5wbmc?x-oss-process=image/format,png)

升级 Server2 为新的主服务器：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvOTA3NTk2LzIwMTgwNS85MDc1OTYtMjAxODA1MDcxOTEwMDIwOTAtODE3MjM1MzQucG5n?x-oss-process=image/format,png)

### **4.3.2、哨兵模式修改配置**

实现步骤:

1. 拷贝到 etc 目录

cp sentinel.conf  /usr/local/redis/etc

2. 修改 sentinel.conf 配置文件

sentinel monitor mymast  192.168.110.133 6379 1  #主节点 名称 IP 端口号 选举次数

sentinel auth-pass mymaster 123456 

3. 修改心跳检测 5000 毫秒

sentinel down-after-milliseconds mymaster 5000

4.sentinel parallel-syncs mymaster 2 --- 做多多少合格节点

5. 启动哨兵模式

./redis-server /usr/local/redis/etc/sentinel.conf --sentinel &

1）Sentinel(哨兵) 进程是用于监控 Redis 集群中 Master 主服务器工作的状态

2）在 Master 主服务器发生故障的时候，可以实现 Master 和 Slave 服务器的切换，保证系统的高可用（High Availability）

**工作方式**

1）每个 Sentinel（哨兵）进程以每秒钟一次的频率向整个集群中的 Master 主服务器，Slave 从服务器以及其他 Sentinel（哨兵）进程发送一个 PING 命令。

2. 如果一个实例（instance）距离最后一次有效回复 PING 命令的时间超过 down-after-milliseconds 选项所指定的值， 则这个实例会被 Sentinel（哨兵）进程标记为主观下线。

3. 如果一个 Master 主服务器被标记为主观下线，则正在监视这个 Master 主服务器的所有 Sentinel（哨兵）进程要以每秒一次的频率确认 Master 主服务器的确进入了主观下线状态。

4. 当有足够数量的 Sentinel（哨兵）进程（大于等于配置文件指定的值）在指定的时间范围内确认 Master 主服务器进入了主观下线状态， 则 Master 主服务器会被标记为客观下线（ODOWN）。

5. 在一般情况下， 每个 Sentinel（哨兵）进程会以每 10 秒一次的频率向集群中的所有 Master 主服务器、Slave 从服务器发送 INFO 命令。

6. 当 Master 主服务器被 Sentinel（哨兵）进程标记为客观下线时，Sentinel（哨兵）进程向下线的 Master 主服务器的所有 Slave 从服务器发送 INFO 命令的频率会从 10 秒一次改为每秒一次。

7. 若没有足够数量的 Sentinel（哨兵）进程同意 Master 主服务器下线， Master 主服务器的客观下线状态就会被移除。若 Master 主服务器重新向 Sentinel（哨兵）进程发送 PING 命令返回有效回复，Master 主服务器的主观下线状态就会被移除。

哨兵 (sentinel) 的一些设计思路和 zookeeper 非常类似

我们从启动并初始化说起

### 4.3.3 启动并初始化 Sentinel

启动一个 Sentinel 可以使用命令：

```
package com.now.community.community.util;
 
public class RedisKeyUtil {
 
    private static final String SPLIT = ":";
    private static final String PREFIX_ENTITY_LIKE = "like:entity";
    private static final String PREFIX_USER_LIKE = "like:user";
 
    // 某个实体的赞
    // like:entity:entityType:entityId -> set(userId)
    public static String getEntityLikeKey(int entityType, int entityId) {
        return PREFIX_ENTITY_LIKE + SPLIT + entityType + SPLIT + entityId;
    }
 
    // 某个用户的赞
    // like:user:userId -> int
    public static String getUserLikeKey(int userId) {
        return PREFIX_USER_LIKE + SPLIT + userId;
    }
}
```

或者命令：

```
// 点赞
    public void like(int userId, int entityType, int entityId, int entityUserId) {
        redisTemplate.execute(new SessionCallback() {
            @Override
            public Object execute(RedisOperations operations) throws DataAccessException {
                String entityLikeKey = RedisKeyUtil.getEntityLikeKey(entityType, entityId);
                boolean isMember = operations.opsForSet().isMember(entityLikeKey, userId);
                operations.multi();
                if (isMember) {
                    operations.opsForSet().remove(entityLikeKey, userId);
                } else {
                    operations.opsForSet().add(entityLikeKey, userId);
                }
                return operations.exec();
            }
        });
    }
```

当一个 Sentinel 启动时， 它需要执行以下步骤：

**初始化服务器。**

首先， 因为 Sentinel 本质上只是一个运行在特殊模式下的 Redis 服务器， 所以启动 Sentinel 的第一步， 就是初始化一个普通的 Redis 服务器.

不过， 因为 Sentinel 执行的工作和普通 Redis 服务器执行的工作不同， 所以 Sentinel 的初始化过程和普通 Redis 服务器的初始化过程并不完全相同。

比如说， 普通服务器在初始化时会通过载入 RDB 文件或者 AOF 文件来还原数据库状态， 但是因为 Sentinel 并不使用数据库， 所以初始化 Sentinel 时就不会载入 RDB 文件或者 AOF 文件。

**将普通 Redis 服务器使用的代码替换成 Sentinel 专用代码。**

第二个步骤就是将一部分普通 Redis 服务器使用的代码替换成 Sentinel 专用代码。

比如说， 普通 Redis 服务器使用 `redis.h/REDIS_SERVERPORT` 常量的值作为服务器端口：

```
// 查询某实体点赞的数量
    public long findEntityLikeCount(int entityType, int entityId) {
        String entityLikeKey = RedisKeyUtil.getEntityLikeKey(entityType, entityId);
        return redisTemplate.opsForSet().size(entityLikeKey);
    }
 
    // 查询某人对某实体的点赞状态
    public int findEntityLikeStatus(int userId, int entityType, int entityId) {
        String entityLikeKey = RedisKeyUtil.getEntityLikeKey(entityType, entityId);
        return redisTemplate.opsForSet().isMember(entityLikeKey, userId) ? 1 : 0;
    }
```

而 Sentinel 则使用 `sentinel.c/REDIS_SENTINEL_PORT` 常量的值作为服务器端口：

```
@RequestMapping(path = "/like", method = RequestMethod.POST)
    @ResponseBody
    public String like(int entityType, int entityId,int entityUserId,int postId) {
        User user = hostHolder.getUser();
 
        // 点赞
        likeService.like(user.getId(), entityType, entityId,entityUserId);
 
        // 数量
        long likeCount = likeService.findEntityLikeCount(entityType, entityId);
        // 状态
        int likeStatus = likeService.findEntityLikeStatus(user.getId(), entityType, entityId);
        // 返回的结果
        Map<String, Object> map = new HashMap<>();
        map.put("likeCount", likeCount);
        map.put("likeStatus", likeStatus);
        return CommunityUtil.getJSONString(0, null, map);
    }
```

为什么在 Sentinel 模式下， Redis 服务器不能执行诸如 SET 、 DBSIZE 、 EVAL 等等这些命令 —— 因为服务器根本没有在命令表中载入这些命令。

**初始化 Sentinel 状态。**

在应用了 Sentinel 的专用代码之后， 接下来， 服务器会初始化一个 `sentinel.c/sentinelState` 结构（后面简称 “Sentinel 状态”）， 这个结构保存了服务器中所有和 Sentinel 功能有关的状态 （服务器的一般状态仍然由 `redis.h/redisServer` 结构保存）：

```
package com.now.community.community.service;
 
import com.now.community.community.entity.User;
import com.now.community.community.util.CommunityConstant;
import com.now.community.community.util.RedisKeyUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataAccessException;
import org.springframework.data.redis.core.RedisOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.SessionCallback;
import org.springframework.stereotype.Service;
 
import java.util.*;
 
@Service
public class FollowService implements CommunityConstant {
 
    @Autowired
    private RedisTemplate redisTemplate;
 
    @Autowired
    private UserService userService;
    public void follow(int userId, int entityType, int entityId) {
        redisTemplate.execute(new SessionCallback() {
            @Override
            public Object execute(RedisOperations operations) throws DataAccessException {
                String followeeKey = RedisKeyUtil.getFolloweeKey(userId, entityType);
                String followerKey = RedisKeyUtil.getFollowerKey(entityType, entityId);
 
                operations.multi();
 
                operations.opsForZSet().add(followeeKey, entityId, System.currentTimeMillis());
                operations.opsForZSet().add(followerKey, userId, System.currentTimeMillis());
 
                return operations.exec();
            }
        });
    }
 
    public void unfollow(int userId, int entityType, int entityId) {
        redisTemplate.execute(new SessionCallback() {
            @Override
            public Object execute(RedisOperations operations) throws DataAccessException {
                String followeeKey = RedisKeyUtil.getFolloweeKey(userId, entityType);
                String followerKey = RedisKeyUtil.getFollowerKey(entityType, entityId);
 
                operations.multi();
 
                operations.opsForZSet().remove(followeeKey, entityId);
                operations.opsForZSet().remove(followerKey, userId);
 
                return operations.exec();
            }
        });
    }
 
    // 查询关注的实体的数量
    public long findFolloweeCount(int userId, int entityType) {
        String followeeKey = RedisKeyUtil.getFolloweeKey(userId, entityType);
        return redisTemplate.opsForZSet().zCard(followeeKey);
    }
 
    // 查询实体的粉丝的数量
    public long findFollowerCount(int entityType, int entityId) {
        String followerKey = RedisKeyUtil.getFollowerKey(entityType, entityId);
        return redisTemplate.opsForZSet().zCard(followerKey);
    }
 
    // 查询当前用户是否已关注该实体
    public boolean hasFollowed(int userId, int entityType, int entityId) {
        String followeeKey = RedisKeyUtil.getFolloweeKey(userId, entityType);
        return redisTemplate.opsForZSet().score(followeeKey, entityId) != null;
    }
 
    // 查询某用户关注的人
    public List<Map<String, Object>> findFollowees(int userId, int offset, int limit) {
        String followeeKey = RedisKeyUtil.getFolloweeKey(userId, ENTITY_TYPE_USER);
        Set<Integer> targetIds = redisTemplate.opsForZSet().reverseRange(followeeKey, offset, offset + limit - 1);
 
        if (targetIds == null) {
            return null;
        }
 
        List<Map<String, Object>> list = new ArrayList<>();
        for (Integer targetId : targetIds) {
            Map<String, Object> map = new HashMap<>();
            User user = userService.findUserById(targetId);
            map.put("user", user);
            Double score = redisTemplate.opsForZSet().score(followeeKey, targetId);
            map.put("followTime", new Date(score.longValue()));
            list.add(map);
        }
 
        return list;
    }
 
    // 查询某用户的粉丝
    public List<Map<String, Object>> findFollowers(int userId, int offset, int limit) {
        String followerKey = RedisKeyUtil.getFollowerKey(ENTITY_TYPE_USER, userId);
        Set<Integer> targetIds = redisTemplate.opsForZSet().reverseRange(followerKey, offset, offset + limit - 1);
 
        if (targetIds == null) {
            return null;
        }
 
        List<Map<String, Object>> list = new ArrayList<>();
        for (Integer targetId : targetIds) {
            Map<String, Object> map = new HashMap<>();
            User user = userService.findUserById(targetId);
            map.put("user", user);
            Double score = redisTemplate.opsForZSet().score(followerKey, targetId);
            map.put("followTime", new Date(score.longValue()));
            list.add(map);
        }
 
        return list;
    }
}
```

**初始化 Sentinel 状态的 `masters` 属性**

Sentinel 状态中的 `masters` 字典记录了所有被 Sentinel 监视的主服务器的相关信息：

*   字典的键是被监视主服务器的名字。
*   而字典的值则是被监视主服务器对应的 `sentinel.c/sentinelRedisInstance` 结构。

每个 `sentinelRedisInstance` 结构代表一个被 Sentinel 监视的 Redis 服务器实例（instance）， 这个实例可以是主服务器、从服务器、或者另外一个 Sentinel 。

实例结构包含的属性非常多， 以下代码展示了一部分属性

```
const REDIS_TB_NAME='user:actId';		//表名
const REDIS_SEP=":";		//命名分隔符
const REDIS_FIELDS="username|regtime";   //表字段名称
const REDIS_FIELD_RANK="rank";      //排行
const REDIS_FIELD_ID="id";   	//表的自增ID
//插入排行榜数据
for($i=0;$i<RANK_REC_NUM;$i++)   //填充数据
{
	$redis_increase_id=$redis->get(REDIS_TB_NAME.REDIS_SEP.REDIS_FIELD_ID);		
	//事务机制,插入用户信息及排行信息，自增id
	$ret=$redis->multi()  //开始事务
	->hMset(REDIS_TB_NAME.REDIS_SEP.$redis_increase_id,array($fields[0]=>"username".$redis_increase_id, $fields[1]=>(time()+intval(rand(0,1000)))))   //username   用户名 //regtime  注册时间
	->Zadd(REDIS_TB_NAME.REDIS_SEP.REDIS_FIELD_RANK,intval(rand(1,100)),$redis_increase_id)     //插入排行
	->incr(REDIS_TB_NAME.REDIS_SEP.REDIS_FIELD_ID)		//自增id
	->exec();  //执行事务
	
	if($ret==false)  //插入失败，重新插入
	{
		$i--;
	}
}	
echo "插入".$i."条记录成功<br>";
```

**创建连向主服务器的网络连接。**

 Sentinel 将成为主服务器的客户端， 它可以向主服务器发送命令， 并从命令回复中获取相关的信息。

对于每个被 Sentinel 监视的主服务器来说， Sentinel 会创建两个连向主服务器的异步网络连接：

*   一个是命令连接， 这个连接专门用于向主服务器发送命令， 并接收命令回复。
*   另一个是订阅连接， 这个连接专门用于订阅主服务器的 `__sentinel__:hello` 频道。

```
<table>
<thead>
<tr style="font-size:bold;color:red"><td>名次</td><td>分数</td><td>姓名</td><td>注册时间</td></tr>
</thead>
<tbody>
<?php
	const REDIS_FIELDS="username|regtime";   //表字段名称
 
	$fields=explode('|',REDIS_FIELDS);
	foreach($rank as $k=>$v)
	{
		//echo REDIS_TB_NAME.REDIS_SEP.$k.REDIS_SEP.$fields[0];
		echo "<tr><td>$i</td><td>$v</td><td>".$redis->hget(REDIS_TB_NAME.REDIS_SEP.$k,$fields[0])."</td><td>".date("Y-m-d H:i:s",$redis->hget(REDIS_TB_NAME.REDIS_SEP.$k,$fields[1]))."</td></tr>";
		$i++;
	}
}
?>
</tbody>
</table>
```

接下来介绍 Sentinel 如何通过命令连接和订阅连接与被监视主服务器进行通讯。

### 4.3.4、获取服务器信息

sentinel 默认每十秒钟发送一次 INFO 命令给主服务器，并获取信息：

1）关于主服务器本身的信息

2）主服务器属下所有从服务器信息

sentinel 发现主服务器有新的从服务器时，会创建相应的实例结构和命令连接，订阅连接

### 4.3.5、给服务器发送消息

### **4.3.6、主观下线**

指的是单个 Sentinel 实例对服务器做出的下线判断，即单个 sentinel 认为某个服务下线（有可能是接收不到订阅，之间的网络不通等等原因）。  
如果服务器在 down-after-milliseconds 给定的毫秒数之内， 没有返回 Sentinel 发送的 PING 命令的回复， 或者返回一个错误， 那么 Sentinel 将这个服务器标记为主观下线（SDOWN ）。  
sentinel 会以每秒一次的频率向所有与其建立了命令连接的实例（master，从服务，其他 sentinel）发 ping 命令，通过判断 ping 回复是有效回复，还是无效回复来判断实例时候在线（对该 sentinel 来说是 “主观在线”）。  
sentinel 配置文件中的 down-after-milliseconds 设置了判断主观下线的时间长度，如果实例在 down-after-milliseconds 毫秒内，返回的都是无效回复，那么 sentinel 回认为该实例已（主观）下线，修改其 flags 状态为 SRI_S_DOWN。如果多个 sentinel 监视一个服务，有可能存在多个 sentinel 的 down-after-milliseconds 配置不同，这个在实际生产中要注意。

### **4.3.7、客观下线**

  
客观下线（Objectively Down， 简称 ODOWN）指的是多个 Sentinel 实例在对同一个服务器做出 SDOWN 判断， 并且通过 SENTINEL is-master-down-by-addr 命令互相交流之后， 得出的服务器下线判断，然后开启 failover。  
客观下线就是说只有**在足够数量的 Sentinel 都将一个服务器标记为主观下线之后， 服务器才会被标记为客观下线**（ODOWN）。  
只有当 master 被认定为客观下线时，才会发生故障迁移。  
当 sentinel 监视的某个服务主观下线后，sentinel 会询问其它监视该服务的 sentinel，看它们是否也认为该服务主观下线，接收到足够数量（这个值可以配置）的 sentinel 判断为主观下线，既任务该服务客观下线，并对其做故障转移操作。  
sentinel 通过发送 SENTINEL is-master-down-by-addr ip port current_epoch runid

（ip：主观下线的服务 id，port：主观下线的服务端口，current_epoch：sentinel 的纪元，runid：* 表示检测服务下线状态，如果是 sentinel 运行 id，表示用来选举领头 sentinel）

来询问其它 sentinel 是否同意服务下线。  
一个 sentinel 接收另一个 sentinel 发来的 is-master-down-by-addr 后，提取参数，根据 ip 和端口，检测该服务时候在该 sentinel 主观下线，并且回复 is-master-down-by-addr，回复包含三个参数：down_state（1 表示已下线，0 表示未下线），leader_runid（领头 sentinal id），leader_epoch（领头 sentinel 纪元）。  
sentinel 接收到回复后，根据配置设置的下线最小数量，达到这个值，既认为该服务客观下线。  
客观下线条件只适用于主服务器： 对于任何其他类型的 Redis 实例， Sentinel 在将它们判断为下线前不需要进行协商， 所以从服务器或者其他 Sentinel 永远不会达到客观下线条件。只要一个 Sentinel 发现某个主服务器进入了客观下线状态， 这个 Sentinel 就可能会被其他 Sentinel 推选出， 并对失效的主服务器执行自动故障迁移操作。

### 4.3.8、选举大哥 sentinel

一个 redis 服务被判断为客观下线时，多个监视该服务的 sentinel 协商，选举一个领头 sentinel，对该 redis 服务进行故障转移操作。**选举领头 sentinel 遵循以下规则：**  
1）所有的 sentinel 都有公平被选举成领头的资格。  
2）所有的 sentinel 都只有一次将某个 sentinel 选举成领头的机会（在一轮选举中），一旦选举，不能更改。  
3）先到先得，一旦当前 sentinel 设置了领头 sentinel，以后要求设置 sentinel 为领头请求都会被拒绝。  
4）每个发现服务客观下线的 sentinel，都会要求其他 sentinel 将自己设置成领头。  
5）当一个 sentinel（源 sentinel）向另一个 sentinel（目 sentinel）发送 is-master-down-by-addr ip port current_epoch runid 命令的时候，runid 参数不是 *，而是 sentinel 运行 id，就表示源 sentinel 要求目标 sentinel 选举其为领头。  
6）源 sentinel 会检查目标 sentinel 对其要求设置成领头的回复，如果回复的 leader_runid 和 leader_epoch 为源 sentinel，表示目标 sentinel 同意将源 sentinel 设置成领头。  
7）如果某个 sentinel 被半数以上的 sentinel 设置成领头，那么该 sentinel 既为领头。  
8）如果在限定时间内，没有选举出领头 sentinel，暂定一段时间，再选举。

**为什么要选？**  
简单来说，就是因为只能有一个 sentinel 节点去完成故障转移。  
sentinel is-master-down-by-addr 这个命令有两个作用，一是确认下线判定，二是进行领导者选举。  
**过程：**  
1）每个做主观下线的 sentinel 节点向其他 sentinel 节点发送上面那条命令，要求将它设置为领导者。  
2）收到命令的 sentinel 节点如果还没有同意过其他的 sentinel 发送的命令（还未投过票），那么就会同意，否则拒绝。  
3）如果该 sentinel 节点发现自己的票数已经过半且达到了 quorum 的值，就会成为领导者  
4）如果这个过程出现多个 sentinel 成为领导者，则会等待一段时间重新选举。

### 4.3.9、转移

1）挑一个新的主服务器

2）把其它从服务器的主服务器改成新的

3）把之前的主服务器改为新主服务器的从服务器

### 4.3.10、怎么挑新的主服务器

1）删除所有下线服务器

2）删除五秒内没回复 INOF 命令的服务器

3）删除数据旧的服务器（连接断开超过 down-after-millseconds*10）

4）根据优先级，选出最高的。

### 4.3.11、重点提炼

*   Sentinel 是一个特殊模式下的 Redis 服务器， 它使用了不同的命令表， 所以 Sentinel 能使用的命令和普通服务器不同。
*   Sentinel 会读入用户指定的配置文件， 为每个要被监视的主服务器创建相应的实例结构， 并创建连向主服务器的命令连接和订阅连接， 其中命令连接用于向主服务器发送命令请求， 而订阅连接则用于接收指定频道的消息。
*   Sentinel 向主服务器发送 INFO 命令获得属下从服务器信息， 为这些从服务器创建实例结构、命令连接和订阅连接。
*   默认 Sentinel 十秒一次向被监视的主服务器和从服务器发送 INFO 命令， 当主服务器处于下线状态， 或者 Sentinel 正在对主服务器进行故障转移操作时， Sentinel 向从服务器发送 INFO 命令的频率会改为每秒一次。
*   对于监视同一个主服务器和从服务器的多个 Sentinel 来说， 它们会以每两秒一次的频率， 通过向被监视服务器的 `__sentinel__:hello` 频道发送消息来向其他 Sentinel 宣告自己的存在。
*   每个 Sentinel 也会从 `__sentinel__:hello` 频道中接收其他 Sentinel 发来的信息， 并根据这些信息为其他 Sentinel 创建相应的实例结构， 以及命令连接。
*   Sentinel 只会与主服务器和从服务器创建命令连接和订阅连接， Sentinel 与 Sentinel 之间则只创建命令连接。
*   Sentinel 以每秒一次的频率向实例（包括主服务器、从服务器、其他 Sentinel）发送 PING 命令， 并根据实例对 PING 命令的回复来判断实例是否在线
*   当 Sentinel 将一个主服务器判断为主观下线时， 它会向同样监视这个主服务器的其他 Sentinel 进行询问， 看它们是否同意这个主服务器已经进入主观下线状态。
*   当 Sentinel 收集到足够多的主观下线投票之后， 它会将主服务器判断为客观下线， 并发起一次针对主服务器的故障转移操作。

> 《三天给你聊清楚 redis》第 3 天说说 redis 大概怎么用，和面试题（18000 字）

五、实战
====

5.1 基础实战
--------

### 5.1.1 实战点赞

点赞功能随处可见，我们都知道点赞是一个非常高频的操作，redis 就非常适合做这种工作。

实现效果：

![](https://img-blog.csdnimg.cn/20191028204136390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

分析：三种类型：给帖子点赞，给评论点赞，给回复点赞

我们只实现查看点赞数量的话，只要一个 int 记录一下就可以，但是我们之后还想查看点赞的人，所以要把每一个点赞的信息都记录好，方便后面的功能继续做出来。

思路：

点赞：把点赞的信息放进去。

取消：把点赞的信息删除。

在此之前，我们要封装一个 get 到 key 的类，方便后面复用。

```
$redis->multi()
->hmset("user:1",array("username"=>"hirryli","regtime"=>1234123483))
->Zadd("user:rank",$scores,$userId)
->incr("user:id")
->exec();
```

点赞业务：

```
//验证码
    @RequestMapping(path = "/kaptcha", method = RequestMethod.GET)
    public void getKaptcha(HttpServletResponse response/*, HttpSession session*/) {
        // 生成验证码
        String text = kaptchaProducer.createText();
        BufferedImage image = kaptchaProducer.createImage(text);
 
        // 将验证码存入session
        //session.setAttribute("kaptcha", text);
 
        //验证码的归属
        String owner= CommunityUtil.generateUUID();
        Cookie cookie=new Cookie("kaptchaOwner",owner);
        cookie.setMaxAge(60);
        cookie.setPath(contextPath);
        response.addCookie(cookie);
 
        //存入redis
        String redisKey= RedisKeyUtil.getKaptchaKey(owner);
        redisTemplate.opsForValue().set(redisKey,text,60, TimeUnit.SECONDS);
 
        // 将图片输出给浏览器
        response.setContentType("image/png");
        try {
            OutputStream os = response.getOutputStream();
            ImageIO.write(image, "png", os);
        } catch (IOException e) {
            logger.error("响应验证码失败:" + e.getMessage());
        }
    }
```

我们要查找是否点赞，还有点赞数量，方便页面显示：

```
@RequestMapping(path = "/login",method = RequestMethod.POST)
    public String login(String username,String password,String code,boolean rememberme,
                        Model model,/*HttpSession session,*/HttpServletResponse response,
                        @CookieValue("kaptchaOwner") String kaptchaOwner){
        // 检查验证码
        //String kaptcha = (String) session.getAttribute("kaptcha");
 
        String kaptcha=null;
        if(StringUtils.isNotBlank(kaptchaOwner)){
            String redisKey=RedisKeyUtil.getKaptchaKey(kaptchaOwner);
            kaptcha=(String) redisTemplate.opsForValue().get(redisKey);
        }
 
        if(StringUtils.isBlank(kaptcha) || StringUtils.isBlank(code) || !kaptcha.equalsIgnoreCase(code)){
            model.addAttribute("codeMsg", "验证码不正确!");
            return "/site/login";
        }
        // 检查账号,密码
        int expiredSeconds = rememberme ? REMEMBER_EXPIRED_SECONDS : DEFAULT_EXPIRED_SECONDS;
        Map<String, Object> map = userService.login(username, password, expiredSeconds);
        if (map.containsKey("ticket")) {
            Cookie cookie = new Cookie("ticket", map.get("ticket").toString());
            cookie.setPath(contextPath);
            cookie.setMaxAge(expiredSeconds);
            response.addCookie(cookie);
            return "redirect:/index";
        } else {
            ...
        }
    }
```

点赞 LikeController

```
public Map<String,Object> login(String username,String password,int expiredSeconds){
        Map<String,Object> map=new HashMap<>();
 
        // 生成登录凭证
        LoginTicket loginTicket = new LoginTicket();
        loginTicket.setUserId(user.getId());
        loginTicket.setTicket(CommunityUtil.generateUUID());
        loginTicket.setStatus(0);
        loginTicket.setExpired(new Date(System.currentTimeMillis() + expiredSeconds * 1000));
        //loginTicketMapper.insertLoginTicket(loginTicket);
 
        String redisKey= RedisKeyUtil.getTicketKey(loginTicket.getTicket());
        redisTemplate.opsForValue().set(redisKey,loginTicket);
        ...
    }
```

###  5.1.2 实战关注

效果： 

![](https://img-blog.csdnimg.cn/20191029142507534.png)

![](https://img-blog.csdnimg.cn/20191029142519701.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

思路：很好想，把自己的粉丝和自己关注的人都存起来（set 即可），做增删改查。

```
public void logout(String ticket) {
 
        //loginTicketMapper.updateStatus(ticket, 1);
        String redisKey= RedisKeyUtil.getTicketKey(ticket);
        LoginTicket loginTicket=(LoginTicket) redisTemplate.opsForValue().get(redisKey);
        loginTicket.setStatus(1);
        redisTemplate.opsForValue().set(redisKey,loginTicket);
    }
```

###   5.1.3 实战统计访问量

过于简单不解释

![](https://img-blog.csdnimg.cn/20210315130808743.png)

###   5.1.4 实战排行榜

```
// 1.优先从缓存中取值
    private User getCache(int userId) {
        String redisKey = RedisKeyUtil.getUserKey(userId);
        return (User) redisTemplate.opsForValue().get(redisKey);
    }
 
    // 2.取不到时初始化缓存数据
    private User initCache(int userId) {
        User user = userMapper.selectById(userId);
        String redisKey = RedisKeyUtil.getUserKey(userId);
        redisTemplate.opsForValue().set(redisKey, user, 3600, TimeUnit.SECONDS);
        return user;
    }
 
    // 3.数据变更时清除缓存数据
    private void clearCache(int userId) {
        String redisKey = RedisKeyUtil.getUserKey(userId);
        redisTemplate.delete(redisKey);
    }
```

```
public User findUserById(int id) {
//        return userMapper.selectById(id);
        User user = getCache(id);
        if (user == null) {
            user = initCache(id);
        }
        return user;
    }
```

Redis 本身支持一些简单的组合型的命令，比如以 NX 结尾命令都是判断在这个值没有时才进行某个命令

•       Redis 支持自定义的命令组合，通过 MULTI 和 EXEC，将几个命令组合起来执行

•       如：插入排行数据和用户信息，并自增 id

```
public int updateHeader(int userId, String headerUrl) {
 
        //return userMapper.updateHeader(userId, headerUrl);
        int rows=userMapper.updateHeader(userId, headerUrl);
        clearCache(userId);
        return rows;
    }
```

5.2 实战优化小项目
-----------

这是我们之前项目的业务流程，做一下简单介绍。

### 登录：

![](https://img-blog.csdnimg.cn/20191030140330511.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

用户输入账号、密码、验证码。我们先判断用户输入的验证码是不是我们 session 存的验证码，然后去查账号密码是否正确。

如果登录成功，发送给用户一张凭证（ticket）。

### 登录后

![](https://img-blog.csdnimg.cn/20191030103908255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

之后的每次请求，用户携带 ticket，服务器得到后，根据 ticket 去 login_ticket 表中查找登录信息，并且根据登录信息再查 user 表获得更多的用户信息。

### 使用 Redis 存储验证码

  
- 验证码需要频繁的访问与刷新，对性能要求较高。  
- 验证码不需永久保存，通常在很短的时间后就会失效。  
- 分布式部署时，存在 Session 共享的问题。

我们重构思路：进入登录页面会访问验证码方法，此方法会自动生成一个验证码和图片，将验证码和图片输出给浏览器，并且下发一个 cookies，这个 cookies 里面存的是一段随机数，这段随机数作为 key 存在 redis 里面（之前是存 session），value 就是验证码，并设置一个过期时间；

```
@RequestMapping(path = "/cookie/set",method = RequestMethod.GET)
    @ResponseBody
    public String setCookie(HttpServletResponse httpServletResponse){
        Cookie cookie=new Cookie("code", CommunityUtil.generateUUID());
        cookie.setPath("/community/alpha");
        cookie.setMaxAge(60*10);
        httpServletResponse.addCookie(cookie);
        return "set cookie";
    }
 
 
    @RequestMapping(path = "/cookie/get",method = RequestMethod.GET)
    @ResponseBody
    public String getCookie(@CookieValue("code") String code){
        System.out.println(code);
        return "get cookie";
    }
 
 
    @RequestMapping(path = "/session/set", method = RequestMethod.GET)
    @ResponseBody
    public String setSession(HttpSession session){
        session.setAttribute("id",1);
        session.setAttribute("name","Test");
        return "set session";
    }
 
 
    @RequestMapping(path = "/session/get", method = RequestMethod.GET)
    @ResponseBody
    public String getSession(HttpSession session) {
        System.out.println(session.getAttribute("id"));
        System.out.println(session.getAttribute("name"));
        return "get session";
    }
```

```
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

###   
使用 Redis 存储登录凭证

  
- 处理每次请求时，都要查询用户的登录凭证，访问的频率非常高。

登录时不存 MySQL 里，存 redis 里

```
public Map<String,Object> login(String username,String password,int expiredSeconds){
        Map<String,Object> map=new HashMap<>();
 
        // 生成登录凭证
        LoginTicket loginTicket = new LoginTicket();
        loginTicket.setUserId(user.getId());
        loginTicket.setTicket(CommunityUtil.generateUUID());
        loginTicket.setStatus(0);
        loginTicket.setExpired(new Date(System.currentTimeMillis() + expiredSeconds * 1000));
        //loginTicketMapper.insertLoginTicket(loginTicket);
 
        String redisKey= RedisKeyUtil.getTicketKey(loginTicket.getTicket());
        redisTemplate.opsForValue().set(redisKey,loginTicket);
        ...
    }
```

查找

退出时也是改 redis 

```
public void logout(String ticket) {
 
        //loginTicketMapper.updateStatus(ticket, 1);
        String redisKey= RedisKeyUtil.getTicketKey(ticket);
        LoginTicket loginTicket=(LoginTicket) redisTemplate.opsForValue().get(redisKey);
        loginTicket.setStatus(1);
        redisTemplate.opsForValue().set(redisKey,loginTicket);
    }
```

###   
使用 Redis 缓存用户信息

  
- 处理每次请求时，都要根据凭证查询用户信息，访问的频率非常高。

缓存用户信息：因为会经常根据 userid 来查询 user 对象，所以使用 redis 来缓存提高服务器性能。使用 redis 的 String 类型，存入 user 对象，会自动将整个对象转换成 json 字符串，同时设置过期时间；

取值：优先从 redis 中取，取不到的时候从 mysql 中取，并将数据初始化到 redis 中

更新：更新的时候先更新 mysql 中的值，然后清除缓存数据；

```
// 1.优先从缓存中取值
    private User getCache(int userId) {
        String redisKey = RedisKeyUtil.getUserKey(userId);
        return (User) redisTemplate.opsForValue().get(redisKey);
    }
 
    // 2.取不到时初始化缓存数据
    private User initCache(int userId) {
        User user = userMapper.selectById(userId);
        String redisKey = RedisKeyUtil.getUserKey(userId);
        redisTemplate.opsForValue().set(redisKey, user, 3600, TimeUnit.SECONDS);
        return user;
    }
 
    // 3.数据变更时清除缓存数据
    private void clearCache(int userId) {
        String redisKey = RedisKeyUtil.getUserKey(userId);
        redisTemplate.delete(redisKey);
    }
```

```
public User findUserById(int id) {
//        return userMapper.selectById(id);
        User user = getCache(id);
        if (user == null) {
            user = initCache(id);
        }
        return user;
    }
```

```
public int updateHeader(int userId, String headerUrl) {
 
        //return userMapper.updateHeader(userId, headerUrl);
        int rows=userMapper.updateHeader(userId, headerUrl);
        clearCache(userId);
        return rows;
    }
```

 5.3 讨论一下为啥用 redis 解决会话？
------------------------

什么是会话？

  会话可简单理解为：用户开一个浏览器，点击多个超链接，访问服务器多个 web 资源，然后关闭浏览器，整个过程称之为一个会话。

• 会话过程中要解决的一些问题？

–每个用户不可避免各自会产生一些数据，程序要想办法为每个用户保存这些数据。

–例如：用户点击超链接通过一个 servlet 购买了一个商品，程序应该想办法保存用户购买的商品，以便于用户点结帐 servlet 时，结帐 servlet 可以得到用户购买的商品为用户结帐。

•Cookie

–Cookie 是客户端技术，程序把每个用户的数据以 cookie 的形式写给用户各自的浏览器。当用户使用浏览器再去访问服务器中的 web 资源时，就会带着各自的数据去。这样，web 资源处理的就是用户各自的数据了。

•HttpSession

–Session 是服务器端技术，利用这个技术，服务器在运行时可以为每一个用户的浏览器创建一个其独享的 HttpSession 对象，由于 session 为用户浏览器独享，所以用户在访问服务器的 web 资源时，可以把各自的数据放在各自的 session 中，当用户再去访问服务器中的其它 web 资源时，其它 web 资源再从用户各自的 session 中取出数据为用户服务。

总结：cookie 存在客户端，session 存在服务器端

 通常结合使用。

![](https://img-blog.csdnimg.cn/20210315132523276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlYnR1NjY2,size_16,color_FFFFFF,t_70)

我们先用 sprintboot 演示一下 cookie 和 session 操作

```
@RequestMapping(path = "/cookie/set",method = RequestMethod.GET)
    @ResponseBody
    public String setCookie(HttpServletResponse httpServletResponse){
        Cookie cookie=new Cookie("code", CommunityUtil.generateUUID());
        cookie.setPath("/community/alpha");
        cookie.setMaxAge(60*10);
        httpServletResponse.addCookie(cookie);
        return "set cookie";
    }
 
 
    @RequestMapping(path = "/cookie/get",method = RequestMethod.GET)
    @ResponseBody
    public String getCookie(@CookieValue("code") String code){
        System.out.println(code);
        return "get cookie";
    }
 
 
    @RequestMapping(path = "/session/set", method = RequestMethod.GET)
    @ResponseBody
    public String setSession(HttpSession session){
        session.setAttribute("id",1);
        session.setAttribute("name","Test");
        return "set session";
    }
 
 
    @RequestMapping(path = "/session/get", method = RequestMethod.GET)
    @ResponseBody
    public String getSession(HttpSession session) {
        System.out.println(session.getAttribute("id"));
        System.out.println(session.getAttribute("name"));
        return "get session";
    }
```

随着服务器要处理的请求越来越多，我们不得不分布式部署，减小服务器压力。

为了负载均衡，我们一般采用 nginx 来分发请求给各个服务器处理

![](https://img-blog.csdnimg.cn/20191023214334592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

但是这样 session 是无法共享的。

（粘性 session）

你可以设置 nginx 的分配策略，下次同一个还让同一个服务器来处理

但是很显然，这就和分布式和 nginx 初衷违背了：负载很难保证均衡。

（同步 session）

一台服务器的 session 给所有服务器复制一份

第一，性能不好。第二，产生了一定的耦合

（专门 session）

专门一台服务器来解决，存 session，其它服务器来这个服务器取 session 再用。

![](https://img-blog.csdnimg.cn/20191023214820389.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

但是也有问题：你这个服务器挂了怎么办？别的服务器都是依赖这个服务器工作的。我们分布式部署本来就是为了解决性能的瓶颈啊。

很容易想到，我们把那个处理 session 的服务器搞个集群：

![](https://img-blog.csdnimg.cn/20191023215000650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

更不行，想想就知道，本来就是为了解决分布式部署的问题，你把单独解决 session 的服务器又搞集群，和之前有什么区别呢？还不如一个服务器存一份简单呢。

（存数据库）

![](https://img-blog.csdnimg.cn/20191023215544270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

可以，但是传统的关系数据库是存到硬盘里，速度太慢。

（nosql）

最终，我们的主流办法使用 nosql 数据库，比如 redis，来解决这个问题的，如果有不同意见，欢迎讨论。

 5.4 插曲：RedLock 小专栏
-------------------

### 概念

Redis 官方站这篇文章提出了一种权威的基于 Redis 实现分布式锁的方式名叫 _Redlock_，此种方式比原先的单节点的方法更安全。它可以保证以下特性：

1.  安全特性：互斥访问，即永远只有一个 client 能拿到锁
2.  避免死锁：最终 client 都可能拿到锁，不会出现死锁的情况，即使原本锁住某资源的 client crash 了或者出现了网络分区
3.  容错性：只要大部分 Redis 节点存活就可以正常提供服务

### 单节点实现

> SET resource_name my_random_value NX PX 30000

主要依靠上述命令，该命令仅当 Key 不存在时（NX 保证）set 值，并且设置过期时间 3000ms （PX 保证），值 my_random_value 必须是所有 client 和所有锁请求发生期间唯一的，释放锁的逻辑是：

```
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

上述实现可以避免释放另一个 client 创建的锁，如果只有 del 命令的话，那么如果 client1 拿到 lock1 之后因为某些操作阻塞了很长时间，此时 Redis 端 lock1 已经过期了并且已经被重新分配给了 client2，那么 client1 此时再去释放这把锁就会造成 client2 原本获取到的锁被 client1 无故释放了，但现在为每个 client 分配一个 unique 的 string 值可以避免这个问题。至于如何去生成这个 unique string，方法很多随意选择一种就行了。

### redlock 算法

算法很易懂，起 5 个 master 节点，分布在不同的机房尽量保证可用性。为了获得锁，client 会进行如下操作：

1.  得到当前的时间，微秒单位
2.  尝试顺序地在 5 个实例上申请锁，当然需要使用相同的 key 和 random value，这里一个 client 需要合理设置与 master 节点沟通的 timeout 大小，避免长时间和一个 fail 了的节点浪费时间
3.  当 client 在大于等于 3 个 master 上成功申请到锁的时候，且它会计算申请锁消耗了多少时间，这部分消耗的时间采用获得锁的当下时间减去第一步获得的时间戳得到，如果锁的持续时长（lock validity time）比流逝的时间多的话，那么锁就真正获取到了。
4.  如果锁申请到了，那么锁真正的 lock validity time 应该是 origin（lock validity time） - 申请锁期间流逝的时间
5.  如果 client 申请锁失败了，那么它就会在少部分申请成功锁的 master 节点上执行释放锁的操作，重置状态

### 失败重试

如果一个 client 申请锁失败了，那么它需要稍等一会在重试避免多个 client 同时申请锁的情况，最好的情况是一个 client 需要几乎同时向 5 个 master 发起锁申请。另外就是如果 client 申请锁失败了它需要尽快在它曾经申请到锁的 master 上执行 unlock 操作，便于其他 client 获得这把锁，避免这些锁过期造成的时间浪费，当然如果这时候网络分区使得 client 无法联系上这些 master，那么这种浪费就是不得不付出的代价了。

### 放锁

放锁操作很简单，就是依次释放所有节点上的锁就行了

### 性能、崩溃恢复

如果我们的节点没有持久化机制，client 从 5 个 master 中的 3 个处获得了锁，然后其中一个重启了，这是注意 **整个环境中又出现了 3 个 master 可供另一个 client 申请同一把锁！** 违反了互斥性。如果我们开启了 AOF 持久化那么情况会稍微好转一些，因为 Redis 的过期机制是语义层面实现的，所以在 server 挂了的时候时间依旧在流逝，重启之后锁状态不会受到污染。但是考虑断电之后呢，AOF 部分命令没来得及刷回磁盘直接丢失了，除非我们配置刷回策略为 fsnyc = always，但这会损伤性能。解决这个问题的方法是，当一个节点重启之后，我们规定在 max TTL 期间它是不可用的，这样它就不会干扰原本已经申请到的锁，等到它 crash 前的那部分锁都过期了，环境不存在历史锁了，那么再把这个节点加进来正常工作。

六、常见问题汇总
========

写到这里，从原理到简单的实战就全部写完了，这里汇总一些常用的以及面试常问的题目，希望帮助到大家。

1、什么是 redis？
------------

  
Redis 本质上是一个 Key-Value 类型的内存数据库，  整个数据库加载在内存当中进行操作， 定期通过异步操作把数据库数据 flush 到硬盘上进行保存。

因为是纯内存操作， Redis 的性能非常出色， 每秒可以处理超过 10 万次读写操作， 是已知性能  
最快的 Key-Value DB。  
Redis 的出色之处不仅仅是性能， Redis 最大的魅力是支持保存多种数据结构， 此外单个  
value 的最大限制是 1GB， 不像 memcached 只能保存 1MB 的数据， 因此 Redis 可以用  
来实现很多有用的功能，比方说用他的 List 来做 FIFO 双向链表，实现一个轻量级的高性 能  
消息队列服务， 用他的 Set 可以做高性能的 tag 系统等等。

另外 Redis 也可以对存入的 Key-Value 设置 expire 时间， 因此也可以被当作一 个功能加强版的 memcached 来用。  
Redis 的主要缺点是数据库容量受到物理内存的限制， 不能用作海量数据的高性能读写， 因此 Redis 适合的场景主要局限在较小数据量的高性能操作和运算上

  
 2、相比 memcached 有哪些优势？
-------------------------

1.  **redis 支持更丰富的数据类型（支持更复杂的应用场景）**：Redis 不仅仅支持简单的 k/v 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。memcache 支持简单的数据类型，String。
2.  **Redis 支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用, 而 Memecache 把数据全部存在内存之中。**
3.  **集群模式**：memcached 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；但是 redis 目前是原生支持 cluster 模式的.
4.  **Memcached 是多线程，非阻塞 IO 复用的网络模型；Redis 使用单线程的多路 IO 复用模型。**

![](https://img-blog.csdnimg.cn/2019110319123966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9mYW50aWFuenVvLmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

3、Redis 的全称是什么？
---------------

  
Remote Dictionary Server。

4、支持哪几种数据类型？
------------

  
String、 List、 Set、 Sorted Set、 hashes  
 

5、Redis 有哪几种数据淘汰策略？
-------------------

  
noeviction: 返回错误当内存限制达到并且客户端尝试执行会让更多内存被使用的命令（大  
部分的写入指令， 但 DEL 和几个例外）  
allkeys-lru: 尝试回收最少使用的键（LRU）， 使得新添加的数据有空间存放。  
volatile-lru: 尝试回收最少使用的键（LRU）， 但仅限于在过期集合的键, 使得新添加的数据  
有空间存放。  
allkeys-random: 回收随机的键使得新添加的数据有空间存放。  
volatile-random: 回收随机的键使得新添加的数据有空间存放，但仅限于在过期集合的键。  
volatile-ttl: 回收在过期集合的键， 并且优先回收存活时间（TTL） 较短的键, 使得新添加的  
数据有空间存放  
 

6、redis 为什么采用跳表而不是红黑树
---------------------

在做范围查找的时候，平衡树比 skiplist 操作要复杂。在平衡树上，我们找到指定范围的小值之后，还需要以中序遍历的顺序继续寻找其它不超过大值的节点。如果不对平衡树进行一定的改造，这里的中序遍历并不容易实现。而在 skiplist 上进行范围查找就非常简单，只需要在找到小值之后，对第 1 层链表进行若干步的遍历就可以实现。  
平衡树的插入和删除操作可能引发子树的调整，逻辑复杂，而 skiplist 的插入和删除只需要修改相邻节点的指针，操作简单又快速。  
从内存占用上来说，skiplist 比平衡树更灵活一些。一般来说，平衡树每个节点包含 2 个指针（分别指向左右子树），而 skiplist 每个节点包含的指针数目平均为 1/(1-p)，具体取决于参数 p 的大小。如果像 Redis 里的实现一样，取 p=1/4，那么平均每个节点包含 1.33 个指针，比平衡树更有优势。  
查找单个 key，skiplist 和平衡树的时间复杂度都为 O(log n)，大体相当；而哈希表在保持较低的哈希值冲突概率的前提下，查找时间复杂度接近 O(1)，性能更高一些。所以我们平常使用的各种 Map 或 dictionary 结构，大都是基于哈希表实现的。  
从算法实现难度上来比较，skiplist 比平衡树要简单得多。

7、介绍一下 HyperLogLog？
-------------------

HyperLogLog 是一种概率数据结构，用来估算数据的基数。数据集可以是网站访客的 IP 地址，E-mail 邮箱或者用户 ID。

基数就是指一个集合中不同值的数目，比如 a, b, c, d 的基数就是 4，a, b, c, d, a 的基数还是 4。虽然 a 出现两次，只会被计算一次。

使用 Redis 统计集合的基数一般有三种方法，分别是使用 Redis 的 HashMap，BitMap 和 HyperLogLog。前两个数据结构在集合的数量级增长时，所消耗的内存会大大增加，但是 HyperLogLog 则不会。

Redis 的 HyperLogLog 通过牺牲准确率来减少内存空间的消耗，只需要 12K 内存，在标准误差 0.81% 的前提下，能够统计 2^64 个数据。所以 HyperLogLog 是否适合在比如统计日活月活此类的对精度要不不高的场景。

这是一个很惊人的结果，以如此小的内存来记录如此大数量级的数据基数。

  
8、为什么 Redis 需要把所有数据放到内存中？
----------------------------

  
Redis 为了达到最快的读写速度将数据都读到内存中， 并通过异步的方式将数据写入磁盘。  
所以 Redis 具有快速和数据持久化的特征。 如果不将数据放在内存中， 磁盘 I/O 速度为严重  
影响 Redis 的性能。 在内存越来越便宜的今天， Redis 将会越来越受欢迎。

**9、Redis 支持的数据类型？**
--------------------

String 字符串：

格式: set key value

string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如 jpg 图片或者序列化的对象 。

string 类型是 Redis 最基本的数据类型，一个键最大能存储 512MB。

Hash（哈希）

格式: hmset name  key1 value1 key2 value2

Redis hash 是一个键值 (key=>value) 对集合。

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）

格式: lpush  name  value

在 key 对应 list 的头部添加字符串元素

格式: rpush  name  value

在 key 对应 list 的尾部添加字符串元素

格式: lrem name  index

key 对应 list 中删除 count 个和 value 相同的元素

格式: llen name  

返回 key 对应 list 的长度

Set（集合）

格式: sadd  name  value

Redis 的 Set 是 string 类型的无序集合。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

zset(sorted set：有序集合)

格式: zadd  name score value

Redis zset 和 set 一样也是 string 类型元素的集合, 且不允许重复的成员。

不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。

zset 的成员是唯一的, 但分数 (score) 却可以重复。

10、 sds 相对 c 的改进？
-----------------

  获取长度：c 字符串并不记录自身长度，所以获取长度只能遍历一遍字符串，redis 直接读取 len 即可。

    缓冲区安全：c 字符串容易造成缓冲区溢出，比如：程序员没有分配足够的空间就执行拼接操作。而 redis 会先检查 sds 的空间是否满足所需要求，如果不满足会自动扩充。

    内存分配：由于 c 不记录字符串长度，对于包含了 n 个字符的字符串，底层总是一个长度 n+1 的数组，每一次长度变化，总是要对这个数组进行一次内存重新分配的操作。因为内存分配涉及复杂算法并且可能需要执行系统调用，所以它通常是比较耗时的操作。     
 

11、redis 链表源码？有什么特性？
--------------------

双端、无环、带长度记录、

多态：使用 `void*` 指针来保存节点值， 可以通过 `dup` 、 `free` 、 `match` 为节点值设置类型特定函数， 可以保存不同类型的值。

12、字典是如何实现的？
------------

其实字典这种数据结构也内置在很多高级语言中，但是 c 语言没有，所以 redis 自己实现了。

应用也比较广泛，比如 redis 的数据库就是字典实现的。不仅如此，当一个哈希键包含的键值对比较多，或者都是很长的字符串，redis 就会用字典作为哈希键的底层实现。

13、LRU？redis 里的具体实现？
--------------------

LRU 全称是 Least Recently Used，即最近最久未使用的意思。

LRU 算法的设计原则是：**如果一个数据在最近一段时间没有被访问到，那么在将来它被访问的可能性也很小**。也就是说，当限定的空间已存满数据时，应当把最久没有被访问到的数据淘汰。

redis 原始的淘汰算法简单实现：当需要淘汰一个 key 时，随机选择 3 个 key，淘汰其中间隔时间最长的 key。** 基本上，我们随机选择 key，淘汰 key 效果很好。后来随机 3 个 key 改成一个配置项 "N 随机 key"。但把默认值提高改成 5 个后效果大大提高。考虑到它的效果，你根本不用修改他。

14、redis 的持久化？
--------------

RDB 持久化可以手动执行，也可以配置定期执行，可以把某个时间的数据状态保存到 RDB 文件中，反之，我们可以用 RDB 文件还原数据库状态。

AOF 持久化是通过保存服务器执行的命令来记录状态的。还原的时候再执行一遍即可。

15、如何选择合适的持久化方式？
----------------

  
一般来说， 如果想达到足以媲美 PostgreSQL 的数据安全性， 你应该同时使用两种持久  
化功能。 如果你非常关心你的数据， 但仍然可以承受数分钟以内的数据丢失， 那么你可以  
只使用 RDB 持久化。  
有很多用户都只使用 AOF 持久化， 但并不推荐这种方式： 因为定时生成 RDB 快照  
（snapshot） 非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复  
的速度要快， 除此之外， 使用 RDB 还可以避免之前提到的 AOF 程序的 bug。  
 

16、Redis 集群方案应该怎么做？ 都有哪些方案？
---------------------------

  
1.twemproxy， 大概概念是， 它类似于一个代理方式， 使用方法和普通 Redis 无任何区别，  
设 置 好它 下 属 的多 个 Redis 实 例 后， 使 用 时在 本 需 要 连接 Redis 的 地 方改 为 连接  
twemproxy， 它会以一个代理的身份接收请求并使用一致性 hash 算法， 将请求转接到具  
体 Redis， 将结果再返回 twemproxy。 使用方式简便 (相对 Redis 只需修改连接端口)， 对  
旧项目扩展的首选。 问题： twemproxy 自身单端口实例的压力， 使用一致性 hash 后， 对  
Redis 节点数量改变时候的计算值的改变， 数据无法自动移动到新的节点。  
2. codis， 目前用的最多的集群方案， 基本和 twemproxy 一致的效果， 但它支持在 节点  
数量改变情况下， 旧节点数据可恢复到新 hash 节点。  
3. Redis cluster3.0 自带的集群， 特点在于他的分布式算法不是一致性 hash， 而是 hash  
槽的概念， 以及自身支持节点设置从节点。 具体看官方文档介绍。  
4. 在业务代码层实现， 起几个毫无关联的 Redis 实例， 在代码层， 对 key 进行 hash 计算，  
然后去对应的 Redis 实例操作数据。 这种方式对 hash 层代码要求比较高， 考虑部分包括，  
节点失效后的替代算法方案， 数据震荡后的自动脚本恢复， 实例的监控， 等等

  
MySQL 里有 2000w 数据， Redis 中只存 20w 的数据，

17、如何保证 Redis 中的数据都是热点数据？
-------------------------

  
Redis 内存数据集大小上升到一定大小的时候， 就会施行数据淘汰策略

18、Redis 有哪些适合的场景？
------------------

  
（1）、 会话缓存（Session Cache）  
最常用的一种使用 Redis 的情景是会话缓存（session cache）。 用 Redis 缓存会话比其他  
存储（如 Memcached） 的优势在于： Redis 提供持久化。 当维护一个不是严格要求一致性  
的缓存时， 如果用户的购物车信息全部丢失， 大部分人都会不高兴的， 现在， 他们还会这样  
吗？  
幸运的是， 随着 Redis 这些年的改进， 很容易找到怎么恰当的使用 Redis 来缓存会话的文  
档。 甚至广为人知的商业平台 Magento 也提供 Redis 的插件。  
（2）、 全页缓存（FPC）  
除基本的会话 token 之外， Redis 还提供很简便的 FPC 平台。 回到一致性问题， 即使重启  
了 Redis 实例， 因为有磁盘的持久化， 用户也不会看到页面加载速度的下降， 这是一个极  
大改进， 类似 PHP 本地 FPC。  
再次以 Magento 为例， Magento 提供一个插件来使用 Redis 作为全页缓存后端。  
此外， 对 WordPress 的用户来说， Pantheon 有一个非常好的插件 wp-Redis， 这个插件  
能帮助你以最快速度加载你曾浏览过的页面。  
（3）、 队列  
Reids 在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得 Redis 能作为一个  
很好的消息队列平台来使用。 Redis 作为队列使用的操作， 就类似于本地程序语言（如  
Python） 对 list 的 push/pop 操作。  
如果你快速的在 Google 中搜索 “Redis queues”， 你马上就能找到大量的开源项目， 这些  
项目的目的就是利用 Redis 创建非常好的后端工具， 以满足各种队列需求。 例如， Celery  
有一个后台就是使用 Redis 作为 broker， 你可以从这里去查看。  
（4）、 排行榜 / 计数器  
Redis 在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序集合（Sorted  
Set） 也使得我们在执行这些操作的时候变的非常简单， Redis 只是正好提供了这两种数据  
结构。 所以， 我们要从排序集合中获取到排名最靠前的 10 个用户–我们称之为  
“user_scores”， 我们只需要像下面一样执行即可：  
当然， 这是假定你是根据你用户的分数做递增的排序。 如果你想返回用户及用户的分数， 你  
需要这样执行：  
ZRANGE user_scores 0 10 WITHSCORES  
Agora Games 就是一个很好的例子， 用 Ruby 实现的， 它的排行榜就是使用 Redis 来存储  
数据的， 你可以在这里看到。  
（5）、 发布 / 订阅  
最后 是 Redis 的发布 / 订阅功能。 发布 / 订阅的使用场景确实非  
常多。 我已看见人们在社交网络连接中使用， 还可作为基于发布 / 订阅的脚本触发器， 甚至  
用 Redis 的发布 / 订阅功能来建立聊天系统。

  
19、说说 Redis 哈希槽的概念？
----------------------

  
Redis 集群没有使用一致性 hash, 而是引入了哈希槽的概念， Redis 集群有 16384 个哈希槽，  
每个 key 通过 CRC16 校验后对 16384 取模来决定放置哪个槽， 集群的每个节点负责一部分  
hash 槽

20、为什么 Redis 集群有 16384 个槽
-------------------------

(1) 如果槽位为 65536，发送心跳信息的消息头达 8k，发送的心跳包过于庞大。

如上所述，在消息头中，最占空间的是`myslots[CLUSTER_SLOTS/8]`。 当槽位为 65536 时，这块的大小是: `65536÷8÷1024=8kb` 因为每秒钟，redis 节点需要发送一定数量的 ping 消息作为心跳包，如果槽位为 65536，这个 ping 消息的消息头太大了，浪费带宽。

(2)redis 的集群主节点数量基本不可能超过 1000 个。

如上所述，集群节点越多，心跳包的消息体内携带的数据越多。如果节点过 1000 个，也会导致网络拥堵。因此 redis 作者，不建议 redis cluster 节点数量超过 1000 个。 那么，对于节点数在 1000 以内的 redis cluster 集群，16384 个槽位够用了。没有必要拓展到 65536 个。

(3) 槽位越小，节点少的情况下，压缩比高

Redis 主节点的配置信息中，它所负责的哈希槽是通过一张 bitmap 的形式来保存的，在传输过程中，会对 bitmap 进行压缩，但是如果 bitmap 的填充率 slots / N 很高的话 (N 表示节点数)，bitmap 的压缩率就很低。 如果节点数很少，而哈希槽数量很多的话，bitmap 的压缩率就很低。

21、Redis 集群会有写操作丢失吗？ 为什么？
-------------------------

  
Redis 并不能保证数据的强一致性， 这意味这在实际中集群在特定的条件下可能会丢失写操  
作。  
 

22、Redis 集群方案应该怎么做？都有哪些方案？
--------------------------

  
1.twemproxy，大概概念是，它类似于一个代理方式， 使用时在本需要连接 redis 的地方改为连接 twemproxy， 它会以一个代理的身份接收请求并使用一致性 hash 算法，将请求转接到具体 redis，将结果再返回 twemproxy。  
缺点： twemproxy 自身单端口实例的压力，使用一致性 hash 后，对 redis 节点数量改变时候的计算值的改变，数据无法自动移动到新的节点。

2.codis，目前用的最多的集群方案，基本和 twemproxy 一致的效果，但它支持在 节点数量改变情况下，旧节点数据可恢复到新 hash 节点

3.redis cluster3.0 自带的集群，特点在于他的分布式算法不是一致性 hash，而是 hash 槽的概念，以及自身支持节点设置从节点。具体看官方文档介绍。  
 

23、为什么要做 Redis 分区？
------------------

  
分区可以让 Redis 管理更大的内存， Redis 将可以使用所有机器的内存。 如果没有分区， 你  
最多只能使用一台机器的内存。 分区使 Redis 的计算能力通过简单地增加计算机得到成倍提  
升, Redis 的网络带宽也会随着计算机和网卡的增加而成倍增长。  
 

24、Redis 分区有什么缺点？
-----------------

  
涉及多个 key 的操作通常不会被支持。 例如你不能对两个集合求交集， 因为他们可能被存  
储到不同的 Redis 实例（实际上这种情况也有办法， 但是不能直接使用交集指令）。  
同时操作多个 key, 则不能使用 Redis 事务.  
分区使用的粒度是 key，不能使用一个非常长的排序 key 存储一个数据集（The partitioning  
granularity is the key, so it is not possible to shard a dataset with a single huge  
key like a very big sorted set） .  
当使用分区的时候， 数据处理会非常复杂， 例如为了备份你必须从不同的 Redis 实例和主  
机同时收集 RDB / AOF 文件。  
分区时动态扩容或缩容可能非常复杂。 Redis 集群在运行时增加或者删除 Redis 节点， 能  
做到最大程度对用户透明地数据再平衡，但其他一些客户端分区或者代理分区方法则不支持  
这种特性。 然而， 有一种预分片的技术也可以较好的解决这个问题。

25、Redis 与其他 key-value 存储有什么不同？
-------------------------------

  
Redis 有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库  
的进化路径。 Redis 的数据类型都是基于基本数据结构的同时对程序员透明， 无需进行额外  
的抽象。  
Redis 运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡  
内存， 应为数据量不能大于硬件内存。 在内存数据库方面的另一个优点是， 相比在磁盘上  
相同的复杂的数据结构， 在内存中操作起来非常简单， 这样 Redis 可以做很多内部复杂性  
很强的事情。 同时， 在磁盘格式方面他们是紧凑的以追加的方式产生的， 因为他们并不需  
要进行随机访问

  
26、Redis 的内存用完了会发生什么？
------------------------

  
如果达到设置的上限， Redis 的写命令会返回错误信息（但是读命令还可以正常返回。） 或  
者你可以将 Redis 当缓存来使用配置淘汰机制，当 Redis 达到内存上限时会冲刷掉旧的内容。  
 

27、Redis 是单线程的， 如何提高多核 CPU 的利用率？
--------------------------------

  
可以在同一个服务器部署多个 Redis 的实例， 并把他们当作不同的服务器来使用， 在某些时  
候， 无论如何一个服务器是不够的，  
所以， 如果你想使用多个 CPU， 你可以考虑一下分片（shard）。

  
28、一个 Redis 实例最多能存放多少的 keys？ List、 Set、Sorted Set 他们最多能存放多少元素？
-----------------------------------------------------------------

  
理论上 Redis 可以处理多达 232 的 keys， 并且在实际中进行了测试， 每个实例至少存放了 2 亿 5 千万的 keys。 我们正在测试一些较大的值。  
任何 list、 set、 和 sorted set 都可以放 232 个元素。  
换句话说， Redis 的存储极限是系统中的可用内存值

29、修改配置不重启 Redis 会实时生效吗？
------------------------

  
针对运行实例， 有许多配置选项可以通过 CONFIG SET 命令进行修改， 而无需执行任何  
形式的重启。 从 Redis 2.2 开始， 可以从 AOF 切换到 RDB 的快照持久性或其他方式  
而不需要重启 Redis。 检索 ‘CONFIG GET *’ 命令获取更多信息。  
但偶尔重新启动是必须的， 如为升级 Redis 程序到新的版本， 或者当你需要修改某些目前  
CONFIG 命令还不支持的配置参数的时候  
 

**30、哨兵**
---------

Redis sentinel 是一个分布式系统中监控 redis 主从服务器，并在主服务器下线时自动进行故障转移。其中三个特性：

监控（Monitoring）：    Sentinel  会不断地检查你的主服务器和从服务器是否运作正常。

提醒（Notification）： 被监控的某个 Redis 服务器出现问题时， Sentinel 可以通过 API 向管理员或者其他应用程序发送通知。

自动故障迁移（Automatic failover）： 当一个主服务器不能正常工作时， Sentinel 会开始一次自动故障迁移操作。

特点：

1、保证高可用

2、监控各个节点

3、自动故障迁移

缺点：主从模式，切换需要时间丢数据

没有解决 master 写的压力

31、缓存穿透
-------

  
一般的缓存系统，都是按照 key 去缓存查询，如果不存在对应的 value，就去后端系统查找（比如 DB）。

一些恶意的请求会故意查询不存在的 key, 请求量很大，就会对后端系统造成很大的压力。这就叫做缓存穿透。

如何避免？

1：对查询结果为空的情况也进行缓存，这样，再次访问时，缓存层会直接返回空值。缓存时间设置短一点，或者该 key 对应的数据 insert 了之后清理缓存。

2：对一定不存在的 key 进行过滤。具体请看布隆过滤器

32、缓存击穿
-------

  
是针对缓存中没有但数据库有的数据。

场景是，当 Key 失效后，假如瞬间突然涌入大量的请求，来请求同一个 Key，这些请求不会命中 Redis，都会请求到 DB，导致数据库压力过大，甚至扛不住，挂掉。

解决办法

1、设置热点 Key，自动检测热点 Key，将热点 Key 的过期时间加大或者设置为永不过期，或者设置为逻辑上永不过期

2、加互斥锁。当发现没有命中 Redis，去查数据库的时候，在执行更新缓存的操作上加锁，当一个线程访问时，其它线程等待，这个线程访问过后，缓存中的数据会被重建，这样其他线程就可以从缓存中取值。

33、缓存雪崩
-------

  
是指大量 Key 同时失效，对这些 Key 的请求又会打到 DB 上，同样会导致数据库压力过大甚至挂掉。

解决办法

1）让 Key 的失效时间分散开，可以在统一的失效时间上再加一个随机值，或者使用更高级的算法分散失效时间。

2）构建多个 redis 实例，个别节点挂了还有别的可以用。

3）多级缓存：比如增加本地缓存，减小 redis 压力。

4）对存储层增加限流措施，当请求超出限制，提供降级服务（一般就是返回错误即可）

34、单线程的 redis 为什么这么快
--------------------

(一) 纯内存操作  
(二) 单线程操作，避免了频繁的上下文切换  
(三) 采用了非阻塞 I/O 多路复用机制

（其实就是历史遗留问题，非要吹的这么好。。。）

35、redis 采用的删除策略
----------------

redis 采用的是定期删除 + 惰性删除策略。

  
36、为什么不用定时删除策略?
------------------

  
定时删除, 用一个定时器来负责监视 key, 过期则自动删除。虽然内存及时释放，但是十分消耗 CPU 资源。在大并发请求下，CPU 要将时间应用在处理请求，而不是删除 key, 因此没有采用这一策略.

  
37、定期删除 + 惰性删除是如何工作的呢?
-------------------------

  
定期删除，redis 默认每个 100ms 检查，是否有过期的 key, 有过期 key 则删除。需要说明的是，redis 不是每个 100ms 将所有的 key 检查一次，而是随机抽取进行检查 (如果每隔 100ms, 全部 key 进行检查，redis 岂不是卡死)。因此，如果只采用定期删除策略，会导致很多 key 到时间没有删除。  
于是，惰性删除派上用场。也就是说在你获取某个 key 的时候，redis 会检查一下，这个 key 如果设置了过期时间那么是否过期了？如果过期了此时就会删除。

38、为什么 Redis 的操作是原子性的，怎么保证原子性的？
-------------------------------

  
对于 Redis 而言，命令的原子性指的是：一个操作的不可以再分，操作要么执行，要么不执行。  
Redis 的操作之所以是原子性的，是因为 Redis 是单线程的。  
Redis 本身提供的所有 API 都是原子操作，Redis 中的事务其实是要保证批量操作的原子性。  
多个命令在并发中也是原子性的吗？  
不一定， 将 get 和 set 改成单命令操作，incr 。使用 Redis 的事务，或者使用 Redis+Lua== 的方式实现.  
 

39、消息队列
-------

不要使用 redis 去做消息队列，这不是 redis 的设计目标。但实在太多人使用 redis 去做去消息队列，redis 的作者看不下去。

kafka 才好用

> **有问题随时评论区提问**

 七、开个脑洞
=======

 最近又研究了一下 redis，说实话，我越研究越觉得不靠谱，为啥会有 nosql 这种东西？

以下是我混乱的思考

> 未来架构越来越复杂，saas 做不了一致性交给 redis 做。
> 
> 如果数据库本身就有个 redis 呢？是不是我们就不需要这玩意了。
> 
> 不对，使用场景还是有区别的，数据库本身不就有 buffer pool 么。
> 
> 一直在琢磨 redis 做缓存的必要性。
> 
> 我要把。。
> 
> 工作上的第三课，就是得考虑成本。之前我是纯正的 redis 吹，但是 mysql 有缓存那些数据和 redis 速度差不了太多，而且没有关系型数据库记录全面，最重要的是贵！
> 
> 最重要的是贵！最重要的是贵！最重要的是贵！
> 
> 我从一个幼稚的 redis 吹，变成了考虑更全面的人，看到了 redis 越来越多的缺点。希望，是我成长了吧。

全篇完。