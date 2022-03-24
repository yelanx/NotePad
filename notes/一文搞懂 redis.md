> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/7ct-mvSIaT3o4-tsMaKRWA)

![](https://mmbiz.qpic.cn/mmbiz_jpg/Z6bicxIx5naJKibdQfa3WbQf4YvHXZiaaMDLv2yvdmwE5lAhBGjnoGf9libzFZOpO3LrWTqyS6LvtwKuLOE8kFzfpg/640?wx_fmt=jpeg)

  

---

**一  什么是 NoSQL？**
-----------------

Nosql = not only sql（不仅仅是 SQL）

关系型数据库：列 + 行，同一个表下数据的结构是一样的。

  

非关系型数据库：数据存储没有固定的格式，并且可以进行横向扩展。

NoSQL 泛指非关系型数据库，随着 web2.0 互联网的诞生，传统的关系型数据库很难对付 web2.0 大数据时代！尤其是超大规模的高并发的社区，暴露出来很多难以克服的问题，NoSQL 在当今大数据环境下发展的十分迅速，Redis 是发展最快的。

传统 RDBMS 和 NoSQL

  

```
RDBMS
 - 组织化结构
 - 固定SQL
 - 数据和关系都存在单独的表中（行列）
 - DML（数据操作语言）、DDL（数据定义语言）等
 - 严格的一致性（ACID): 原子性、一致性、隔离性、持久性
 - 基础的事务

```

```
NoSQL
 - 不仅仅是数据
 - 没有固定查询语言
 - 键值对存储（redis）、列存储（HBase）、文档存储（MongoDB）、图形数据库（不是存图形，放的是关系）（Neo4j）
 - 最终一致性（BASE）：基本可用、软状态/柔性事务、最终一致性

```

**二  redis 是什么？**
-----------------

Redis = Remote Dictionary Server，即远程字典服务。

  

是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value 数据库，并提供多种语言的 API。

  

与 memcached 一样，为了保证效率，数据都是缓存在内存中。区别的是 redis 会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了 master-slave(主从) 同步。

**三  redis 五大基本类型**
-------------------

 Redis 是一个开源，内存存储的数据结构服务器，可用作数据库，高速缓存和消息队列代理。它支持字符串、哈希表、列表、集合、有序集合，位图，hyperloglogs 等数据类型。内置复制、Lua 脚本、LRU 收回、事务以及不同级别磁盘持久化功能，同时通过 Redis Sentinel 提供高可用，通过 Redis Cluster 提供自动分区。

由于 redis 类型大家很熟悉，且网上命令使用介绍很多，下面重点介绍五大基本类型的底层数据结构与应用场景，以便当开发时，可以熟练使用 redis。

  

### 1  String（字符串）

```
1.String类型是redis的最基础的数据结构，也是最经常使用到的类型。
  而且其他的四种类型多多少少都是在字符串类型的基础上构建的，所以String类型是redis的基础。
2.String 类型的值最大能存储 512MB，这里的String类型可以是简单字符串、
  复杂的xml/json的字符串、二进制图像或者音频的字符串、以及可以是数字的字符串

```

#### **应用场景**

1、缓存功能：String 字符串是最常用的数据类型，不仅仅是 redis，各个语言都是最基本类型，因此，利用 redis 作为缓存，配合其它数据库作为存储层，利用 redis 支持高并发的特点，可以大大加快系统的读写速度、以及降低后端数据库的压力。

  

2、计数器：许多系统都会使用 redis 作为系统的实时计数器，可以快速实现计数和查询的功能。而且最终的数据结果可以按照特定的时间落地到数据库或者其它存储介质当中进行永久保存。

  

3、统计多单位的数量：eg，uid：gongming   count：0    根据不同的 uid 更新 count 数量。

  

4、共享用户 session：用户重新刷新一次界面，可能需要访问一下数据进行重新登录，或者访问页面缓存 cookie，这两种方式做有一定弊端，1）每次都重新登录效率低下 2）cookie 保存在客户端，有安全隐患。这时可以利用 redis 将用户的 session 集中管理，在这种模式只需要保证 redis 的高可用，每次用户 session 的更新和获取都可以快速完成。大大提高效率。

  

### 2  List（列表）

```
list类型是用来存储多个有序的字符串的，列表当中的每一个字符看做一个元素
2.一个列表当中可以存储有一个或者多个元素，redis的list支持存储2^32次方-1个元素。
3.redis可以从列表的两端进行插入（pubsh）和弹出（pop）元素，支持读取指定范围的元素集，
  或者读取指定下标的元素等操作。redis列表是一种比较灵活的链表数据结构，它可以充当队列或者栈的角色。
4.redis列表是链表型的数据结构，所以它的元素是有序的，而且列表内的元素是可以重复的。
  意味着它可以根据链表的下标获取指定的元素和某个范围内的元素集。

```

#### **应用场景**

1、消息队列：reids 的链表结构，可以轻松实现阻塞队列，可以使用左进右出的命令组成来完成队列的设计。比如：数据的生产者可以通过 Lpush 命令从左边插入数据，多个数据消费者，可以使用 BRpop 命令阻塞的 “抢” 列表尾部的数据。

  

2、文章列表或者数据分页展示的应用。比如，我们常用的博客网站的文章列表，当用户量越来越多时，而且每一个用户都有自己的文章列表，而且当文章多时，都需要分页展示，这时可以考虑使用 redis 的列表，列表不但有序同时还支持按照范围内获取元素，可以完美解决分页查询功能。大大提高查询效率。

  

### 3  Set（集合）

```
1.redis集合（set）类型和list列表类型类似，都可以用来存储多个字符串元素的集合。
2.但是和list不同的是set集合当中不允许重复的元素。而且set集合当中元素是没有顺序的，不存在元素下标。
3.redis的set类型是使用哈希表构造的，因此复杂度是O(1)，它支持集合内的增删改查，
  并且支持多个集合间的交集、并集、差集操作。可以利用这些集合操作，解决程序开发过程当中很多数据集合间的问题。

```

1、标签：比如我们博客网站常常使用到的兴趣标签，把一个个有着相同爱好，关注类似内容的用户利用一个标签把他们进行归并。

  

2、共同好友功能，共同喜好，或者可以引申到二度好友之类的扩展应用。

  

3、统计网站的独立 IP。利用 set 集合当中元素不唯一性，可以快速实时统计访问网站的独立 IP。

数据结构

  

set 的底层结构相对复杂写，使用了 intset 和 hashtable 两种数据结构存储，intset 可以理解为数组。

### 4  sorted set（有序集合）

redis 有序集合也是集合类型的一部分，所以它保留了集合中元素不能重复的特性，但是不同的是，有序集合给每个元素多设置了一个分数。

  

```
redis有序集合也是集合类型的一部分，所以它保留了集合中元素不能重复的特性，但是不同的是，
有序集合给每个元素多设置了一个分数，利用该分数作为排序的依据。

```

#### **应用场景**

1、  排行榜：有序集合经典使用场景。例如视频网站需要对用户上传的视频做排行榜，榜单维护可能是多方面：按照时间、按照播放量、按照获得的赞数等。

  

2、用 Sorted Sets 来做带权重的队列，比如普通消息的 score 为 1，重要消息的 score 为 2，然后工作线程可以选择按 score 的倒序来获取工作任务。让重要的任务优先执行。

### 5  hash（哈希）

```
  Redis hash数据结构 是一个键值对（key-value）集合,它是一个 string 类型的 field 和 value 的映射表，
redis本身就是一个key-value型数据库，因此hash数据结构相当于在value中又套了一层key-value型数据。
所以redis中hash数据结构特别适合存储关系型对象

```

#### **应用场景**

1、由于 hash 数据类型的 key-value 的特性，用来存储关系型数据库中表记录，是 redis 中哈希类型最常用的场景。一条记录作为一个 key-value，把每列属性值对应成 field-value 存储在哈希表当中，然后通过 key 值来区分表当中的主键。

  

2、经常被用来存储用户相关信息。优化用户信息的获取，不需要重复从数据库当中读取，提高系统性能。

**四  五大基本类型底层数据存储结构**
---------------------

在学习基本类型底层数据存储结构前，首先看下 redis 整体的存储结构。

  

redis 内部整体的存储结构是一个大的 hashmap，内部是数组实现的 hash，key 冲突通过挂链表去实现，每个 dictEntry 为一个 key/value 对象，value 为定义的 redisObject。

  

结构图如下：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQe9ibaK43iaP6wnG49O59UAAC55p44jP9kXbbjSwEUlic1ZRgI2YfqgHSmg/640?wx_fmt=png)

  

