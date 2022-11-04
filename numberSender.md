# 六种方式实现全局唯一发号器

来源：<a href="https://blog.csdn.net/qq_20051535/article/details/120072174">https://blog.csdn.net/qq_20051535/article/details/120072174</a>


<div>
<article class="baidu_pl">
        <div id="article_content" class="article_content clearfix">
        <link rel="stylesheet" href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/ck_htmledit_views-6e43165c0a.css">
                <div id="content_views" class="htmledit_views">
                   </h1> 
<h1><a name="t1"></a>概念</h1> 
<p>&nbsp; 发号器，也就是在系统全局生成绝对唯一的唯一id生成器，比如订单号、流水号等场景。类似于身份证号，它需要保证全局唯一，尤其是在<a href="https://so.csdn.net/so/search?q=%E5%88%86%E5%B8%83%E5%BC%8F&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E5%88%86%E5%B8%83%E5%BC%8F&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;分布式\&quot;}&quot;}" data-tit="分布式" data-pretit="分布式">分布式</a>机器中，不同机器不能生成一样的号牌。我们需要通过一些算法或方式实现这个小功能。</p> 
<h1><a name="t2"></a><a href="https://so.csdn.net/so/search?q=%E9%9B%AA%E8%8A%B1%E7%AE%97%E6%B3%95&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E9%9B%AA%E8%8A%B1%E7%AE%97%E6%B3%95&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;雪花算法\&quot;}&quot;}" data-tit="雪花算法" data-pretit="雪花算法">雪花算法</a></h1> 
<p>&nbsp; 由Twitter提出，基于对long的高低位分配实现，几乎可以理解为发号器的最优实现，目前美团、百度等开源发号器大多基于或参考了这种分配形式。</p> 
<p style="text-align:center;"><img alt="" src="https://img-blog.csdnimg.cn/20210902235455513.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5YyX6bmkTQ==,size_20,color_FFFFFF,t_70,g_se,x_16"></p> 
<p>&nbsp; &nbsp;雪花算法将一个long的除符号位外63位分成了下面的三个部分：</p> 
<ul><li>41位的毫秒时间戳（可以使用69年左右）</li><li>10位的机器号标识（10位的长度最多支持部署1024个节点）</li><li>12位的计数顺序号（12位的计数顺序号支持每个节点每毫秒产生4096个ID序号）</li></ul>
<h2><a name="t3"></a>👍 优点</h2> 
<p>1. <a href="https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E6%88%B3&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E6%88%B3&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;时间戳\&quot;}&quot;}" data-tit="时间戳" data-pretit="时间戳">时间戳</a>在高位，这样产生的将会是趋势递增的数字，适合用于订单号、流水号等场景；</p> 
<p>2. 性能高，可以在短时间生成大量号牌，而且几乎不依赖任何IO；</p> 
<p>3. 可以根据自身业务需要分配bit位。</p> 
<h2><a name="t4"></a>🚩 缺点</h2> 
<p>1. 依赖系统时钟，如果系统时钟回拨可能导致故障。尤其是要注意闰秒场景，JDK1.7之前没有解决，可能系统回拨而导致生成重复号牌；</p> 
<p>2. 由于依赖系统时钟，如果多机部署且这几台机器时钟不一致可能导致全局不是递增的。</p> 
<p>&nbsp;&nbsp;</p> 
<h1><a name="t5"></a>UUID</h1> 
<p>&nbsp; UUID是一个堪称经典的唯一ID生成方式，UUID具有很多个不同版本，整体上思路差不多，都是通过时间戳、MAC地址等信息处理拼接之后分成一个全球唯一的id，比如下面这样的形式：</p> 
<pre data-index="0"><code class="hljs language-undefined" onclick="mdcp.copyCode(event)">00000000-0000-0000-0000-000000000000</code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4334&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; 当然，这是一个特殊的UUID，正常情况时不可能全为0的，我们将上面这个全为0的UUID称为Nil UUID，一般我们用不到它。</p> 
<h2><a name="t6"></a>👍 优点</h2> 
<p>1. 本地运算，性能高，低延时，可拓展性高；</p> 
<h2><a name="t7"></a>🚩 缺点</h2> 
<p>1. 不是趋势递增的，不具有有序性；</p> 
<p>2. UUID太长了，一般采用32位编码。不适合在分库分表等场景使用，因为UUID做数据库主键太长了；</p> 
<p>3. UUID存在很多版本，部分版本可能存在使用随机数生成，在特别极端的情况下可能存在重复；</p> 
<p>&nbsp; &nbsp;</p> 
<h1><a name="t8"></a>MongoDB发号器</h1> 
<p>&nbsp; MongDB实现了一种发号器机制，和推特的雪花算法类似，也是通过位运算进行的。可以直接看源码：<a href="https://github.com/mongodb/mongo-java-driver/blob/master/bson/src/main/org/bson/types/ObjectId.java">mongo-java-driver/ObjectId.java at master · mongodb/mongo-java-driver · GitHub</a></p> 
<p style="text-align:center;"><img alt="" src="https://img-blog.csdnimg.cn/20210903003131260.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5YyX6bmkTQ==,size_20,color_FFFFFF,t_70,g_se,x_16"></p> 
<p>&nbsp; &nbsp;可以看到MongDB采用了类似于雪花算法的形式，它的分配形式是：</p> 
<p>32位：秒级时间戳，从1970年开始计数，目前来看可以支持136年</p> 
<p>24位：递增数字，这里和雪花算法有一点不同，它即使时间戳变化也不会归零。如果超过了24位就截取后24位；</p> 
<p>24位：机器标识，将服务器上全部网卡的Mac地址拼接后计算HashCode，截取24位；</p> 
<p>16位：通过JMX（Java平台的管理和监控接口）获取进程号。</p> 
<h2><a name="t9"></a>👍 优点</h2> 
<p>1. 一样能够实现趋势递增；</p> 
<p>2. 高性能，本地生成无需远程调用，不依赖其他服务可靠性高。</p> 
<h2><a name="t10"></a>🚩 缺点</h2> 
<p>1. 由于进程号获取失败、机器号获取失败都会用随机数等代替，可能在极端情况出现重复。</p> 
<p>2. 也是比较长，不能存储进long类型等。</p> 
<p>&nbsp; &nbsp;</p> 
<h1><a name="t11"></a>Flicker发号器</h1> 
<p>&nbsp; 据说是Flicker团队的解决思路，在某公司公众号看到的，但是目前网上并没有找到此团队相关资料，来源存疑。</p> 
<p>&nbsp; 所以在这里只讲解实现思路。整体思路比较简单，采用了MySQL自增长ID的机制 和&nbsp;replace into 语句。</p> 
<p>&nbsp; 先建立一张表：</p> 
<pre data-index="1"><code class="language-sql hljs" onclick="mdcp.copyCode(event)"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> Tickets64 (</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">    id <span class="hljs-type">bigint</span>(<span class="hljs-number">20</span>) unsigned <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> auto_increment,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">    stub <span class="hljs-type">char</span>(<span class="hljs-number">1</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">default</span> <span class="hljs-string">''</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">    <span class="hljs-keyword">PRIMARY</span> KEY (id),</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">    <span class="hljs-keyword">UNIQUE</span> KEY stub (stub)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">)ENGINE <span class="hljs-operator">=</span> MyISAM;</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4334&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; 每一次通过下面的语句获取最新的唯一ID：</p> 
<pre data-index="2"><code class="language-sql hljs" onclick="mdcp.copyCode(event)"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">REPLACE <span class="hljs-keyword">INTO</span> Tickets64 (stub) <span class="hljs-keyword">VALUES</span> (<span class="hljs-string">'a'</span>);</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">SELECT</span> LAST_INSERT_ID();</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4334&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; REPLACE INTO 的逻辑和INSERT相似，他的逻辑是如果存在，就先删除再添加，所以能保证id自增。</p> 
<p>&nbsp; 为了避免单点故障，一般会选择使用两台或以上MySQL服务器进行服务，通过区分步长实现区分，比如：</p> 
<pre data-index="3"><code class="hljs language-vbnet" onclick="mdcp.copyCode(event)"><ol class="hljs-ln" style="width:100%"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">Server1：</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">auto</span>-increment-increment = <span class="hljs-number">2</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">auto</span>-increment-offset = <span class="hljs-number">1</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"> </div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">Server2：</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">auto</span>-increment-increment = <span class="hljs-number">2</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">auto</span>-increment-offset = <span class="hljs-number">2</span></div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4334&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<h2><a name="t12"></a>👍 优点</h2> 
<p>1. 能保证整体上的趋势递增；</p> 
<p>2. 依靠数据库的自增ID，能够生成有序的ID。</p> 
<h2><a name="t13"></a>🚩 缺点</h2> 
<p>1. 依靠数据库的可用性，如果数据库在极端场景下全部不可用可能导致系统不可用；</p> 
<p>2. 没有之前的方法快。</p> 
<p>&nbsp; &nbsp;&nbsp;</p> 
<h1><a name="t14"></a>步长方式</h1> 
<p>&nbsp; 来源已经无从考证。滴滴的开源发号器Tinyid、阿里开源分库分表组件TDDL生成全局ID、有道发号器（未开源）基于此算法，美团开源的开源发号器Leaf支持此算法（可使用配置修改）。</p> 
<p>&nbsp; 原理相对简单，每一台机器都先领很多号牌，之后等到快发完再去领下一批，避免了每一次都去请求MySQL。</p> 
<p>&nbsp; 以Tinyid为例，根据官方介绍，需要先建立下面数据表：</p> 
<pre data-index="4"><code class="language-sql hljs" onclick="mdcp.copyCode(event)"><ol class="hljs-ln" style="width:1022px"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> `tiny_id_info` (</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `id` <span class="hljs-type">bigint</span>(<span class="hljs-number">20</span>) unsigned <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> AUTO_INCREMENT COMMENT <span class="hljs-string">'自增主键'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `biz_type` <span class="hljs-type">varchar</span>(<span class="hljs-number">63</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">''</span> COMMENT <span class="hljs-string">'业务类型，唯一'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `begin_id` <span class="hljs-type">bigint</span>(<span class="hljs-number">20</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'0'</span> COMMENT <span class="hljs-string">'开始id，仅记录初始值，无其他含义。初始化时begin_id和max_id应相同'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="5"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `max_id` <span class="hljs-type">bigint</span>(<span class="hljs-number">20</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'0'</span> COMMENT <span class="hljs-string">'当前最大id'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="6"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `step` <span class="hljs-type">int</span>(<span class="hljs-number">11</span>) <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'0'</span> COMMENT <span class="hljs-string">'步长'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="7"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `delta` <span class="hljs-type">int</span>(<span class="hljs-number">11</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'1'</span> COMMENT <span class="hljs-string">'每次id增量'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="8"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `remainder` <span class="hljs-type">int</span>(<span class="hljs-number">11</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'0'</span> COMMENT <span class="hljs-string">'余数'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="9"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `create_time` <span class="hljs-type">timestamp</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'2010-01-01 00:00:00'</span> COMMENT <span class="hljs-string">'创建时间'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="10"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `update_time` <span class="hljs-type">timestamp</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'2010-01-01 00:00:00'</span> COMMENT <span class="hljs-string">'更新时间'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="11"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  `version` <span class="hljs-type">bigint</span>(<span class="hljs-number">20</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">DEFAULT</span> <span class="hljs-string">'0'</span> COMMENT <span class="hljs-string">'版本号'</span>,</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="12"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  <span class="hljs-keyword">PRIMARY</span> KEY (`id`),</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="13"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">  <span class="hljs-keyword">UNIQUE</span> KEY `uniq_biz_type` (`biz_type`)</div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="14"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line">) ENGINE<span class="hljs-operator">=</span>InnoDB AUTO_INCREMENT<span class="hljs-operator">=</span><span class="hljs-number">1</span> <span class="hljs-keyword">DEFAULT</span> CHARSET<span class="hljs-operator">=</span>utf8 COMMENT <span class="hljs-string">'id信息表'</span>;</div></div></li></ol></code><div class="hljs-button {2}" data-title="复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4334&quot;}" onclick="hljs.copyCode(event)"></div></pre> 
<p>&nbsp; &nbsp;能够看出使用了乐观锁的形式，系统原理相对简单。</p> 
<h2><a name="t15"></a>👍 优点</h2> 
<p>1. 相比于之前的Flicker方案，能够大大降低数据库压力；</p> 
<p>2. 生成ID性能大大提升；</p> 
<p>3. 符合我们通常需要的趋势递增需求。</p> 
<h2><a name="t16"></a>🚩 缺点</h2> 
<p>1. 强依赖于数据库系统，数据库服务宕机将导致发号功能不可用。</p> 
<p>&nbsp; &nbsp;&nbsp;</p> 
<h1><a name="t17"></a>中间件</h1> 
<p>&nbsp; 可以通过引入 Redis 或 Zookeeper 实现这种功能。例如通过Zookeeper的顺序节点版本号生成全局唯一的顺序递增id。</p> 
<p>&nbsp; 但是我们一般情况并不希望引入更多的中间件来解决这个不大的问题。</p> 
<h2><a name="t18"></a>👍 优点</h2> 
<p>1. 绝对依次递增，之前方式都只能保证整体上趋势递增；</p> 
<h2><a name="t19"></a>🚩 缺点</h2> 
<p>1. 速度上比不上本机生成或大量缓存的形式；</p> 
<p>2. 强依赖中间件，中间件宕机将导致服务不可用；</p> 
<p>3. 实现比上面的形式复杂。</p>
                </div><div><div></div></div>
        </div>
        
</article>
</div>