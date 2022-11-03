# Redis
<!-- GFM-TOC -->
* [Redis](#redis)
    * [一、概述](#一概述)
    * [二、数据类型](#二数据类型)
        * [STRING](#string)
        * [LIST](#list)
        * [SET](#set)
        * [HASH](#hash)
        * [ZSET](#zset)
    * [三、数据结构](#三数据结构)
        * [字典](#字典)
        * [跳跃表](#跳跃表)
    * [四、使用场景](#四使用场景)
        * [计数器](#计数器)
        * [缓存](#缓存)
        * [查找表](#查找表)
        * [消息队列](#消息队列)
        * [会话缓存](#会话缓存)
        * [分布式锁实现](#分布式锁实现)
        * [其它](#其它)
    * [五、Redis 与 Memcached](#五redis-与-memcached)
        * [数据类型](#数据类型)
        * [数据持久化](#数据持久化)
        * [分布式](#分布式)
        * [内存管理机制](#内存管理机制)
    * [六、键的过期时间](#六键的过期时间)
    * [七、数据淘汰策略](#七数据淘汰策略)
    * [八、持久化](#八持久化)
        * [RDB 持久化](#rdb-持久化)
        * [AOF 持久化](#aof-持久化)
    * [九、事务](#九事务)
    * [十、事件](#十事件)
        * [文件事件](#文件事件)
        * [时间事件](#时间事件)
        * [事件的调度与执行](#事件的调度与执行)
    * [十一、复制](#十一复制)
        * [连接过程](#连接过程)
        * [主从链](#主从链)
    * [十二、Sentinel](#十二sentinel)
    * [十三、分片](#十三分片)
    * [十四、一个简单的论坛系统分析](#十四一个简单的论坛系统分析)
        * [文章信息](#文章信息)
        * [点赞功能](#点赞功能)
        * [对文章进行排序](#对文章进行排序)
    * [参考资料](#参考资料)
<!-- GFM-TOC -->


## 一、概述

Redis 是速度非常快的非关系型（NoSQL）内存键值数据库，可以存储键和五种不同类型的值之间的映射。

键的类型只能为字符串，值支持五种数据类型：字符串、列表、集合、散列表、有序集合。

Redis 支持很多特性，例如将内存中的数据持久化到硬盘中，使用复制来扩展读性能，使用分片来扩展写性能。

## 二、数据类型

| 数据类型 | 可以存储的值 | 操作 |
| :--: | :--: | :--: |
| STRING | 字符串、整数或者浮点数 | 对整个字符串或者字符串的其中一部分执行操作\</br\> 对整数和浮点数执行自增或者自减操作 |
| LIST | 列表 | 从两端压入或者弹出元素 \</br\> 对单个或者多个元素进行修剪，\</br\> 只保留一个范围内的元素 |
| SET | 无序集合 | 添加、获取、移除单个元素\</br\> 检查一个元素是否存在于集合中\</br\> 计算交集、并集、差集\</br\> 从集合里面随机获取元素 |
| HASH | 包含键值对的无序散列表 | 添加、获取、移除单个键值对\</br\> 获取所有键值对\</br\> 检查某个键是否存在|
| ZSET | 有序集合 | 添加、获取、删除元素\</br\> 根据分值范围或者成员来获取元素\</br\> 计算一个键的排名 |

> [What Redis data structures look like](https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/1-2-what-redis-data-structures-look-like/)

### STRING

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/6019b2db-bc3e-4408-b6d8-96025f4481d6.png" width="400"/> </div><br>

```html
> set hello world
OK
> get hello
"world"
> del hello
(integer) 1
> get hello
(nil)
```

### LIST

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/fb327611-7e2b-4f2f-9f5b-38592d408f07.png" width="400"/> </div><br>

```html
> rpush list-key item
(integer) 1
> rpush list-key item2
(integer) 2
> rpush list-key item
(integer) 3

> lrange list-key 0 -1
1) "item"
2) "item2"
3) "item"

> lindex list-key 1
"item2"

> lpop list-key
"item"

> lrange list-key 0 -1
1) "item2"
2) "item"
```

### SET

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/cd5fbcff-3f35-43a6-8ffa-082a93ce0f0e.png" width="400"/> </div><br>

```html
> sadd set-key item
(integer) 1
> sadd set-key item2
(integer) 1
> sadd set-key item3
(integer) 1
> sadd set-key item
(integer) 0

> smembers set-key
1) "item"
2) "item2"
3) "item3"

> sismember set-key item4
(integer) 0
> sismember set-key item
(integer) 1

> srem set-key item2
(integer) 1
> srem set-key item2
(integer) 0

> smembers set-key
1) "item"
2) "item3"
```

### HASH

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7bd202a7-93d4-4f3a-a878-af68ae25539a.png" width="400"/> </div><br>

```html
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

### ZSET

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1202b2d6-9469-4251-bd47-ca6034fb6116.png" width="400"/> </div><br>

```html
> zadd zset-key 728 member1
(integer) 1
> zadd zset-key 982 member0
(integer) 1
> zadd zset-key 982 member0
(integer) 0

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

## 三、数据结构

### 字典

dictht 是一个散列表结构，使用拉链法解决哈希冲突。

```c
/* This is our hash table structure. Every dictionary has two of this as we
 * implement incremental rehashing, for the old to the new table. */
typedef struct dictht {
    dictEntry **table;
    unsigned long size;
    unsigned long sizemask;
    unsigned long used;
} dictht;
```

```c
typedef struct dictEntry {
    void *key;
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    struct dictEntry *next;
} dictEntry;
```

Redis 的字典 dict 中包含两个哈希表 dictht，这是为了方便进行 rehash 操作。在扩容时，将其中一个 dictht 上的键值对 rehash 到另一个 dictht 上面，完成之后释放空间并交换两个 dictht 的角色。

```c
typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    unsigned long iterators; /* number of iterators currently running */
} dict;
```

rehash 操作不是一次性完成，而是采用渐进方式，这是为了避免一次性执行过多的 rehash 操作给服务器带来过大的负担。

渐进式 rehash 通过记录 dict 的 rehashidx 完成，它从 0 开始，然后每执行一次 rehash 都会递增。例如在一次 rehash 中，要把 dict[0] rehash 到 dict[1]，这一次会把 dict[0] 上 table[rehashidx] 的键值对 rehash 到 dict[1] 上，dict[0] 的 table[rehashidx] 指向 null，并令 rehashidx++。

在 rehash 期间，每次对字典执行添加、删除、查找或者更新操作时，都会执行一次渐进式 rehash。

采用渐进式 rehash 会导致字典中的数据分散在两个 dictht 上，因此对字典的查找操作也需要到对应的 dictht 去执行。

```c
/* Performs N steps of incremental rehashing. Returns 1 if there are still
 * keys to move from the old to the new hash table, otherwise 0 is returned.
 *
 * Note that a rehashing step consists in moving a bucket (that may have more
 * than one key as we use chaining) from the old to the new hash table, however
 * since part of the hash table may be composed of empty spaces, it is not
 * guaranteed that this function will rehash even a single bucket, since it
 * will visit at max N*10 empty buckets in total, otherwise the amount of
 * work it does would be unbound and the function may block for a long time. */
int dictRehash(dict *d, int n) {
    int empty_visits = n * 10; /* Max number of empty buckets to visit. */
    if (!dictIsRehashing(d)) return 0;

    while (n-- && d->ht[0].used != 0) {
        dictEntry *de, *nextde;

        /* Note that rehashidx can't overflow as we are sure there are more
         * elements because ht[0].used != 0 */
        assert(d->ht[0].size > (unsigned long) d->rehashidx);
        while (d->ht[0].table[d->rehashidx] == NULL) {
            d->rehashidx++;
            if (--empty_visits == 0) return 1;
        }
        de = d->ht[0].table[d->rehashidx];
        /* Move all the keys in this bucket from the old to the new hash HT */
        while (de) {
            uint64_t h;

            nextde = de->next;
            /* Get the index in the new hash table */
            h = dictHashKey(d, de->key) & d->ht[1].sizemask;
            de->next = d->ht[1].table[h];
            d->ht[1].table[h] = de;
            d->ht[0].used--;
            d->ht[1].used++;
            de = nextde;
        }
        d->ht[0].table[d->rehashidx] = NULL;
        d->rehashidx++;
    }

    /* Check if we already rehashed the whole table... */
    if (d->ht[0].used == 0) {
        zfree(d->ht[0].table);
        d->ht[0] = d->ht[1];
        _dictReset(&d->ht[1]);
        d->rehashidx = -1;
        return 0;
    }

    /* More to rehash... */
    return 1;
}
```


### rehash & 渐进式 hash

<div id="article_content" class="article_content clearfix">
        <link rel="stylesheet" href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/ck_htmledit_views-6e43165c0a.css">
                <div id="content_views" class="markdown_views prism-atom-one-dark">
                    <svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
                        <path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path>
                    </svg>
                    <h1><a name="t0"></a><a id="Redis__2"></a>Redis 键值对结构</h1> 
<p><img src="https://img-blog.csdnimg.cn/8532f5cc62064c6ca0451345431adf04.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2FybG9zS2VGZW5n,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述"></p> 
<ul><li>HashTable</li></ul> 
<blockquote> 
 <p>Redis中有一个「全局<a href="https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;哈希\&quot;}&quot;}" data-tit="哈希" data-pretit="哈希">哈希</a>表」，该哈希表中保存锁所有的键值对。对于Hash表的查找操作时间复杂度为O(1)</p> 
</blockquote> 
<ul><li>Bucket</li></ul> 
<blockquote> 
 <p>哈希表中的每一个元素称为哈希桶(Bucket),哈希桶中保存了键值对数据</p> 
</blockquote> 
<ul><li>Entry</li></ul> 
<blockquote> 
 <p>保存键值对数据<br> 如上图：其实Entry中保存的是Key，Value的指针值，通过对应的指针能够对Key，Value进行查找</p> 
</blockquote> 
<p>举个🌰：<br> 假设你现在要往Redis中写入一个String类型的数据，其中Key=username_1 ，Value=小明<br> 那么Redis将调用<a href="https://so.csdn.net/so/search?q=Hash&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=Hash&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;Hash\&quot;}&quot;}" data-tit="Hash" data-pretit="hash">Hash</a>函数计算Key的哈希值，根据哈希值选择哈希表中对应的哈希桶，然后将Key，Value进行存储。并将Key,Value的指针存入哈希桶的Enrty中</p> 
<h1><a name="t1"></a><a id="Rehash_19"></a>Rehash</h1> 
<h2><a name="t2"></a><a id="_20"></a>哈希冲突</h2> 
<p><img src="https://img-blog.csdnimg.cn/3c0743360c5f429192831cc9f15ab037.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2FybG9zS2VGZW5n,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述"><br> 哈希表中桶的数量是有限的，当Key的数量较大时自然避免不了哈希冲突(多个Key落在了同一个哈希桶中)。当哈希桶中存在哈希冲突时那么多个Entry就形成了链表，每个链表中有一个Next指针指向了下一个元素。当哈希桶中的链表过长时，那么查询性能会显著降低(链表的查找<a href="https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;时间复杂度\&quot;}&quot;}" data-tit="时间复杂度" data-pretit="时间复杂度">时间复杂度</a>为O(N))，Redis为了避免类似的问题从而会进行Rehash操作</p> 
<h2><a name="t3"></a><a id="Rehash_24"></a>Rehash</h2> 
<p><img src="https://img-blog.csdnimg.cn/c98da7d56e0549759f34d7c37f92ef48.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2FybG9zS2VGZW5n,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述"><br> 为了能够减少哈希冲突，其实最直接的做法是增加哈希桶数量从而让元素能够更加均匀的分布在哈希表中。而Redis中的Rehash操作的原理其实也是如此，只不过他的设计更加巧妙。<br> Redis中其实有两个「全局哈希表」，一开始时默认使用的Hash Table1来存储数据，而Hash Table2并没有分配内存空间。随着Hash Table1中的元素越来越多时，Redis会进行Rehash操作。首先会给Hash Table2分配一定的内存空间(肯定比哈希表一大)，然后将Hash Table1中的元素重新映射至Hash Table2中，最后会释放Hash Table1。这样来看的话，Redis的Rehash操作的确能减少哈希冲突，但是你有没有想过如果Hash Table1中的元素特别多时，如果这么粗暴的将数据往Hash Table2中搬，那势必会阻塞Redis的主线程进而影响Redis的性能。其实Redis也考虑到了这个问题，那么接下来我们看看Redis是如何解决这种问题的</p> 
<h2><a name="t4"></a><a id="Rehash_30"></a>渐进式Rehash</h2> 
<p><img src="https://img-blog.csdnimg.cn/a00a1328ac454ba09886198abba32bb9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2FybG9zS2VGZW5n,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述"><br> 如上图所示，渐进式Rehash采用的做法是：</p> 
<p>在将数据拷贝至Hash Table2时，Hash Table1仍然对客户端提供服务。<br> 当客户端访问Hash Table1时，Hash Table1 将索引位置为1的Bucket1中的Entery全部拷贝至Hash Table2,同理当客户端再一次访问Hash Table1时，Hash Table1 将索引位置为2的Bucket2中的Entery全部拷贝至Hash Table2。再拷贝数据进Hash Table2的同时会对数据做重新的Bucket分配，从而减少Hash冲突。<br> 如此往复下去，当Hash Table1的元素都拷贝Hash Table2时，Hash Table2对顶替Hash Table1 与客户端进行交互，此时Hash Table1会被释放，等待下一次Rehash使用。</p> 
<h1><a name="t5"></a><a id="_39"></a>总结</h1> 
<p>上文中我们描述了Redis是通过Hash Table的方式来组织所有的键值对，Hash Table中的元素为Bucket(哈希桶)。Bucket中存储着键值对的指针。<br> 当Hash Table中的元素逐渐增多时，会在Bucket中形成链表，一旦链表过长则会严重影响Redis的查询性能，这对以速度著称的Redis是不能接受的。所以Redis采用了增加Hash Table的容量来解决这个问题，也就是所谓的Rehash机制，而Redis为了减轻Rehash时数据大量拷贝锁带来的压力，从而采用了渐进式Rehash来分批次的进行数据拷贝。</p> 
<p>如果你对文章内容有疑问，欢迎留言区交流</p>
                </div><div><div></div></div>
                <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/markdown_views-22a2fefd3b.css" rel="stylesheet">
                <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/style-4f8fbf9108.css" rel="stylesheet">
        </div>


### 跳跃表

是有序集合的底层实现之一。

跳跃表是基于多指针有序链表实现的，可以看成多个有序链表。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/beba612e-dc5b-4fc2-869d-0b23408ac90a.png" width="600px"/> </div><br>

在查找时，从上层指针开始查找，找到对应的区间之后再到下一层去查找。下图演示了查找 22 的过程。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0ea37ee2-c224-4c79-b895-e131c6805c40.png" width="600px"/> </div><br>

与红黑树等平衡树相比，跳跃表具有以下优点：

- 插入速度非常快速，因为不需要进行旋转等操作来维护平衡性；
- 更容易实现；
- 支持无锁操作。

## 四、使用场景

### 计数器

可以对 String 进行自增自减运算，从而实现计数器功能。

Redis 这种内存型数据库的读写性能非常高，很适合存储频繁读写的计数量。

### 缓存

将热点数据放到内存中，设置内存的最大使用量以及淘汰策略来保证缓存的命中率。

### 查找表

例如 DNS 记录就很适合使用 Redis 进行存储。

查找表和缓存类似，也是利用了 Redis 快速的查找特性。但是查找表的内容不能失效，而缓存的内容可以失效，因为缓存不作为可靠的数据来源。

### 消息队列

List 是一个双向链表，可以通过 lpush 和 rpop 写入和读取消息

不过最好使用 Kafka、RabbitMQ 等消息中间件。

### 会话缓存

可以使用 Redis 来统一存储多台应用服务器的会话信息。

当应用服务器不再存储用户的会话信息，也就不再具有状态，一个用户可以请求任意一个应用服务器，从而更容易实现高可用性以及可伸缩性。

### 分布式锁实现

在分布式场景下，无法使用单机环境下的锁来对多个节点上的进程进行同步。

可以使用 Redis 自带的 SETNX 命令实现分布式锁，除此之外，还可以使用官方提供的 RedLock 分布式锁实现。

### 其它

Set 可以实现交集、并集等操作，从而实现共同好友等功能。

ZSet 可以实现有序性操作，从而实现排行榜等功能。

## 五、Redis 与 Memcached

两者都是非关系型内存键值数据库，主要有以下不同：

### 数据类型

Memcached 仅支持字符串类型，而 Redis 支持五种不同的数据类型，可以更灵活地解决问题。

### 数据持久化

Redis 支持两种持久化策略：RDB 快照和 AOF 日志，而 Memcached 不支持持久化。

### 分布式

Memcached 不支持分布式，只能通过在客户端使用一致性哈希来实现分布式存储，这种方式在存储和查询时都需要先在客户端计算一次数据所在的节点。

Redis Cluster 实现了分布式的支持。

### 一致性哈希

普通hash算法问题：
<img src="https://img-blog.csdnimg.cn/img_convert/209b4033ff9453e3e1764b8d186f80a8.png" alt="image-20220814162729177">

<img src="https://img-blog.csdnimg.cn/img_convert/1785646a75edde993bf3a91cc8442f42.png" alt="image-20220814163047424">

所以说，当服务器数量发生改变时，所有缓存在一定时间内是失效的，当应用无法从缓存中获取数据时，则会向后端服务器请求数据；这样会造成缓存的雪崩，后端服务器将会承受巨大的压力，整个系统很有可能被压垮。为了解决这种情况，就有了一致性哈希算法。

#### 概述

先构造一个长度为2^32的整数环（这个环被称为一致性Hash环），根据节点名称的Hash值（其分布为[0, 2^32-1]）将服务器节点放置在这个Hash环上，然后根据数据的Key值计算得到其Hash值（其分布也为[0, 2^32-1]），接着在Hash环上顺时针查找距离这个Key值的Hash值最近的服务器节点，完成Key到服务器的映射查找。

这种算法解决了普通余数Hash算法伸缩性差的问题，可以保证在上线、下线服务器的情况下尽量有多的请求命中原来路由到的服务器。

**步骤一：哈希环的组织：**

我们将 2^32 想象成一个圆，像钟表一样，钟表的圆可以理解成由60个点组成的圆，而此处我们把这个圆想象成由2^32个点组成的圆，示意图如下：

圆环的正上方的点代表0，0点右侧的第一个点代表1，以此类推，2、3、4、5、6……直到232-1,也就是说0点左侧的第一个点代表232-1，我们把这个由 2^32 个点组成的圆环称为hash环。

<img src="https://img-blog.csdnimg.cn/img_convert/9e98d8173df7bc41c9033d64b732986d.png" alt="image-20220814164742233">

**步骤二：确定服务器在哈希环的位置：**

哈希算法公式：`hash（服务器的IP）% 2^32`

上述公式的计算结果一定是 0 到 2^32-1 之间的整数，那么上图中的 hash 环上必定有一个点与这个整数对应，所以我们可以使用这个整数代表服务器，也就是服务器就可以映射到这个环上，假设我们有 ABC 三台服务器，那么它们在哈希环上的示意图如下：

<img src="https://img-blog.csdnimg.cn/img_convert/98cff1e57f3b7ae7dd2f577bbc8f5123.png" alt="image-20220814164947938">

**步骤三：将数据映射到哈希环上：**

我们还是使用图片的名称作为 key，所以我们使用下面算法将图片映射在哈希环上：hash（图片名称） % 2^32，假设我们有4张图片，映射后的示意图如下，其中橘黄色的点表示图片：

那么，怎么算出上图中的图片应该被缓存到哪一台服务上面呢？我们只要从图片的位置开始，沿顺时针方向遇到的第一个服务器就是图片存放的服务器了。最终，1号、2号图片将会被缓存到服务器A上，3号图片将会被缓存到服务器B上，4号、5号图片将会被缓存到服务器C上。

<img src="https://img-blog.csdnimg.cn/img_convert/98cff1e57f3b7ae7dd2f577bbc8f5123.png" alt="image-20220814164947938">

**数据重定位**

假设服务器B出现了故障，需要将服务器B移除，那么移除前后的示意图如下图所示：

<img src="https://img-blog.csdnimg.cn/img_convert/39b6a76cb898dd02f9aee939353abd77.png" alt="image-20220814170431521">

在服务器B未移除时，图片3应该被缓存到服务器B中，可是当服务器B移除以后，按照之前描述的一致性哈希算法的规则，图片3应该被缓存到服务器C中，因为从图片3的位置出发，沿顺时针方向遇到的第一个缓存服务器节点就是服务器C，也就是说，如果服务器B出现故障被移除时，图片3的缓存位置会发生改变，但是，图片4、图片5仍然会被缓存到服务器C中，图片1与图片2仍然会被缓存到服务器A中，这与服务器B移除之前并没有任何区别，这就是一致性哈希算法的数据重定位。

**一致性哈希的优点**

如果只是简单对服务器数量进行取模，那么当服务器数量发生变化时，会产生缓存的雪崩，从而很有可能导致系统崩溃

而使用一致性哈希算法就可以很好的解决这个问题，因为一致性Hash算法对于节点的增减都只需重定位环空间中的一小部分数据，只有部分缓存会失效，不至于将所有压力都在同一时间集中到后端服务器上，具有较好的容错性和可扩展性。

**一致性哈希算法的问题**

hash 环的倾斜与虚拟节点：

一致性哈希算法在服务节点太少的情况下，容易因为节点分部不均匀而造成数据倾斜问题，也就是被缓存的对象大部分集中缓存在某一台服务器上，从而出现数据分布不均匀的情况，这种情况就称为 hash 环的倾斜。如下图所示：

<img src="https://img-blog.csdnimg.cn/img_convert/37a75267a4a5bb7fb83925efda1ad8df.png" alt="image-20220814170739606">

hash 环的倾斜在极端情况下，仍然有可能引起系统的崩溃，为了解决这种数据倾斜问题，一致性哈希算法引入了虚拟节点机制，即对每一个服务节点计算多个哈希，每个计算结果位置都放置一个此服务节点，称为虚拟节点

这样就可以实现一个实际物理节点可以对应多个虚拟节点，虚拟节点越多，hash环上的节点就越多，缓存被均匀分布的概率就越大，hash环倾斜所带来的影响就越小，同时数据定位算法不变，最后只是多了一步虚拟节点到实际节点的映射。

具体做法可以在服务器ip或主机名的后面增加编号来实现，加入虚拟节点以后的hash环如下（除了标真的都是虚拟节点）：

<img src="https://img-blog.csdnimg.cn/img_convert/5c237535c6d7233e5c60c7e0fbb935e6.png" alt="image-20220814171413194">



### 内存管理机制

- 在 Redis 中，并不是所有数据都一直存储在内存中，可以将一些很久没用的 value 交换到磁盘，而 Memcached 的数据则会一直在内存中。

- Memcached 将内存分割成特定长度的块来存储数据，以完全解决内存碎片的问题。但是这种方式会使得内存的利用率不高，例如块的大小为 128 bytes，只存储 100 bytes 的数据，那么剩下的 28 bytes 就浪费掉了。

## 六、键的过期时间

Redis 可以为每个键设置过期时间，当键过期时，会自动删除该键。

对于散列表这种容器，只能为整个键设置过期时间（整个散列表），而不能为键里面的单个元素设置过期时间。

## 七、数据淘汰策略

可以设置内存最大使用量，当内存使用量超出时，会施行数据淘汰策略。

Redis 具体有 6 种淘汰策略：

| 策略 | 描述 |
| :--: | :--: |
| volatile-lru | 从已设置过期时间的数据集中挑选最近最少使用的数据淘汰 |
| volatile-ttl | 从已设置过期时间的数据集中挑选将要过期的数据淘汰 |
|volatile-random | 从已设置过期时间的数据集中任意选择数据淘汰 |
| allkeys-lru | 从所有数据集中挑选最近最少使用的数据淘汰 |
| allkeys-random | 从所有数据集中任意选择数据进行淘汰 |
| noeviction | 禁止驱逐数据 |

作为内存数据库，出于对性能和内存消耗的考虑，Redis 的淘汰算法实际实现上并非针对所有 key，而是抽样一小部分并且从中选出被淘汰的 key。

使用 Redis 缓存数据时，为了提高缓存命中率，需要保证缓存数据都是热点数据。可以将内存最大使用量设置为热点数据占用的内存量，然后启用 allkeys-lru 淘汰策略，将最近最少使用的数据淘汰。

Redis 4.0 引入了 volatile-lfu 和 allkeys-lfu 淘汰策略，LFU 策略通过统计访问频率，将访问频率最少的键值对淘汰。

## 八、持久化

Redis 是内存型数据库，为了保证数据在断电后不会丢失，需要将内存中的数据持久化到硬盘上。

### RDB 持久化

将某个时间点的所有数据都存放到硬盘上。

可以将快照复制到其它服务器从而创建具有相同数据的服务器副本。

如果系统发生故障，将会丢失最后一次创建快照之后的数据。

如果数据量很大，保存快照的时间会很长。

### AOF 持久化

将写命令添加到 AOF 文件（Append Only File）的末尾。

使用 AOF 持久化需要设置同步选项，从而确保写命令同步到磁盘文件上的时机。这是因为对文件进行写入并不会马上将内容同步到磁盘上，而是先存储到缓冲区，然后由操作系统决定什么时候同步到磁盘。有以下同步选项：

| 选项 | 同步频率 |
| :--: | :--: |
| always | 每个写命令都同步 |
| everysec | 每秒同步一次 |
| no | 让操作系统来决定何时同步 |

- always 选项会严重减低服务器的性能；
- everysec 选项比较合适，可以保证系统崩溃时只会丢失一秒左右的数据，并且 Redis 每秒执行一次同步对服务器性能几乎没有任何影响；
- no 选项并不能给服务器性能带来多大的提升，而且也会增加系统崩溃时数据丢失的数量。

随着服务器写请求的增多，AOF 文件会越来越大。Redis 提供了一种将 AOF 重写的特性，能够去除 AOF 文件中的冗余写命令。

## 九、事务

一个事务包含了多个命令，服务器在执行事务期间，不会改去执行其它客户端的命令请求。

事务中的多个命令被一次性发送给服务器，而不是一条一条发送，这种方式被称为流水线，它可以减少客户端与服务器之间的网络通信次数从而提升性能。

Redis 最简单的事务实现方式是使用 MULTI 和 EXEC 命令将事务操作包围起来。

## 十、事件

Redis 服务器是一个事件驱动程序。

### 文件事件

服务器通过套接字与客户端或者其它服务器进行通信，文件事件就是对套接字操作的抽象。

Redis 基于 Reactor 模式开发了自己的网络事件处理器，使用 I/O 多路复用程序来同时监听多个套接字，并将到达的事件传送给文件事件分派器，分派器会根据套接字产生的事件类型调用相应的事件处理器。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9ea86eb5-000a-4281-b948-7b567bd6f1d8.png" width=""/> </div><br>

### 时间事件

服务器有一些操作需要在给定的时间点执行，时间事件是对这类定时操作的抽象。

时间事件又分为：

- 定时事件：是让一段程序在指定的时间之内执行一次；
- 周期性事件：是让一段程序每隔指定时间就执行一次。

Redis 将所有时间事件都放在一个无序链表中，通过遍历整个链表查找出已到达的时间事件，并调用相应的事件处理器。

### 事件的调度与执行

服务器需要不断监听文件事件的套接字才能得到待处理的文件事件，但是不能一直监听，否则时间事件无法在规定的时间内执行，因此监听时间应该根据距离现在最近的时间事件来决定。

事件调度与执行由 aeProcessEvents 函数负责，伪代码如下：

```python
def aeProcessEvents():
    # 获取到达时间离当前时间最接近的时间事件
    time_event = aeSearchNearestTimer()
    # 计算最接近的时间事件距离到达还有多少毫秒
    remaind_ms = time_event.when - unix_ts_now()
    # 如果事件已到达，那么 remaind_ms 的值可能为负数，将它设为 0
    if remaind_ms < 0:
        remaind_ms = 0
    # 根据 remaind_ms 的值，创建 timeval
    timeval = create_timeval_with_ms(remaind_ms)
    # 阻塞并等待文件事件产生，最大阻塞时间由传入的 timeval 决定
    aeApiPoll(timeval)
    # 处理所有已产生的文件事件
    procesFileEvents()
    # 处理所有已到达的时间事件
    processTimeEvents()
```

将 aeProcessEvents 函数置于一个循环里面，加上初始化和清理函数，就构成了 Redis 服务器的主函数，伪代码如下：

```python
def main():
    # 初始化服务器
    init_server()
    # 一直处理事件，直到服务器关闭为止
    while server_is_not_shutdown():
        aeProcessEvents()
    # 服务器关闭，执行清理操作
    clean_server()
```

从事件处理的角度来看，服务器运行流程如下：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/c0a9fa91-da2e-4892-8c9f-80206a6f7047.png" width="350"/> </div><br>

## 十一、复制

通过使用 slaveof host port 命令来让一个服务器成为另一个服务器的从服务器。

一个从服务器只能有一个主服务器，并且不支持主主复制。

### 连接过程

1. 主服务器创建快照文件，发送给从服务器，并在发送期间使用缓冲区记录执行的写命令。快照文件发送完毕之后，开始向从服务器发送存储在缓冲区中的写命令；

2. 从服务器丢弃所有旧数据，载入主服务器发来的快照文件，之后从服务器开始接受主服务器发来的写命令；

3. 主服务器每执行一次写命令，就向从服务器发送相同的写命令。

### 主从链

随着负载不断上升，主服务器可能无法很快地更新所有从服务器，或者重新连接和重新同步从服务器将导致系统超载。为了解决这个问题，可以创建一个中间层来分担主服务器的复制工作。中间层的服务器是最上层服务器的从服务器，又是最下层服务器的主服务器。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/395a9e83-b1a1-4a1d-b170-d081e7bb5bab.png" width="600"/> </div><br>

## 十二、Sentinel

Sentinel（哨兵）可以监听集群中的服务器，并在主服务器进入下线状态时，自动从从服务器中选举出新的主服务器。

## 十三、分片

分片是将数据划分为多个部分的方法，可以将数据存储到多台机器里面，这种方法在解决某些问题时可以获得线性级别的性能提升。

假设有 4 个 Redis 实例 R0，R1，R2，R3，还有很多表示用户的键 user:1，user:2，... ，有不同的方式来选择一个指定的键存储在哪个实例中。

- 最简单的方式是范围分片，例如用户 id 从 0\~1000 的存储到实例 R0 中，用户 id 从 1001\~2000 的存储到实例 R1 中，等等。但是这样需要维护一张映射范围表，维护操作代价很高。
- 还有一种方式是哈希分片，使用 CRC32 哈希函数将键转换为一个数字，再对实例数量求模就能知道应该存储的实例。

根据执行分片的位置，可以分为三种分片方式：

- 客户端分片：客户端使用一致性哈希等算法决定键应当分布到哪个节点。
- 代理分片：将客户端请求发送到代理上，由代理转发请求到正确的节点上。
- 服务器分片：Redis Cluster。

## 十四、一个简单的论坛系统分析

该论坛系统功能如下：

- 可以发布文章；
- 可以对文章进行点赞；
- 在首页可以按文章的发布时间或者文章的点赞数进行排序显示。

### 文章信息

文章包括标题、作者、赞数等信息，在关系型数据库中很容易构建一张表来存储这些信息，在 Redis 中可以使用 HASH 来存储每种信息以及其对应的值的映射。

Redis 没有关系型数据库中的表这一概念来将同种类型的数据存放在一起，而是使用命名空间的方式来实现这一功能。键名的前面部分存储命名空间，后面部分的内容存储 ID，通常使用 : 来进行分隔。例如下面的 HASH 的键名为 article:92617，其中 article 为命名空间，ID 为 92617。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7c54de21-e2ff-402e-bc42-4037de1c1592.png" width="400"/> </div><br>

### 点赞功能

当有用户为一篇文章点赞时，除了要对该文章的 votes 字段进行加 1 操作，还必须记录该用户已经对该文章进行了点赞，防止用户点赞次数超过 1。可以建立文章的已投票用户集合来进行记录。

为了节约内存，规定一篇文章发布满一周之后，就不能再对它进行投票，而文章的已投票集合也会被删除，可以为文章的已投票集合设置一个一周的过期时间就能实现这个规定。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/485fdf34-ccf8-4185-97c6-17374ee719a0.png" width="400"/> </div><br>

### 对文章进行排序

为了按发布时间和点赞数进行排序，可以建立一个文章发布时间的有序集合和一个文章点赞数的有序集合。（下图中的 score 就是这里所说的点赞数；下面所示的有序集合分值并不直接是时间和点赞数，而是根据时间和点赞数间接计算出来的）

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f7d170a3-e446-4a64-ac2d-cb95028f81a8.png" width="800"/> </div><br>

## 参考资料

- Carlson J L. Redis in Action[J]. Media.johnwiley.com.au, 2013.
- [黄健宏. Redis 设计与实现 [M]. 机械工业出版社, 2014.](http://redisbook.com/index.html)
- [REDIS IN ACTION](https://redislabs.com/ebook/foreword/)
- [Skip Lists: Done Right](http://ticki.github.io/blog/skip-lists-done-right/)
- [论述 Redis 和 Memcached 的差异](http://www.cnblogs.com/loveincode/p/7411911.html)
- [Redis 3.0 中文版- 分片](http://wiki.jikexueyuan.com/project/redis-guide)
- [Redis 应用场景](http://www.scienjus.com/redis-use-case/)
- [Using Redis as an LRU cache](https://redis.io/topics/lru-cache)