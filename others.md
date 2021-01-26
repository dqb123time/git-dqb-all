# HTTP and RPC

https://www.zhihu.com/question/41609070  既然有 HTTP 请求，为什么还要用 RPC 调用？

https://developer.aliyun.com/article/652743   RPC和HTTP服务对比

https://blog.csdn.net/qq_44712437/article/details/109148135  初识RPC框架+JAVA实现

 [JAVA RPC (七) 之手把手从零教你写一个生产级RPC之client的代理](https://www.cnblogs.com/zyl2016/p/10770625.html)

[HTTP和RPC的优缺点](https://segmentfault.com/a/1190000015920678) 

在HTTP和RPC的选择上，可能有些人是迷惑的，主要是因为，有些RPC框架配置复杂，如果走HTTP也能完成同样的功能，那么为什么要选择RPC，而不是更容易上手的HTTP来实现了。
本文主要来阐述HTTP和RPC的异同，让大家更容易根据自己的实际情况选择更适合的方案。

- 传输协议
  - RPC，可以基于TCP协议，也可以基于HTTP协议
  - HTTP，基于HTTP协议
- 传输效率
  - RPC，使用自定义的TCP协议，可以让请求报文体积更小，或者使用HTTP2协议，也可以很好的减少报文的体积，提高传输效率
  - HTTP，如果是基于HTTP1.1的协议，请求中会包含很多无用的内容，如果是基于HTTP2.0，那么简单的封装以下是可以作为一个RPC来使用的，这时标准RPC框架更多的是服务治理
- 性能消耗，主要在于序列化和反序列化的耗时
  - RPC，可以基于thrift实现高效的二进制传输
  - HTTP，大部分是通过json来实现的，字节大小和序列化耗时都比thrift要更消耗性能
- 负载均衡
  - RPC，基本都自带了负载均衡策略
  - HTTP，需要配置Nginx，HAProxy来实现
- 服务治理（下游服务新增，重启，下线时如何不影响上游调用者）
  - RPC，能做到自动通知，不影响上游
  - HTTP，需要事先通知，修改Nginx/HAProxy配置

总结：
  RPC主要用于公司内部的服务调用，性能消耗低，传输效率高，服务治理方便。HTTP主要用于对外的异构环境，浏览器接口调用，APP接口调用，第三方接口调用等。



# 分布式ID神器之雪花算法简介

https://zhuanlan.zhihu.com/p/85837641

https://github.com/baidu/uid-generator/blob/master/README.zh_cn.md



# HTTP keep-alive和TCP keepalive的区别

https://my.oschina.net/passerman/blog/1822421