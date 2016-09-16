---
title: Websocket简析
layout: post
tags:
  - websocket
---



这段时间一直在用websocket来传输数据，不管是前端和服务器，后端和服务器，甚至后端C++层里，都大量的使用了websocket通信，而且也越发的觉得websocket甚是好用。
所以这里将websocket相应的信息整理一下。



相关链接：



1.[Websocket协议](http://blog.csdn.net/mary736265635/article/details/47757237)


2.[Websocket协议解析](http://www.king-liu.net/?p=772)

#何谓Websocket？


WebSocket是HTML5出的东西（协议），  其实可以说与http基本没有关系。相对于HTTP这种非持久的协议来说，Websocket是一个持久化的协议，并且是一个双工的通信形式。


简单的举个例子吧，用目前应用比较广泛的PHP生命周期来解释：


HTTP的生命周期通过Request来界定，也就是一个Request 一个Response，那么在HTTP1.0中，这次HTTP请求就结束了。
在HTTP1.1中进行了改进，使得有一个keep-alive，也就是说，在一个HTTP连接中，可以发送多个Request，接收多个Response。
但是请记住 Request = Response ， 在HTTP中永远是这样，也就是说一个request只能有一个response。而且这个response也是被动的，不能主动发起。


但是对于Websocket,首先它是基于HTTP协议的，或者说借用了HTTP的协议来完成一部分握手。
在握手阶段是一样的：


首先我们来看个典型的Websocket握手（借用Wikipedia的。。）


GET /chat HTTP/1.1


Host: server.example.com


Upgrade: websocket


Connection: Upgrade


Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==


Sec-WebSocket-Protocol: chat, superchat


Sec-WebSocket-Version: 13


Origin: http://example.com


熟悉HTTP的童鞋可能发现了，这段类似HTTP协议的握手请求中，多了几个东西：


Upgrade: websocket


Connection: Upgrade


这个就是Websocket的核心了，告诉Apache、Nginx等服务器：注意啦，我发起的是Websocket协议，快点帮我找到对应的助理处理~不是那个老土的HTTP。


Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==


Sec-WebSocket-Protocol: chat, superchat


Sec-WebSocket-Version: 13


首先，Sec-WebSocket-Key 是一个Base64 encode的值，这个是浏览器随机生成的，告诉服务器：泥煤，不要忽悠窝，我要验证尼是不是真的是Websocket助理。


然后，Sec_WebSocket-Protocol 是一个用户定义的字符串，用来区分同URL下，不同的服务所需要的协议。简单理解：今晚我要服务A，别搞错啦~


最后，Sec-WebSocket-Version 是告诉服务器所使用的Websocket Draft（协议版本），在最初的时候，Websocket协议还在 Draft 阶段，各种奇奇怪怪的协议都有，而且还有很多期奇奇怪怪不同的东西，什么Firefox和Chrome用的不是一个版本之类的，当初Websocket协议太多可是一个大难题。。不过现在还好，已经定下来啦~大家都使用的一个东西~ 脱水：服务员，我要的是13岁的噢→_→

然后服务器会返回下列东西，表示已经接受到请求， 成功建立Websocket啦！


HTTP/1.1 101 Switching Protocols


Upgrade: websocket


Connection: Upgrade


Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=


Sec-WebSocket-Protocol: chat


这里开始就是HTTP最后负责的区域了，告诉客户，我已经成功切换协议啦~


Upgrade: websocket


Connection: Upgrade


依然是固定的，告诉客户端即将升级的是Websocket协议，而不是mozillasocket，lurnarsocket或者shitsocket。


然后，Sec-WebSocket-Accept 这个则是经过服务器确认，并且加密过后的 Sec-WebSocket-Key。服务器：好啦好啦，知道啦，给你看我的ID CARD来证明行了吧。。


后面的，Sec-WebSocket-Protocol 则是表示最终使用的协议。


至此，HTTP已经完成它所有工作了，接下来就是完全按照Websocket协议进行了。
