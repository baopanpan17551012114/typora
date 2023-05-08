# rpc和http的区别是什么 各自的优缺点有哪些

http://m.ccutu.com/244407.html

http是指从客户端到服务器端的请求消息，rpc是远程过程调用协议。

## 1rpc和http的区别是什么

rpc和http的存在重大不同的是：

http请求是使用具有标准语义的通用的接口定向到资源的，这些语义能够被中间组件和提供服务的来源机器进行解释。结果是使得一个应用支持分层的转换(layers of transformation)和间接层(indirection)，并且独立于消息的来源，这对于一个Internet规模、多个组织、无法控制的可伸缩性的信息系统来说，是非常有用的。

与之相比较，rpc的机制是根据语言的API(language API)来定义的，而不是根据基于网络的应用来定义的。

## 2HTTP和RPC的优缺点

主要来阐述HTTP和RPC的异同，让大家更容易根据自己的实际情况选择更适合的方案。

**传输协议**

RPC:可以基于TCP协议，也可以基于HTTP协议

HTTP:基于HTTP协议

**传输效率**

RPC:使用自定义的TCP协议，可以让请求报文体积更小，或者使用HTTP2协议，也可以很好的减少报文的体积，提高传输效率

HTTP:如果是基于HTTP1.1的协议，请求中会包含很多无用的内容，如果是基于HTTP2.0，那么简单的封装以下是可以作为一个RPC来使用的，这时标准RPC框架更多的是服务治理

**性能消耗**

RPC:可以基于thrift实现高效的二进制传输

HTTP:大部分是通过json来实现的，字节大小和序列化耗时都比thrift要更消耗性能

**负载均衡**

RPC：基本都自带了负载均衡策略

HTTP：需要配置Nginx，HAProxy来实现

**服务治理**

RPC：能做到自动通知，不影响上游

HTTP:需要事先通知，修改Nginx/HAProxy配置

**总结**

RPC主要用于公司内部的服务调用，性能消耗低，传输效率高，服务治理方便。

HTTP主要用于对外的异构环境，浏览器接口调用，APP接口调用，第三方接口调用等。



# [rpc接口和http接口的区别和联系](https://www.cnblogs.com/hustdc/p/10166515.html)

1 什么是http接口

http接口是基于http协议的post和get接口。

2 什么是rpc接口

rpc接口就相当于调用本地接口一样调用远程服务的接口。

3 常用的rpc框架

thrift

自动代码生成，生成rpc的客户端和服务器端。

dubbo

brpc

等