dictEntry 是存储 key->value 的地方，再让我们看一下 dictEntry 结构体

  

```
/*
 * 字典
 */
typedef struct dictEntry {
    // 键
    void *key;
    // 值
    union {
        // 指向具体redisObject
        void *val;
        // 
        uint64_t u64;
        int64_t s64;
    } v;
    // 指向下个哈希表节点，形成链表
    struct dictEntry *next;
} dictEntry;

```

### 1  redisObject

我们接着再往下看 redisObject 究竟是什么结构的

  

```
/*
 * Redis 对象
 */
typedef struct redisObject {
    // 类型 4bits
    unsigned type:4;
    // 编码方式 4bits
    unsigned encoding:4;
    // LRU 时间（相对于 server.lruclock） 24bits
    unsigned lru:22;
    // 引用计数 Redis里面的数据可以通过引用计数进行共享 32bits
    int refcount;
    // 指向对象的值 64-bit
    void *ptr;
} robj;

```

*ptr 指向具体的数据结构的地址；type 表示该对象的类型，即 String,List,Hash,Set,Zset 中的一个，但为了提高存储效率与程序执行效率，每种对象的底层数据结构实现都可能不止一种，encoding 表示对象底层所使用的编码。

  

redis 对象底层的八种数据结构

  

```
 REDIS_ENCODING_INT（long 类型的整数）
 REDIS_ENCODING_EMBSTR embstr （编码的简单动态字符串）
 REDIS_ENCODING_RAW （简单动态字符串）
 REDIS_ENCODING_HT （字典）
 REDIS_ENCODING_LINKEDLIST （双端链表）
 REDIS_ENCODING_ZIPLIST （压缩列表）
 REDIS_ENCODING_INTSET （整数集合）
 REDIS_ENCODING_SKIPLIST （跳跃表和字典）

```

好了，通过 redisObject 就可以具体指向 redis 数据类型了，总结一下每种数据类型都使用了哪些数据结构，如下图所示

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQebZeEpHdvvkAbz5unmDBHVJSyU0G6FFkrQfv60demHu9fzoTNQsC0Ig/640?wx_fmt=png)

  

  

前期准备知识已准备完毕，下面分每种基本类型来讲。

  

### 2  String 数据结构

String 类型的转换顺序

  

*   当保存的值为整数且值的大小不超过 long 的范围，使用整数存储
    

  

*   当字符串长度不超过 44 字节时，使用 EMBSTR 编码
    

  

它只分配一次内存空间，redisObject 和 sds 是连续的内存，查询效率会快很多，也正是因为 redisObject 和 sds 是连续在一起，伴随了一些缺点：当字符串增加的时候，它长度会增加，这个时候又需要重新分配内存，导致的结果就是整个 redisObject 和 sds 都需要重新分配空间，这样是会影响性能的，所以 redis 用 embstr 实现一次分配而后, 只允许读，如果修改数据，那么它就会转成 raw 编码，不再用 embstr 编码了。

  

*   大于 44 字符时，使用 raw 编码
    

#### **SDS**

embstr 和 raw 都为 sds 编码，看一下 sds 的结构体

  

```
/* 针对不同长度整形做了相应的数据结构
 * Note: sdshdr5 is never used, we just access the flags byte directly.
 * However is here to document the layout of type 5 SDS strings. 
 */
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};

struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};

struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};

struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};

struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};

```

由于 redis 底层使用 c 语言实现，可能会有疑问为什么不用 c 语言的字符串呢，而是用 sds 结构体。

  

1) 低复杂度获取字符串长度：由于 len 存在，可以直接查出字符串长度，复杂度 O(1)；如果用 c 语言字符串，查询字符串长度需要遍历整个字符串，复杂度为 O(n)；

  

2) 避免缓冲区溢出：进行两个字符串拼接 c 语言可使用 strcat 函数，但如果没有足够的内存空间。就会造成缓冲区溢出；而用 sds 在进行合并时会先用 len 检查内存空间是否满足需求，如果不满足，进行空间扩展，不会造成缓冲区溢出；

  

3）减少修改字符串的内存重新分配次数：c 语言字符串不记录字符串长度，如果要修改字符串要重新分配内存，如果不进行重新分配会造成内存缓冲区泄露；

  

redis sds 实现了空间预分配和惰性空间释放两种策略

  

空间预分配：

  

1）如果 sds 修改后，sds 长度（len 的值）将于 1mb，那么会分配与 len 相同大小的未使用空间，此时 len 与 free 值相同。例如，修改之后字符串长度为 100 字节，那么会给分配 100 字节的未使用空间。最终 sds 空间实际为 100 + 100 + 1(保存空字符'\0')；

  

2）如果大于等于 1mb，每次给分配 1mb 未使用空间

惰性空间释放：对字符串进行缩短操作时，程序不立即使用内存重新分配来回收缩短后多余的字节，而是使用 free 属性将这些字节的数量记录下来，等待后续使用（sds 也提供 api，我们可以手动触发字符串缩短）；

  

4）二进制安全：因为 C 字符串以空字符作为字符串结束的标识，而对于一些二进制文件（如图片等），内容可能包括空字符串，因此 C 字符串无法正确存取；而所有 SDS 的 API 都是以处理二进制的方式来处理 buf 里面的元素，并且 SDS 不是以空字符串来判断是否结束，而是以 len 属性表示的长度来判断字符串是否结束；

  

5）遵从每个字符串都是以空字符串结尾的惯例，这样可以重用 C 语言库 <string.h> 中的一部分函数。

学习完 sds，我们回到上面讲到的，为什么小于 44 字节用 embstr 编码呢？

  

再看一下 rejectObject 和 sds 定义的结构（短字符串的 embstr 用最小的 sdshdr8）

  

```
typedef struct redisObject {
    // 类型 4bits
    unsigned type:4;
    // 编码方式 4bits
    unsigned encoding:4;
    // LRU 时间（相对于 server.lruclock） 24bits
    unsigned lru:22;
    // 引用计数 Redis里面的数据可以通过引用计数进行共享 32bits
    int refcount;
    // 指向对象的值 64-bit
    void *ptr;
} robj;

```

```
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};

```

redisObject 占用空间

  

 4 + 4 + 24 + 32 + 64 = 128bits = 16 字节

  

sdshdr8 占用空间

  

1（uint8_t） + 1（uint8_t）+ 1 （unsigned char）+ 1（buf[] 中结尾的'\0'字符）= 4 字节

  

初始最小分配为 64 字节，所以只分配一次空间的 embstr 最大为 64 - 16- 4 = 44 字节

  

### 3  List 存储结构

1.  Redis3.2 之前的底层实现方式：压缩列表 ziplist 或者 双向循环链表 linkedlist
    
      
    

当 list 存储的数据量比较少且同时满足下面两个条件时，list 就使用 ziplist 存储数据：

  

*   list 中保存的每个元素的长度小于 64 字节；
    
*   列表中数据个数少于 512 个
    

  

2.  Redis3.2 及之后的底层实现方式：quicklist

  

quicklist 是一个双向链表，而且是一个基于 ziplist 的双向链表，quicklist 的每个节点都是一个 ziplist，结合了双向链表和 ziplist 的优点。

  

#### **ziplist**

