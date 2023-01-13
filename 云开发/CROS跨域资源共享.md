[所有人都应该知道的跨域及CORS - 知乎](https://zhuanlan.zhihu.com/p/53996160)
[CORS (Cross-Origin Resource Sharing) - FastAPI](https://fastapi.tiangolo.com/tutorial/cors/)
## 背景
说起跨域，很多web程序员并不陌生，出于对安全问题的考虑，1995年由Netscape提出同源策略，浏览器在发送Ajax请求时，只接收同域服务器响应的数据资源；那么什么才算同域呢？很简单，**协议、域名、端口全部相同才算同一域下**，三个条件有一个不一致，都不算同域，既跨域；



> [!NOTE] 同源的含义
> An origin is the combination of protocol (`http`, `https`), domain (`myapp.com`, `localhost`, `localhost.tiangolo.com`), and port (`80`, `443`, `8080`).
> So, all these are different origins:
> -   `http://localhost`
> -   `https://localhost`
> -   `http://localhost:8080`
> 
> Even if they are all in `localhost`, they use different protocols or ports, so, they are different "origins".



因此，即使是我们自己的域名服务器，而二级域名或三级域名不一致，也会出现跨域，如：[http://img.xiling.me](https://link.zhihu.com/?target=http%3A//img.xiling.me) 与 [http://blog.xiling.me](https://link.zhihu.com/?target=http%3A//blog.xiling.me) 之间需要数据交互，就跨域了；

这就让人很苦恼了，明明都是我自己的域名，我自己的服务器，还是受到了同源策略的保护无法进行数据交互；这不能算是设计缺陷，只能说当年考虑不周全，也有可能因为当年网络通信协议的不健全，原因不得而知，各路解决方案中，最为典型的有两个：JSONP和同域代理

终于有人看不下去了，提出了浏览器与服务器跨域通信的安全性通信策略，它就是我们今天的主角 **跨域资源共享(Cross-origin resource sharing)，简称 CORS ;** 有了它，大家可以安全放心的抛弃 JSONP 和 同域代理了，步入正统的跨域数据交互的殿堂；

## CROS本质

CORS 的使用得益于 HTTP-1.1 协议版本的推广的大面积使用，它由一系列传输的[HTTP头](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Glossary/Header)组成，这些HTTP头有两个作用，
1. 用于阻止还是允许浏览器向其他域名发起请求；
2. 用于接受还是拒绝其他域名返回的响应数据；

也就意味着，我们只要搞清楚**什么样的头信息是控制浏览器发送还是不发送请求，什么样的头信息控制浏览器接受还是拒绝服务器的响应数据**，这两点搞明白，CORS就算彻底搞清楚了

## CROS请求头

我们先从一个普通的Ajax跨域说起，可能还有人不知道这个事情，就是Ajax在发送跨域请求时，请求是能够正常发送出去的，同时服务器也是可以正常接收并能够做出响应的：

![](https://pic2.zhimg.com/80/v2-b4a5c3b06bd8354041223a7aba899b2d_1440w.webp)

  

再次强调，跨域是浏览器正确做出请求，服务器也能正确做出响应，是浏览器拒绝接收跨域服务器返回的数据；

而在上面的请求头中，我们能够发现与普通的HTTP请求不一样的是，添加了Origin请求头，它指示了请求来自于哪个站点，这个请求头是浏览器在发现本次为跨域请求时自动添加的，目的就是告知服务器本次请求是那个域发起的，而此时，如果服务器允许本次请求响应的数据是可以共享的，那么服务器需要添加一个 Access-Control-Allow-Origin 响应头，并指明可以共享数据的域，如下：

![](https://pic2.zhimg.com/80/v2-c022a92b325e06ee466ec41f3d7b6d79_1440w.webp)

  

而Access-Control-Allow-Origin 响应头的值，可以设置为“*” (星号)，表示可以与任意域进行数据共享； 

很多人也认为使用CORS解决跨域很简单，只需要在服务器添加响应头 “ Access-Control-Allow-Origin ：* ” 就可以了，

其实不然，因为在CORS中，所有的跨域请求被分为了两种类型，一种是简单请求，一种是复杂请求 (严格来说应该叫‘需预检请求’)；简单请求与普通的ajax请求无异；但复杂请求，必须在正式发送请求前先发送一个OPTIONS方法的请求已得到服务器的同意，若没有得到服务器的同意，浏览器不会发送正式请求；

## CORS头信息对应含义
Access-Control-Allow-Origin ： 指示请求的资源能共享给哪些域，可以是具体的域名或者*表示所有域。

Access-Control-Allow-Credentials ： 指示当请求的凭证标记为 true 时，是否响应该请求。

Access-Control-Allow-Headers ： 用在对预请求的响应中，指示实际的请求中可以使用哪些 HTTP 头。

Access-Control-Allow-Methods： 指定对预请求的响应中，哪些 HTTP 方法允许访问请求的资源。

Access-Control-Expose-Headers ： 指示哪些 HTTP 头的名称能在响应中列出。

Access-Control-Max-Age ： 指示预请求的结果能被缓存多久。

Access-Control-Request-Headers ：用于发起一个预请求，告知服务器正式请求会使用那些 HTTP 头。

Access-Control-Request-Method ： 用于发起一个预请求，告知服务器正式请求会使用哪一种 HTTP 请求方法。

Origin ： 指示获取资源的请求是从什么域发起的