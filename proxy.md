# 详解正向代理与反向代理

来源：<a href="https://blog.csdn.net/Dax1_/article/details/124652162">https://blog.csdn.net/Dax1_/article/details/124652162</a>

<article class="baidu_pl">
        <div id="article_content" class="article_content clearfix">
        <link rel="stylesheet" href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/ck_htmledit_views-6e43165c0a.css">
                <div id="content_views" class="markdown_views prism-github-gist">
                    <svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
                        <path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path>
                    </svg>
                    <h2><a name="t0"></a><a id="1_0"></a>1.<a href="https://so.csdn.net/so/search?q=%E6%AD%A3%E5%90%91%E4%BB%A3%E7%90%86&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E6%AD%A3%E5%90%91%E4%BB%A3%E7%90%86&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;正向代理\&quot;}&quot;}" data-tit="正向代理" data-pretit="正向代理">正向代理</a></h2> 
<h3><a name="t1"></a><a id="11__2"></a>1.1 概念</h3> 
<p>正向代理是一个位于客户端和目标服务器之间的<a href="https://so.csdn.net/so/search?q=%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;代理服务器\&quot;}&quot;}" data-tit="代理服务器" data-pretit="代理服务器">代理服务器</a>（中间服务器）。为了从目标服务器取得内容，客户端向代理服务器发送一个请求，<strong>并且指定目标服务器</strong>，之后代理向目标服务器转发请求，将获得的内容返回给客户端。正向代理的情况下，客户端必须要进行一些特殊的设置才能使用。</p> 
<h3><a name="t2"></a><a id="12__5"></a>1.2 特点</h3> 
<ul><li>正向代理需要主动设置代理服务器ip或者域名进行访问，由设置的服务器ip或者域名去访问内容并返回</li><li>正向代理是<strong>代理客户端</strong>，为客户端收发请求，使真实客户端对服务器不可见。</li></ul> 
<p><img src="https://img-blog.csdnimg.cn/47a23a3b43944d39a8911d5fedcec043.png" alt="在这里插入图片描述"></p> 
<h3><a name="t3"></a><a id="13__11"></a>1.3 使用场景</h3> 
<p>正向代理的典型用途是为防火墙内的局域网客户端提供访问服务器的途径，正向代理还可以使用缓冲特性减少网络利用率。</p> 
<ul><li>科学上网（翻墙）</li></ul> 
<p>有时候，用户想要访问某国外网站，该网站无法在国内直接访问，但是我们可以访问到一个代理服务器，这个代理服务器可以访问到这个国外网站。这样呢，用户对该国外网站的访问就需要通过代理服务器来转发请求，并且该代理服务器也会将请求的响应再返回给用户。这个上网的过程就是用到了正向代理。</p> 
<p><img src="https://img-blog.csdnimg.cn/9fff8ace5eba48d193cdf7d104eba629.png" alt="在这里插入图片描述"></p> 
<h3><a name="t4"></a><a id="14__22"></a>1.4 用途</h3> 
<ul><li><strong>突破访问显示</strong>：通过代理服务器，可以突破自身ip访问限制，访问国外网站等</li><li><strong>提高访问速度</strong>：通常代理服务器都设置一个较大的硬盘缓冲区，会将部分请求的响应保存到缓冲区中，当其他用户再访问相同的信息时，则直接由缓冲区中取出信息，传给用户，以提高访问速度</li><li><strong>隐藏客户端真实ip</strong>：上网者可以通过正向代理的方法隐藏自己的ip，免受攻击</li></ul> 
<h2><a name="t5"></a><a id="2_28"></a>2.<a href="https://so.csdn.net/so/search?q=%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86&amp;spm=1001.2101.3001.7020" target="_blank" class="hl hl-1" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.7020&quot;,&quot;dest&quot;:&quot;https://so.csdn.net/so/search?q=%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86&amp;spm=1001.2101.3001.7020&quot;,&quot;extra&quot;:&quot;{\&quot;searchword\&quot;:\&quot;反向代理\&quot;}&quot;}" data-tit="反向代理" data-pretit="反向代理">反向代理</a></h2> 
<h3><a name="t6"></a><a id="21__29"></a>2.1 概念</h3> 
<p>反向代理是指以代理服务器来接收客户端的请求，然后将请求转发给内部网络上的服务器，将从服务器上得到的结果返回给客户端，此时代理服务器对外表现为一个反向代理服务器。</p> 
<p>对于客户端来说，反向代理就相当于目标服务器，只需要将反向代理当作目标服务器一样发送请求就可以了，并且客户端不需要进行任何设置。</p> 
<h3><a name="t7"></a><a id="22__34"></a>2.2 特点</h3> 
<ul><li>正向代理需要配置代理服务器，而反向代理不需要做任何设置。</li><li>反向代理是<strong>代理服务器</strong>，为服务器收发请求，使真实服务器对客户端不可见。</li></ul> 
<p><img src="https://img-blog.csdnimg.cn/e93b8af934b14b769644ebdb94d142ed.png" alt="在这里插入图片描述"></p> 
<h3><a name="t8"></a><a id="23__40"></a>2.3 使用场景</h3> 
<p>反向代理的典型用途是将防火墙外的服务器提供给客户端访问，反向代理还可以为后端的多台服务器提供负载均衡，或者为后端较慢的服务器提供缓冲服务。</p> 
<p><img src="https://img-blog.csdnimg.cn/14e38ed5ee064d8ab0b08655950646b9.png" alt="在这里插入图片描述"></p> 
<h3><a name="t9"></a><a id="24__45"></a>2.4 用途</h3> 
<ul><li><strong>隐藏服务器真实ip</strong>：使用反向代理，可以对客户端隐藏服务器的ip地址</li><li><strong>负载均衡</strong>：反向代理服务器可以做负载均衡，根据所有真实服务器的负载情况，将客户端请求分发到不同的真实服务器上</li><li><strong>提高访问速度</strong>：反向代理服务器可以对静态内容及短时间内有大量访问请求的动态内容提供缓存服务，提高访问速度</li><li><strong>提供安全保障</strong>：反向代理服务器可以作为应用层防火墙，为网站提供对基于web的攻击行为（例如DoS/DDoS）的防护，更容易排查恶意软件等。还可以为后端服务器统一提供加密和SSL加速（如SSL终端代理），提供HTTP访问认证等。</li></ul> 
<h2><a name="t10"></a><a id="3_51"></a>3.正向代理和反向代理的异同</h2> 
<h3><a name="t11"></a><a id="31__52"></a>3.1 相同点</h3> 
<p>正向代理和反向代理所处的位置都是客户端和真实服务器之间，所做的事情也都是把客户端的请求转发给服务器，再把服务器的响应转发给客户端。</p> 
<h3><a name="t12"></a><a id="32__55"></a>3.2 不同点</h3> 
<ul><li>正向代理是<strong>客户端的代理</strong>，服务器不知道真正的客户端是谁；反向代理是服<strong>务器的代理</strong>，客户端不知道真正的服务器是谁</li><li>正向代理一般是<strong>客户端架设</strong>的；反向代理一般是<strong>服务器架设</strong>的</li><li>正向代理主要是用来解决<strong>访问限制问题</strong>；反向代理则是提供<strong>负载均衡、安全防护</strong>等作用。二者都能提高访问速度</li></ul> 
<h2><a name="t13"></a><a id="4_61"></a>4.通过故事理解正向代理和反向代理</h2> 
<h3><a name="t14"></a><a id="41__63"></a>4.1 正向代理</h3> 
<p>同学A急需一笔钱，他直接向富豪马云借钱，但是他俩之间毫无关系，结果当然是没有借到。经过一番打听，同学A的老师王先生是马云的好朋友，于是A同学请求王老师，让王老师帮忙向马云借钱，最终马云同意借钱给王老师，王老师把这笔钱转交给了A同学。</p> 
<p>上文就相当于一个正向代理的过程，A同学为客户端，马云为服务器，王老师为正向代理。A同学请求王老师向马云借钱，这个过程中A同学隐藏了自己的角色，马云事实上是不知道到底是谁借的钱。相当于服务器不知道真正发起请求的客户端是谁。</p> 
<h3><a name="t15"></a><a id="42__68"></a>4.2 反向代理</h3> 
<p>如果遇到困难需要拨打10086客服电话，可能一个地区的10086客服有几十个，但是我们不需要关心电话那头的人是谁。只需要拨通10086的总机号码，电话那头总有客服会回应。</p> 
<p>这里的10086总机号码就相当于反向代理，客户端不知道真正提供服务的人是谁。</p> 

</article>