ziplist 是一种压缩链表，它的好处是更能节省内存空间，因为它所存储的内容都是在连续的内存区域当中的。当列表对象元素不大，每个元素也不大的时候，就采用 ziplist 存储。但当数据量过大时就 ziplist 就不是那么好用了。因为为了保证他存储内容在内存中的连续性，插入的复杂度是 O(N)，即每次插入都会重新进行 realloc。如下图所示，redisObject 对象结构中 ptr 所指向的就是一个 ziplist。整个 ziplist 只需要 malloc 一次，它们在内存中是一块连续的区域。

  

ziplist 结构如下：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQenKx4s7APSJrjxmTnzSlgmFiaPjCOjRWHyTM2w8t1QghibTFbCLE7LzWw/640?wx_fmt=png)

1、zlbytes: 用于记录整个压缩列表占用的内存字节数

　  

2、zltail：记录要列表尾节点距离压缩列表的起始地址有多少字节

3、zllen：记录了压缩列表包含的节点数量。  

  

4、entryX：要说列表包含的各个节点

  

5、zlend：用于标记压缩列表的末端

  

为什么数据量大时不用 ziplist？

  

因为 ziplist 是一段连续的内存，插入的时间复杂化度为 O(n)，而且每当插入新的元素需要 realloc 做内存扩展；而且如果超出 ziplist 内存大小，还会做重新分配的内存空间，并将内容复制到新的地址。如果数量大的话，重新分配内存和拷贝内存会消耗大量时间。所以不适合大型字符串，也不适合存储量多的元素。

  

#### **快速列表（quickList）**

快速列表是 ziplist 和 linkedlist 的混合体，是将 linkedlist 按段切分，每一段用 ziplist 来紧凑存储，多个 ziplist 之间使用双向指针链接。

  

为什么不直接使用 linkedlist？

  

linkedlist 的附加空间相对太高，prev 和 next 指针就要占去 16 个字节，而且每一个结点都是单独分配，会加剧内存的碎片化，影响内存管理效率。

  

quicklist 结构

  

```
typedef struct quicklist {
    // 指向quicklist的头部
    quicklistNode *head;
    // 指向quicklist的尾部
    quicklistNode *tail;
    unsigned long count;
    unsigned int len;
    // ziplist大小限定，由list-max-ziplist-size给定
    int fill : 16;
    // 节点压缩深度设置，由list-compress-depth给定
    unsigned int compress : 16;
} quicklist;

typedef struct quicklistNode {
    // 指向上一个ziplist节点
    struct quicklistNode *prev;
    // 指向下一个ziplist节点
    struct quicklistNode *next;
    // 数据指针，如果没有被压缩，就指向ziplist结构，反之指向quicklistLZF结构
    unsigned char *zl;
    // 表示指向ziplist结构的总长度(内存占用长度)
    unsigned int sz;
    // ziplist数量
    unsigned int count : 16;     /* count of items in ziplist */
    unsigned int encoding : 2;   /* RAW==1 or LZF==2 */
    // 预留字段，存放数据的方式，1--NONE，2--ziplist
    unsigned int container : 2;  /* NONE==1 or ZIPLIST==2 */
    // 解压标记，当查看一个被压缩的数据时，需要暂时解压，标记此参数为1，之后再重新进行压缩
    unsigned int recompress : 1; /* was this node previous compressed? */
    unsigned int attempted_compress : 1; /* node can't compress; too small */
    // 扩展字段
    unsigned int extra : 10; /* more bits to steal for future usage */
} quicklistNode;

typedef struct quicklistLZF {
    // LZF压缩后占用的字节数
    unsigned int sz; /* LZF size in bytes*/
    // 柔性数组，存放压缩后的ziplist字节数组
    char compressed[];
} quicklistLZF;

```

结构图如下：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQe4ZhEGl3GvPmAUrbKuibXRwGewicyrPzIL8AyVJvNpFUMNoFyKouCibIZQ/640?wx_fmt=png)

  

ziplist 的长度

  

quicklist 内部默认单个 ziplist 长度为 8k 字节，超出了这个字节数，就会新起一个 ziplist。关于长度可以使用 list-max-ziplist-size 决定。

  

压缩深度

  

我们上面说到了 quicklist 下是用多个 ziplist 组成的，同时为了进一步节约空间，Redis 还会对 ziplist 进行压缩存储，使用 LZF 算法压缩，可以选择压缩深度。quicklist 默认的压缩深度是 0，也就是不压缩。压缩的实际深度由配置参数 list-compress-depth 决定。为了支持快速 push/pop 操作，quicklist 的首尾两个 ziplist 不压缩，此时深度就是 1。如果深度为 2，就表示 quicklist 的首尾第一个 ziplist 以及首尾第二个 ziplist 都不压缩。

### 4  Hash 类型

当 Hash 中数据项比较少的情况下，Hash 底层才用压缩列表 ziplist 进行存储数据，随着数据的增加，底层的 ziplist 就可能会转成 dict，具体配置如下

  

```
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

```

在 List 中已经介绍了 ziplist，下面来介绍下 dict。

  

看下数据结构

  

```
typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    int iterators; /* number of iterators currently running */
} dict;

typedef struct dictht {
    //指针数组，这个hash的桶
    dictEntry **table;
    //元素个数
    unsigned long size;
    unsigned long sizemask;
    unsigned long used;
} dictht;

dictEntry大家应该熟悉，在上面有讲，使用来真正存储key->value的地方
typedef struct dictEntry {
    // 键
    void *key;
    // 值
    union {
        // 指向具体redisObject
        void *val;
        // 
        uint64_t u64;
        int64_t s64;
    } v;
    // 指向下个哈希表节点，形成链表
    struct dictEntry *next;
} dictEntry;

```

我们可以看到每个 dict 中都有两个 hashtable

  

结构图如下：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeibA3jqJXwxZYXwrAyKmTfia17fX8TT9eYsq5C4APzT4LaTibFGoictOaEg/640?wx_fmt=png)

  

虽然 dict 结构有两个 hashtable，但是通常情况下只有一个 hashtable 是有值的。但是在 dict 扩容缩容的时候，需要分配新的 hashtable，然后进行渐近式搬迁，这时候两个 hashtable 存储的旧的 hashtable 和新的 hashtable。搬迁结束后，旧 hashtable 删除，新的取而代之。

  

下面让我们学习下 rehash 全貌。

  

### 5  渐进式 rehash

所谓渐进式 rehash 是指我们的大字典的扩容是比较消耗时间的，需要重新申请新的数组，然后将旧字典所有链表的元素重新挂接到新的数组下面，是一个 O(n) 的操作。但是因为我们的 redis 是单线程的，无法承受这样的耗时过程，所以采用了渐进式 rehash 小步搬迁，虽然慢一点，但是可以搬迁完毕。

  

扩容条件

  

我们的扩容一般会在 Hash 表中的元素个数等于第一维数组的长度的时候，就会开始扩容。扩容的大小是原数组的两倍。不过在 redis 在做 bgsave（RDB 持久化操作的过程），为了减少内存页的过多分离（Copy On Write），redis 不会去扩容。但是如果 hash 表的元素个数已经到达了第一维数组长度的 5 倍的时候，就会强制扩容，不管你是否在持久化。

  

不扩容主要是为了尽可能减少内存页过多分离，系统需要更多的开销去回收内存。

  

缩容条件

  

当我们的 hash 表元素逐渐删除的越来越少的时候。redis 于是就会对 hash 表进行缩容来减少第一维数组长度的空间占用。缩容的条件是元素个数低于数组长度的 10%，并且缩容不考虑是否在做 redis 持久化。

  

不用考虑 bgsave 主要是因为我们的缩容的内存都是已经使用过的，缩容的时候可以直接置空，而且由于申请的内存比较小，同时会释放掉一些已经使用的内存，不会增大系统的压力。

  

rehash 步骤

  

1、为 ht[1] 分配空间，让字典同时持有 ht[0] 和 ht[1] 两个哈希表；

  

2、定时维持一个索引计数器变量 rehashidx，并将它的值设置为 0，表示 rehash 开始；

  

3、在 rehash 进行期间，每次对字典执行 CRUD 操作时，程序除了执行指定的操作以外，还会将 ht[0] 中的数据 rehash 到 ht[1] 表中，并且将 rehashidx 加一；

  

4、当 ht[0] 中所有数据转移到 ht[1] 中时，将 rehashidx 设置成 - 1，表示 rehash 结束；

  

（采用渐进式 rehash 的好处在于它采取分而治之的方式，避免了集中式 rehash 带来的庞大计算量。特别的在进行 rehash 时只能对 h[0] 元素减少的操作，如查询和删除；而查询是在两个哈希表中查找的，而插入只能在 ht[1] 中进行，ht[1] 也可以查询和删除。）

  

5、将 ht[0] 释放，然后将 ht[1] 设置成 ht[0]，最后为 ht[1] 分配一个空白哈希表。

  

过程如下图：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeZvpGfYXvagLAe6cnh88a9qkaBPzhINZrK0skHm04zSnIB69ia0C13rQ/640?wx_fmt=png)

###   

### 6  set 数据结构

Redis 的集合相当于 Java 中的 HashSet，它内部的键值对是无序、唯一的。它的内部实现相当于一个特殊的字典，字典中所有的 value 都是一个值 NULL。集合 Set 类型底层编码包括 hashtable 和 inset。

  

当存储的数据同时满足下面这样两个条件的时候，Redis 就采用整数集合 intset 来实现 set 这种数据类型：

  

*   存储的数据都是整数
    
*   存储的数据元素个数小于 512 个
    

  

当不能同时满足这两个条件的时候，Redis 就使用 dict 来存储集合中的数据

  

hashtable 在上面介绍过了，我们就只介绍 inset。

  

#### **inset 结构体**

```
typedef struct intset {
    uint32_t encoding;
    // length就是数组的实际长度
    uint32_t length;
    // contents 数组是实际保存元素的地方，数组中的元素有以下两个特性：
    // 1.没有重复元素
    // 2.元素在数组中从小到大排列
    int8_t contents[];
} intset;

// encoding 的值可以是以下三个常量的其中一个
#define INTSET_ENC_INT16 (sizeof(int16_t))
#define INTSET_ENC_INT32 (sizeof(int32_t))
#define INTSET_ENC_INT64 (sizeof(int64_t))

```

inset 的查询

  

intset 是一个有序集合，查找元素的复杂度为 O(logN)（采用二分法），但插入时不一定为 O(logN)，因为有可能涉及到升级操作。比如当集合里全是 int16_t 型的整数，这时要插入一个 int32_t，那么为了维持集合中数据类型的一致，那么所有的数据都会被转换成 int32_t 类型，涉及到内存的重新分配，这时插入的复杂度就为 O(N) 了。是 intset 不支持降级操作。

  

**inset 是有序不要和我们 zset 搞混，zset 是设置一个 score 来进行排序，而 inset 这里只是单纯的对整数进行升序而已 **

### 7  Zset 数据结构

Zset 有序集合和 set 集合有着必然的联系，他保留了集合不能有重复成员的特性，但不同的是，有序集合中的元素是可以排序的，但是它和列表的使用索引下标作为排序依据不同的是，它给每个元素设置一个分数，作为排序的依据。

  

zet 的底层编码有两种数据结构，一个 ziplist，一个是 skiplist。

  

Zset 也使用了 ziplist 做了排序，所以下面讲一下 ziplist 如何做排序。

#### 

#### **ziplist 做排序**

每个集合元素使用两个紧挨在一起的压缩列表节点来保存，第一个节点保存元素的成员（member），而第二个元素则保存元素的分值（score）。

  

存储结构图如下一目了然：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeDjF0LV8VWhCISGuPZlPEpq4ZsbUwl2vZTnWA5QeDd610njHiac3p7Qw/640?wx_fmt=png)

#### 

#### **skiplist 跳表**

结构体如下，skiplist 是与 dict 结合来使用的，这个结构比较复杂。

  

```
/*
 * 跳跃表
 */
typedef struct zskiplist {
    // 头节点，尾节点
    struct zskiplistNode *header, *tail;
    // 节点数量
    unsigned long length;
    // 目前表内节点的最大层数
    int level;
} zskiplist;

/*
 * 跳跃表节点
 */
typedef struct zskiplistNode {
    // member 对象
    robj *obj;
    // 分值
    double score;
    // 后退指针
    struct zskiplistNode *backward;
    // 层
    struct zskiplistLevel {
        // 前进指针
        struct zskiplistNode *forward;
        // 这个层跨越的节点数量
        unsigned int span;
    } level[];
} zskiplistNode;

```

跳表是什么？

  

我们先看下链表

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeG5AGMQPzGQ6UjAn8g6e38CCWHdJcjX9GbLPonjwPLLRBLPicm1Pt1JA/640?wx_fmt=png)

  

如果想查找到 node5 需要从 node1 查到 node5，查询耗时

  

但如果在 node 上加上索引：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQejuBkaibIib0TibYwibrMhyicab2W3ajzDWdvg3u4hgN1H2Fxuy2ysciajqhQ/640?wx_fmt=png)

  

这样通过索引就能直接从 node1 查找到 node5

  

#### **redis 跳跃表**

让我们再看下 redis 的跳表结构（图太复杂，直接从网上找了张图说明）

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQe1tPZa64s5Pf5tMcicVS3XTQ92EIT2C7cVY0HRqDo0mgEybKq1GKdUdA/640?wx_fmt=png)

*   header: 指向跳跃表的表头节点，通过这个指针程序定位表头节点的时间复杂度就为 O(1)；
    

  

*   tail: 指向跳跃表的表尾节点, 通过这个指针程序定位表尾节点的时间复杂度就为 O(1)；
    

  

*   level: 记录目前跳跃表内, 层数最大的那个节点的层数 (表头节点的层数不计算在内)，通过这个属性可以再 O(1) 的时间复杂度内获取层高最好的节点的层数；
    

  

*   length: 记录跳跃表的长度, 也即是, 跳跃表目前包含节点的数量 (表头节点不计算在内)，通过这个属性，程序可以再 O(1) 的时间复杂度内返回跳跃表的长度。
    

  

结构右方的是四个 zskiplistNode 结构, 该结构包含以下属性

  

*   层 (level):
    

  

节点中用 L1、L2、L3 等字样标记节点的各个层, L1 代表第一层, L2 代表第二层, 以此类推。

  

每个层都带有两个属性: 前进指针和跨度。前进指针用于访问位于表尾方向的其他节点, 而跨度则记录了前进指针所指向节点和当前节点的距离 (跨度越大、距离越远)。在上图中, 连线上带有数字的箭头就代表前进指针, 而那个数字就是跨度。当程序从表头向表尾进行遍历时, 访问会沿着层的前进指针进行。

  

每次创建一个新跳跃表节点的时候, 程序都根据幂次定律 (powerlaw, 越大的数出现的概率越小) 随机生成一个介于 1 和 32 之间的值作为 level 数组的大小, 这个大小就是层的“高度”。

  

*   后退 (backward) 指针：
    

  

节点中用 BW 字样标记节点的后退指针, 它指向位于当前节点的前一个节点。后退指针在程序从表尾向表头遍历时使用。与前进指针所不同的是每个节点只有一个后退指针，因此每次只能后退一个节点。

  

*   分值 (score):
    

  

各个节点中的 1.0、2.0 和 3.0 是节点所保存的分值。在跳跃表中, 节点按各自所保存的分值从小到大排列。

  

*   成员对象 (oj):
    

  

各个节点中的 o1、o2 和 o3 是节点所保存的成员对象。在同一个跳跃表中, 各个节点保存的成员对象必须是唯一的, 但是多个节点保存的分值却可以是相同的: 分值相同的节点将按照成员对象在字典序中的大小来进行排序, 成员对象较小的节点会排在前面 (靠近表头的方向), 而成员对象较大的节点则会排在后面 (靠近表尾的方向)。

  

**五  三大特殊数据类型**
---------------

### 1  geospatial（地理位置）

```
1.geospatial将指定的地理空间位置（纬度、经度、名称）添加到指定的key中。
  这些数据将会存储到sorted set这样的目的是为了方便使用GEORADIUS或者GEORADIUSBYMEMBER命令对数据进行半径查询等操作。
2.sorted set使用一种称为Geohash的技术进行填充。经度和纬度的位是交错的，以形成一个独特的52位整数。
  sorted set的double score可以代表一个52位的整数，而不会失去精度。（有兴趣的同学可以学习一下Geohash技术，使用二分法构建唯一的二进制串）
3.有效的经度是-180度到180度
  有效的纬度是-85.05112878度到85.05112878度

```

#### **应用场景**

1.  查看附近的人
    
2.  微信位置共享
    
3.  地图上直线距离的展示
    

###   

### 2  Hyperloglog（基数）

什么是基数？ 不重复的元素

  

```
hyperloglog 是用来做基数统计的，其优点是：输入的提及无论多么大，hyperloglog使用的空间总是固定的12KB ，利用12KB，它可以计算2^64个不同元素的基数！非常节省空间！但缺点是估算的值，可能存在误差

```

#### **应用场景**

1.  网页统计 UV （浏览用户数量，同一天同一个 ip 多次访问算一次访问，目的是计数，而不是保存用户）
    

  

传统的方式，set 保存用户的 id，可以统计 set 中元素数量作为标准判断。

  

但如果这种方式保存大量用户 id，会占用大量内存，我们的目的是为了计数，而不是去保存 id。

### 3  Bitmaps（位存储）

```
Redis提供的Bitmaps这个“数据结构”可以实现对位的操作。Bitmaps本身不是一种数据结构，实际上就是字符串，但是它可以对字符串的位进行操作。
可以把Bitmaps想象成一个以位为单位数组，数组中的每个单元只能存0或者1，数组的下标在bitmaps中叫做偏移量。单个bitmaps的最大长度是512MB，即2^32个比特位。

```

#### **应用场景**

两种状态的统计都可以使用 bitmaps，例如：统计用户活跃与非活跃数量、登录与非登录、上班打卡等等。

**六  Redis 事务**
---------------

事务本质：一组命令的集合

### 1  数据库事务与 redis 事务

#### **数据库的事务**

数据库事务通过 ACID（原子性、一致性、隔离性、持久性）来保证。

  

数据库中除查询操作以外，插入 (Insert)、删除(Delete) 和更新 (Update) 这三种操作都会对数据造成影响，因为事务处理能够保证一系列操作可以完全地执行或者完全不执行，因此在一个事务被提交以后，该事务中的任何一条 SQL 语句在被执行的时候，都会生成一条撤销日志(Undo Log)。

  

#### **redis 事务**

redis 事务提供了一种 “将多个命令打包， 然后一次性、按顺序地执行” 的机制， 并且事务在执行的期间不会主动中断 —— 服务器在执行完事务中的所有命令之后， 才会继续处理其他客户端的其他命令。

  

Redis 中一个事务从开始到执行会经历开始事务（muiti）、命令入队和执行事务 (exec) 三个阶段，事务中的命令在加入时都没有被执行，直到提交时才会开始执行 (Exec) 一次性完成。

一组命令中存在两种错误不同处理方式

  

1. 代码语法错误（编译时异常）所有命令都不执行

  

2. 代码逻辑错误（运行时错误），其他命令可以正常执行  （该点不保证事务的原子性）

为什么 redis 不支持回滚来保证原子性

  

这种做法的优点：

  

*   Redis 命令只会因为错误的语法而失败（并且这些问题不能在入队时发现），或是命令用在了错误类型的键上面：这也就是说，从实用性的角度来说，失败的命令是由编程错误造成的，而这些错误应该在开发的过程中被发现，而不应该出现在生产环境中。
    

  

*   因为不需要对回滚进行支持，所以 Redis 的内部可以保持简单且快速。
    

** 鉴于没有任何机制能避免程序员自己造成的错误， 并且这类错误通常不会在生产环境中出现， 所以 Redis 选择了更简单、更快速的无回滚方式来处理事务。

  

### 事务监控

悲观锁：认为什么时候都会出现问题，无论做什么操作都会加锁。

  

乐观锁：认为什么时候都不会出现问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据。

  

使用 cas 实现乐观锁

  

redis 使用 watch key 监控指定数据，相当于加乐观锁

watch 保证事务只能在所有被监视键都没有被修改的前提下执行， 如果这个前提不能满足的话，事务就不会被执行。

  

watch 执行流程

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQecXfKj7L87mdOibJmPCBZraY1Q4kUWkXstjex42gTfoKw7hxKUTwtCRQ/640?wx_fmt=png)

  

---

**七  Redis 持久化**  

-------------------

Redis 是一种内存型数据库，一旦服务器进程退出，数据库的数据就会丢失，为了解决这个问题 Redis 供了两种持久化的方案，将内存中的数据保存到磁盘中，避免数据的丢失

两种持久化方式：快照（RDB 文件）和追加式文件（AOF 文件），下面分别为大家介绍两种方式的原理。

*   RDB 持久化方式会在一个特定的间隔保存那个时间点的数据快照。
    

  

*   AOF 持久化方式则会记录每一个服务器收到的写操作。在服务启动时，这些记录的操作会逐条执行从而重建出原来的数据。写操作命令记录的格式跟 Redis 协议一致，以追加的方式进行保存。
    

  

*   Redis 的持久化是可以禁用的，就是说你可以让数据的生命周期只存在于服务器的运行时间里。
    

  

*   两种方式的持久化是可以同时存在的，但是当 Redis 重启时，AOF 文件会被优先用于重建数据。
    

### 1  RDB 持久化

RDB 持久化产生的文件是一个经过压缩的二进制文件，这个文件可以被保存到硬盘中，可以通过这个文件还原数据库的状态，它可以手动执行，也可以在 redis.conf 配置文件中配置，定时执行。

#### **工作原理**

在进行 RDB 时，redis 的主进程不会做 io 操作，会 fork 一个子进程来完成该操作：

  

1.  Redis 调用 forks。同时拥有父进程和子进程。
    
      
    
2.  子进程将数据集写入到一个临时 RDB 文件中。
    
      
    
3.  当子进程完成对新 RDB 文件的写入时，Redis 用新 RDB 文件替换原来的 RDB 文件，并删除旧的 RDB 文件。
    
      
    

这种工作方式使得 Redis 可以从写时复制（copy-on-write）机制中获益 (因为是使用子进程进行写操作，而父进程依然可以接收来自客户端的请求)

#### **触发机制**

在 Redis 中 RDB 持久化的触发分为两种：自己手动触发与自动触发。

主动触发

  

1.  save 命令是同步的命令，会占用主进程，会造成阻塞，阻塞所有客户端的请求
    

2.  bgsave
    
      
    

bgsave 是异步进行，进行持久化的时候，redis 还可以将继续响应客户端请求 

bgsave 和 save 对比

  

<table width="752"><tbody><tr><td colspan="1" rowspan="1"><section>命令</section></td><td colspan="1" rowspan="1"><section>save</section></td><td colspan="1" rowspan="1"><section>bgsave</section></td></tr><tr><td colspan="1" rowspan="1"><section>IO 类型</section></td><td colspan="1" rowspan="1"><section>同步</section></td><td colspan="1" rowspan="1"><section>异步</section></td></tr><tr><td colspan="1" rowspan="1"><section>阻塞</section></td><td colspan="1" rowspan="1"><section>是</section></td><td colspan="1" rowspan="1"><section>是（阻塞发生在 fock()，通常非常快）</section></td></tr><tr><td colspan="1" rowspan="1"><section>复杂度</section></td><td colspan="1" rowspan="1"><section>O(n)</section></td><td colspan="1" rowspan="1"><section>O(n)</section></td></tr><tr><td colspan="1" rowspan="1"><section>优点</section></td><td colspan="1" rowspan="1"><section>不会消耗额外的内存</section></td><td colspan="1" rowspan="1"><section>不阻塞客户端命令</section></td></tr><tr><td colspan="1" rowspan="1"><section>缺点</section></td><td colspan="1" rowspan="1"><section>阻塞客户端命令</section></td><td colspan="1" rowspan="1"><section>需要 fock 子进程，消耗内存</section></td></tr></tbody></table>

  

自动触发

  

1.  save 自动触发配置，见下面配置，满足 m 秒内修改 n 次 key，触发 rdb
    
      
    

```
# 时间策略   save m n m秒内修改n次key，触发rdb
save 900 1
save 300 10
save 60 10000

# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /home/work/app/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes

```

2.  从节点全量复制时，主节点发送 rdb 文件给从节点完成复制操作，主节点会触发 bgsave 命令；
    
      
    
3.  执行 flushall 命令，会触发 rdb
    
      
    
4.  退出 redis，且没有开启 aof 时
    

优点：

  

1.  RDB 的内容为二进制的数据，占用内存更小，更紧凑，更适合做为备份文件；
    
      
    
2.  RDB 对灾难恢复非常有用，它是一个紧凑的文件，可以更快的传输到远程服务器进行 Redis 服务恢复；
    
      
    
3.  RDB 可以更大程度的提高 Redis 的运行速度，因为每次持久化时 Redis 主进程都会 fork() 一个子进程，进行数据持久化到磁盘，Redis 主进程并不会执行磁盘 I/O 等操作；
    
      
    
4.  与 AOF 格式的文件相比，RDB 文件可以更快的重启。
    
      
    

缺点：

  

1.  因为 RDB 只能保存某个时间间隔的数据，如果中途 Redis 服务被意外终止了，则会丢失一段时间内的 Redis 数据。
    
      
    
2.  RDB 需要经常 fork() 才能使用子进程将其持久化在磁盘上。如果数据集很大，fork() 可能很耗时，并且如果数据集很大且 CPU 性能不佳，则可能导致 Redis 停止为客户端服务几毫秒甚至一秒钟。
    

### 2  AOF（Append Only File）

以日志的形式来记录每个写的操作，将 Redis 执行过的所有指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis 启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

#### **AOF 配置项**

```
# 默认不开启aof  而是使用rdb的方式
appendonly no

# 默认文件名
appendfilename "appendonly.aof"

# 每次修改都会sync 消耗性能
# appendfsync always
# 每秒执行一次 sync 可能会丢失这一秒的数据
appendfsync everysec
# 不执行 sync ,这时候操作系统自己同步数据，速度最快
# appendfsync no 

```

AOF 的整个流程大体来看可以分为两步，一步是命令的实时写入（如果是 appendfsync everysec 配置，会有 1s 损耗），第二步是对 aof 文件的重写。

  

#### **AOF 重写机制**

随着 Redis 的运行，AOF 的日志会越来越长，如果实例宕机重启，那么重放整个 AOF 将会变得十分耗时，而在日志记录中，又有很多无意义的记录，比如我现在将一个数据 incr 一千次，那么就不需要去记录这 1000 次修改，只需要记录最后的值即可。所以就需要进行 AOF 重写。

  

Redis 提供了 bgrewriteaof 指令用于对 AOF 日志进行重写，该指令运行时会开辟一个子进程对内存进行遍历，然后将其转换为一系列的 Redis 的操作指令，再序列化到一个日志文件中。完成后再替换原有的 AOF 文件，至此完成。

  

同样的也可以在 redis.config 中对重写机制的触发进行配置：

  

通过将 no-appendfsync-on-rewrite 设置为 yes，开启重写机制；auto-aof-rewrite-percentage 100 意为比上次从写后文件大小增长了 100% 再次触发重写；

  

auto-aof-rewrite-min-size 64mb 意为当文件至少要达到 64mb 才会触发制动重写。

#### **触发方式**

1.  手动触发：bgrewriteaof  
    
      
    
2.  自动触发 就是根据配置规则来触发，当然自动触发的整体时间还跟 Redis 的定时任务频率有关系。
    

优点

  

1、数据安全，aof 持久化可以配置 appendfsync 属性，有 always，每进行一次 命令操作就记录到 aof 文件中一次。

  

2、通过 append 模式写文件，即使中途服务器宕机，可以通过 redis-check-aof 工具解决数据一致性问题。

  

3、AOF 机制的 rewrite 模式。AOF 文件没被 rewrite 之前（文件过大时会对命令 进行合并重写），可以删除其中的某些命令（比如误操作的 flushall）)

缺点

  

1、AOF 文件比 RDB 文件大，且恢复速度慢。

2、数据集大的时候，比 rdb 启动效率低。

### 3  rdb 与 aof 对比

<table width="864"><tbody><tr><td colspan="1" rowspan="1"><section>比较项</section></td><td colspan="1" rowspan="1"><section>RDB</section></td><td colspan="1" rowspan="1"><section>AOF</section></td></tr><tr><td colspan="1" rowspan="1"><section>启动优先级</section></td><td colspan="1" rowspan="1"><section>低</section></td><td colspan="1" rowspan="1"><section>高</section></td></tr><tr><td colspan="1" rowspan="1"><section>体积</section></td><td colspan="1" rowspan="1"><section>小</section></td><td colspan="1" rowspan="1"><section>大</section></td></tr><tr><td colspan="1" rowspan="1"><section>恢复速度</section></td><td colspan="1" rowspan="1"><section>快</section></td><td colspan="1" rowspan="1"><section>慢</section></td></tr><tr><td colspan="1" rowspan="1"><section>数据安全性</section></td><td colspan="1" rowspan="1"><section>丢数据</section></td><td colspan="1" rowspan="1"><section>根据策略决定</section></td></tr></tbody></table>

**八  发布与订阅**
------------

redis 发布与订阅是一种消息通信的模式：发送者（pub）发送消息，订阅者（sub）接收消息。

  

redis 通过 PUBLISH 和 SUBSCRIBE 等命令实现了订阅与发布模式，这个功能提供两种信息机制，分别是订阅 / 发布到频道、订阅 / 发布到模式的客户端。

  

### 1  频道（channel）

#### **订阅**

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeD7qn9ctJrwe297uxicjXm6SQXA0TLicweNV1gZW51xBiazicPI8fJNd6Aw/640?wx_fmt=png)

#### 

#### **发布**

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeRlQjIkJ8E4FO8dia4aJYModRMia9EMlnsH4KRFt02Ixic5cewice22N0Kg/640?wx_fmt=png)

  

#### **完整流程**

发布者发布消息

  

发布者向频道 channel:1 发布消息 hi

  

```
127.0.0.1:6379> publish channel:1 hi
(integer) 1

```

订阅者订阅消息

  

```
127.0.0.1:6379> subscribe channel:1
Reading messages... (press Ctrl-C to quit)
1) "subscribe" // 消息类型
2) "channel:1" // 频道
3) "hi" // 消息内容

```

执行 subscribe 后客户端会进入订阅状态，仅可以使 subscribe、unsubscribe、psubscribe 和 punsubscribe 这四个属于 "发布 / 订阅" 之外的命令

  

订阅频道后的客户端可能会收到三种消息类型

  

*   subscribe。表示订阅成功的反馈信息。第二个值是订阅成功的频道名称，第三个是当前客户端订阅的频道数量。
    

  

*   message。表示接收到的消息，第二个值表示产生消息的频道名称，第三个值是消息的内容。
    

  

*   unsubscribe。表示成功取消订阅某个频道。第二个值是对应的频道名称，第三个值是当前客户端订阅的频道数量，当此值为 0 时客户端会退出订阅状态，之后就可以执行其他非 "发布 / 订阅" 模式的命令了。
    

  

#### **数据结构**

基于频道的发布订阅模式是通过字典数据类型实现的

  

```
struct redisServer {
    // ...
    dict *pubsub_channels;
    // ...
};

```

其中，字典的键为正在被订阅的频道， 而字典的值则是一个链表， 链表中保存了所有订阅这个频道的客户端。

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQerEibHMXvbrtkz3kTTkj0s31CCZoNwRJYDBEGqr78Z1hDic8FnpLCg2lw/640?wx_fmt=png)

  

订阅

  

当使用 subscribe 订阅时，在字典中找到频道 key（如没有则创建），并将订阅的 client 关联在链表后面。

  

当 client 10 执行 subscribe channel1 channel2 channel3 时，会将 client 10 分别加到 channel1 channel2 channel3 关联的链表尾部。

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeBptaII37mFOD3Xv9ht61YABefvD8H111TLBVypzouPud9Ra2Vzbwicw/640?wx_fmt=png)

  

发布

  

发布时，根据 key，找到字典汇总 key 的地址，然后将 msg 发送到关联的链表每一台机器。

  

退订

  

遍历关联的链表，将指定的地址删除即可。

  

### 2  模式（pattern）

pattern 使用了通配符的方式来订阅

  

通配符中? 表示 1 个占位符，* 表示任意个占位符 (包括 0)，?* 表示 1 个以上占位符。

  

所以当使用 publish 命令发送信息到某个频道时， 不仅所有订阅该频道的客户端会收到信息， 如果有某个 / 某些模式和这个频道匹配的话， 那么所有订阅这个 / 这些频道的客户端也同样会收到信息。

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeO8u87JyVkmXNSbuia8NJkHkKB7YBSCIFKrUwgRUgIMKPf2z8mcjpnJw/640?wx_fmt=png)

  

#### **订阅发布完整流程**

发布者发布消息

  

```
127.0.0.1:6379> publish b m1
(integer) 1
127.0.0.1:6379> publish b1 m1
(integer) 1
127.0.0.1:6379> publish b11 m1
(integer) 1

```

订阅者订阅消息

  

```
127.0.0.1:6379> psubscribe b*
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "b*"
3) (integer) 3
1) "pmessage"
2) "b*"
3) "b"
4) "m1"
1) "pmessage"
2) "b*"
3) "b1"
4) "m1"
1) "pmessage"
2) "b*"
3) "b11"
4) "m1"


```

#### **数据结构**

#### pattern 属性是一个链表，链表中保存着所有和模式相关的信息。

```
struct redisServer {
    // ...
    list *pubsub_patterns;
    // ...
};
// 链表中的每一个节点结构如下，保存着客户端与模式信息
typedef struct pubsubPattern {
    redisClient *client;
    robj *pattern;
} pubsubPattern;

```

数据结构图如下：

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeIW8ORVAKbyhIJhicXDoKSaLZBxRHbQjSvQmOLuOBRJGYxR58dXtbYbw/640?wx_fmt=png)

  

订阅

  

当有信的订阅时，会将订阅的客户端和模式信息添加到链表后面。

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeMt258TsBQQqAiaefdUjEkJLxW3ggxdH418SciabJBIibO6iaicWCwkEGVCw/640?wx_fmt=png)

  

发布

  

当发布者发布消息时，首先会发送到对应的频道上，在遍历模式列表，根据 key 匹配模式，匹配成功将消息发给对应的订阅者。

完成的发布伪代码如下

  

```
def PUBLISH(channel, message):
    # 遍历所有订阅频道 channel 的客户端
    for client in server.pubsub_channels[channel]:
        # 将信息发送给它们
        send_message(client, message)
    # 取出所有模式，以及订阅模式的客户端
    for pattern, client in server.pubsub_patterns:
        # 如果 channel 和模式匹配
        if match(channel, pattern):
            # 那么也将信息发给订阅这个模式的客户端
            send_message(client, message)

```

退订

  

使用 punsubscribe，可以将订阅者退订，将改客户端移除出链表。

  

**九  主从复制**
-----------

什么是主从复制

  

```
主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。
2.前者称为主节点(master)，后者称为从节点(slave)；数据的复制是单向的，只能由主节点到从节点
默认情况下，每台redis服务器都是主节点；且一个主节点可以有多个从节点（或者没有），但一个从节点只有一个主

```

主从复制的作用主要包括

  

*   数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
    

  

*   故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
    

  

*   负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写 Redis 数据时应用连接主节点，读 Redis 数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高 Redis 服务器的并发量。
    

  

*   高可用基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是 Redis 高可用的基础。
    

  

主从库采用的是读写分离的方式

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQedANPrcW3yf2HhKEZ3I3ZyW48iaEia45rbxR4Oxq1FwkQClDHFxxd0slA/640?wx_fmt=png)

###   

### 1  原理

分为全量复制与增量复制

  

全量复制：发生在第一次复制时

  

增量复制：只会把主从库网络断连期间主库收到的命令，同步给从库

### 2  全量复制的三个阶段

第一阶段是主从库间建立连接、协商同步的过程。

  

主要是为全量复制做准备。从库和主库建立起连接，并告诉主库即将进行同步，主库确认回复后，主从库间就可以开始同步了。

  

具体来说，从库给主库发送 psync 命令，表示要进行数据同步，主库根据这个命令的参数来启动复制。psync 命令包含了主库的 runID 和复制进度 offset 两个参数。runID，是每个 Redis 实例启动时都会自动生成的一个随机 ID，用来唯一标记这个实例。当从库和主库第一次复制时，因为不知道主库的 runID，所以将 runID 设为 “？”。offset，此时设为 -1，表示第一次复制。主库收到 psync 命令后，会用 FULLRESYNC 响应命令带上两个参数：主库 runID 和主库目前的复制进度 offset，返回给从库。从库收到响应后，会记录下这两个参数。这里有个地方需要注意，FULLRESYNC 响应表示第一次复制采用的全量复制，也就是说，主库会把当前所有的数据都复制给从库。

  

第二阶段，主库将所有数据同步给从库。

  

从库收到数据后，在本地完成数据加载。这个过程依赖于内存快照生成的 RDB 文件。

  

具体来说，主库执行 bgsave 命令，生成 RDB 文件，接着将文件发给从库。从库接收到 RDB 文件后，会先清空当前数据库，然后加载 RDB 文件。这是因为从库在通过 replicaof 命令开始和主库同步前，可能保存了其他数据。为了避免之前数据的影响，从库需要先把当前数据库清空。在主库将数据同步给从库的过程中，主库不会被阻塞，仍然可以正常接收请求。否则，Redis 的服务就被中断了。但是，这些请求中的写操作并没有记录到刚刚生成的 RDB 文件中。为了保证主从库的数据一致性，主库会在内存中用专门的 replication buffer，记录 RDB 文件生成后收到的所有写操作。

  

第三个阶段，主库会把第二阶段执行过程中新收到的写命令，再发送给从库。

  

具体的操作是，当主库完成 RDB 文件发送后，就会把此时 replication buffer 中的修改操作发给从库，从库再重新执行这些操作。这样一来，主从库就实现同步了。

**十  哨兵机制**
-----------

哨兵的核心功能是主节点的自动故障转移

  

下图是一个典型的哨兵集群监控的逻辑图

  

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeib7k9dSPNnjpfKzURHqUjTzdpoibnq1bAj5JKWHpg0kh2X7tYVLpD9BQ/640?wx_fmt=png)

   

Redis Sentinel 包含了若个 Sentinel 节点，这样做也带来了两个好处：

  

1.  对于节点的故障判断是由多个 Sentinel 节点共同完成，这样可以有效地防止误判
    
      
    
2.  即使个别 Sentinel 节点不可用，整个 Sentinel 集群依然是可用的。
    

  

哨兵实现了一下功能

  

1.  监控：每个 Sentinel 节点会对数据节点（Redis master/slave 节点）和其余 Sentinel 节点进行监控
    
      
    
2.  通知：Sentinel 节点会将故障转移的结果通知给应用方
    
      
    
3.  故障转移：实现 slave 晋升为 master，并维护后续正确的主从关系
    
      
    
4.  配置中心：在 Redis Sentinel 模式中，客户端在初始化的时候连接的是 Sentinel 节点集合，从中获取主节点信息
    
      
    

其中，监控和自动故障转移功能，使得哨兵可以及时发现主节点故障并完成转移；而配置中心和通知功能，则需要在与客户端的交互中才能体现。

  

### 1  原理

#### **监控**

Sentinel 节点需要监控 master、slave 以及其它 Sentinel 节点的状态。这一过程是通过 Redis 的 pub/sub 系统实现的。Redis Sentinel 一共有三个定时监控任务，完成对各个节点发现和监控：

  

1.  监控主从拓扑信息：每隔 10 秒，每个 Sentinel 节点，会向 master 和 slave 发送 INFO 命令获取最新的拓扑结构
    
      
    
2.  Sentinel 节点信息交换：每隔 2 秒，每个 Sentinel 节点，会向 Redis 数据节点的__sentinel__:hello 频道上，发送自身的信息，以及对主节点的判断信息。这样，Sentinel 节点之间就可以交换信息
    
      
    
3.  节点状态监控：每隔 1 秒，每个 Sentinel 节点，会向 master、slave、其余 Sentinel 节点发送 PING 命令做心跳检测，来确认这些节点当前是否可达
    
      
    

#### **主观 / 客观下线**

主观下线

  

每个 Sentinel 节点，每隔 1 秒会对数据节点发送 ping 命令做心跳检测，当这些节点超过 down-after-milliseconds 没有进行有效回复时，Sentinel 节点会对该节点做失败判定，这个行为叫做主观下线。

  

客观下线

  

客观下线，是指当大多数 Sentinel 节点，都认为 master 节点宕机了，那么这个判定就是客观的，叫做客观下线。

  

那么这个大多数是指多少呢？这其实就是分布式协调中的 quorum 判定了，大多数就是过半数，比如哨兵数量是 5，那么大多数就是 5/2+1=3 个，哨兵数量是 10 大多数就是 10/2+1=6 个。

  

注：Sentinel 节点的数量至少为 3 个，否则不满足 quorum 判定条件。

  

#### **哨兵选举**

如果发生了客观下线，那么哨兵节点会选举出一个 Leader 来进行实际的故障转移工作。Redis 使用了 Raft 算法来实现哨兵领导者选举，大致思路如下：

  

1.  每个 Sentinel 节点都有资格成为领导者，当它主观认为某个数据节点宕机后，会向其他 Sentinel 节点发送 sentinel is-master-down-by-addr 命令，要求自己成为领导者；
    
      
    
2.  收到命令的 Sentinel 节点，如果没有同意过其他 Sentinel 节点的 sentinelis-master-down-by-addr 命令，将同意该请求，否则拒绝（每个 Sentinel 节点只有 1 票）；
    
      
    
3.  如果该 Sentinel 节点发现自己的票数已经大于等于 MAX(quorum, num(sentinels)/2+1)，那么它将成为领导者；
    
      
    
4.  如果此过程没有选举出领导者，将进入下一次选举。
    

####   

#### **故障转移**

选举出的 Leader Sentinel 节点将负责故障转移，也就是进行 master/slave 节点的主从切换。故障转移，首先要从 slave 节点中筛选出一个作为新的 master，主要考虑以下 slave 信息：

  

1.  跟 master 断开连接的时长：如果一个 slave 跟 master 的断开连接时长已经超过了 down-after-milliseconds 的 10 倍，外加 master 宕机的时长，那么该 slave 就被认为不适合选举为 master；
    
      
    
2.  slave 的优先级配置：slave priority 参数值越小，优先级就越高；
    
      
    
3.  复制 offset：当优先级相同时，哪个 slave 复制了越多的数据（offset 越靠后），优先级越高；
    
      
    
4.  run id：如果 offset 和优先级都相同，则哪个 slave 的 run id 越小，优先级越高。
    
      
    

接着，筛选完 slave 后， 会对它执行 slaveof no one 命令，让其成为主节点。

  

最后，Sentinel 领导者节点会向剩余的 slave 节点发送命令，让它们成为新的 master 节点的从节点，复制规则与 parallel-syncs 参数有关。

  

Sentinel 节点集合会将原来的 master 节点更新为 slave 节点，并保持着对其关注，当其恢复后命令它去复制新的主节点。

  

注：Leader Sentinel 节点，会从新的 master 节点那里得到一个 configuration epoch，本质是个 version 版本号，每次主从切换的 version 号都必须是唯一的。其他的哨兵都是根据 version 来更新自己的 master 配置。

  

**十一  缓存穿透、击穿、雪崩**
------------------

### 1  缓存穿透

*   问题来源
    

  

缓存穿透是指缓存和数据库中都没有的数据，而用户不断发起请求。由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。在流量大时，可能 DB 就挂掉了，要是有人利用不存在的 key 频繁攻击我们的应用，这就是漏洞。

  

如发起为 id 为 “-1” 的数据或 id 为特别大不存在的数据。这时的用户很可能是攻击者，攻击会导致数据库压力过大。

  

*   解决方案
    
      
    

1.  接口层增加校验，如用户鉴权校验，id 做基础校验，id<=0 的直接拦截；
    
      
    
2.  从缓存取不到的数据，在数据库中也没有取到，这时也可以将 key-value 对写为 key-null，缓存有效时间可以设置短点，如 30 秒（设置太长会导致正常情况也没法使用）。这样可以防止攻击用户反复用同一个 id 暴力攻击。
    
      
    
3.  布隆过滤器。类似于一个 hash set，用于快速判某个元素是否存在于集合中，其典型的应用场景就是快速判断一个 key 是否存在于某容器，不存在就直接返回。布隆过滤器的关键就在于 hash 算法和容器大小。
    

###   

### 2  缓存击穿

*   问题来源
    

  

缓存击穿是指缓存中没有但数据库中有的数据（一般是缓存时间到期），这时由于并发用户特别多，同时读缓存没读到数据，又同时去数据库去取数据，引起数据库压力瞬间增大，造成过大压力。

  

*   解决方案
    

  

1、设置热点数据永远不过期。

  

2、接口限流与熔断，降级。重要的接口一定要做好限流策略，防止用户恶意刷接口，同时要降级准备，当接口中的某些服务不可用时候，进行熔断，失败快速返回机制。

  

3、加互斥锁

  

### 3  缓存雪崩

*   问题来源
    

  

缓存雪崩是指缓存中数据大批量到过期时间，而查询数据量巨大，引起数据库压力过大甚至 down 机。和缓存击穿不同的是，缓存击穿指并发查同一条数据，缓存雪崩是不同数据都过期了，很多数据都查不到从而查数据库。

  

*   解决方案
    

  

1.  缓存数据的过期时间设置随机，防止同一时间大量数据过期现象发生。
    
      
    
2.  如果缓存数据库是分布式部署，将热点数据均匀分布在不同的缓存数据库中。
    
      
    
3.  设置热点数据永远不过期
    

**文章最后**
--------

每一项技术深挖都是一个庞大的体系，学海无涯，共勉。

**阿里云新版云计算架构师 ACE 认证全面重构**

为建立云计算生态领域，含金量第一的专家级人才标准和认证体系，影响泛云生态高层次技术人才，阿里云认证对阿里云云计算架构师 ACE 认证进行了里程碑式重构！2022 年 3 月 28 日 14：00 来观看阿里云新版云计算架构师 ACE 认证重构发布会。改革重点：1、依据阿里云 P8 架构师能力模型进行设计，含金量大幅度提升。2、新增实验、面试环节，全部由阿里云 P9 技术专家组成考官团队。3、新增阿里云技术专家全球唯一编号 - ACE00001 开始排序。4、新增配套证书 & 纪念套装，包含定制化水晶杯，奖牌等。点击阅读原文查看详情！

![](https://mmbiz.qpic.cn/mmbiz_png/Z6bicxIx5naJHFiasCRvu4qykX5fCDNOQeuFfzPliaynaTC8P3AH6ibI5nrLXibiaRsIW3YEsqIsBxMuOIZwqRB5zH2g/640?wx_fmt=png)