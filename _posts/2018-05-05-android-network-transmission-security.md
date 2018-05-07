---
title: App安全之网络传输安全
subtitle: "App安全之网络传输安全"
author: Jawky
layout: post
date: 2018/5/5 23:37:55     
tags:
    - 学习
---

面试过程中被问到几次关于App网络传输安全的问题，所以总结一下。

# App安全之网络传输安全

移动端App安全如果按CS结构来划分的话，主要涉及客户端本身数据安全，Client到Server网络传输的安全，客户端本身安全又包括代码安全和数据存储安全。所以当我们谈论App安全问题的时候一般来说在以下三类范畴当中。

- App代码安全，包括代码混淆，加密或者app加壳。
- App数据存储安全，主要指在磁盘做数据持久化的时候所做的加密。
- App网络传输安全，指对数据从客户端传输到Server中间过程的加密，防止网络世界当中其他节点对数据的窃听。

**安全相关的基础概念**

网络安全相关的概念非常之多，要在广度和深度上都有所造诣很困难。但如果只是站在保障App通信基本安全这个维度上，做到合理使用安全算法，比大部分人所预期的都要简单很多。以下这些基础概念是必备知识：

- 对称加密算法，代表算法AES
- 非对称加密算法，代表算法RSA，ECC
- 电子签名，用于确认消息发送方的身份
- 消息摘要生成算法，MD5，SHA，用于检测消息是否被第三方修改过

**App网络安全实战**

在App安全上的投入再多也不会过，不过安全问题上所投入的开发资源应该根据开发团队技术积累，产品发布deadline，用户规模及产品关注度等综合因素考量。如果可以的话最好做到以下几点：

**All Traffic HTTPS**

全站使用HTTPS，而且是强制使用。所有的流量都应该在HTTPS上产生，没有人可以决定哪些流量是可以不用考虑安全问题的。如果自建长连接使用tcp，udp或者其他网络协议，也应该实现类似HTTPS的密钥协商流程。

**Certificate Pinning**

RSA的签名机制虽然看着安全，一旦出现上游证书颁发机构私钥泄漏，或者签名流程发现漏洞等情况，中间人攻击还是会导致数据被第三方破解甚至被钓鱼。Certificate Pinning是一种与服务器证书强绑定的机制，要么绑定证书本身，需要证书更新机制配合加强安全性，要么使用私钥绑定，这样更新证书的时候只要保证私钥不变即可。现在流行的HTTP framework，iOS端如AFNetworking，Android端如OKHttp都支持Certificate Pinning。

**Perfect Forward Secrecy**

很多人会觉得非对称加密算法足够安全，只要使用了RSA或者AES，加密过后的数据就认为安全。但没有绝对的安全，无论是RSA或者AES算法本身都有可能在未来某一天被破解，正如当年的DES，甚至有传言NSA正如当年掌握了differential cryptanalysis一样，现在已经获取了某种方法来破解当前互联网当中的部分网络流量，至于到底是RSA还是AES就不得而知了。

Forward Secrecy就是为了避免某个私钥的泄漏或者被破解而导致历史数据一起泄漏。现在google的https配置所使用的是TLS_ECDHE_RSA算法，每次对称密钥的协商都是使用ECC生成临时的公钥私钥对（之前提到过ECC在快速生成密钥对上有优势），身份验证使用RSA算法进行签名。

如做不到以上至少要考虑做到以下几点：

- 1、尽量使用https

	https可以过滤掉大部分的安全问题。https在证书申请，服务器配置，性能优化，客户端配置上都需要投入精力，所以缺乏安全意识的开发人员容易跳过https，或者拖到以后遇到问题再优化。https除了性能优化麻烦一些以外其他都比想象中的简单，如果没精力优化性能，至少在注册登录模块需要启用https，这部分业务对性能要求比较低。
- 2、不要穿输密码

	不知道现在还有多少app后台是明文存储密码的。无论客户端，server还是网络传输都要避免明文密码，要使用hash值。客户端不要做任何密码相关的存储，hash值也不行。存储token进行下一次的认证，而且token需要设置有效期，使用refresh token去申请新的token。

- 3、Post并不比Get安全
 
	事实上，Post和Get一样不安全，都是明文。参数放在QueryString或者Body没任何安全上的差别。在Http的环境下，使用Post或者Get都需要做加密和签名处理。

- 4、不要使用301跳转

    301跳转很容易被Http劫持攻击。移动端http使用301比桌面端更危险，用户看不到浏览器地址，无法察觉到被重定向到了其他地址。如果一定要使用，确保跳转发生在https的环境下，而且https做了证书绑定校验。

- 5、http请求都带上MAC

	所有客户端发出的请求，无论是查询还是写操作，都带上MAC（Message Authentication Code）。MAC不但能保证请求没有被篡改（Integrity），还能保证请求确实来自你的合法客户端（Signing）。当然前提是你客户端的key没有被泄漏，如何保证客户端key的安全是另一个话题。MAC值的计算可以简单的处理为hash（request params＋key）。带上MAC之后，服务器就可以过滤掉绝大部分的非法请求。MAC虽然带有签名的功能，和RSA证书的电子签名方式却不一样，原因是MAC签名和签名验证使用的是同一个key，而RSA是使用私钥签名，公钥验证，MAC的签名并不具备法律效应。

- 6、http请求使用临时密钥

	高延迟的网络环境下，不经优化https的体验确实会明显不如http。在不具备https条件或对网络性能要求较高且缺乏https优化经验的场景下，http的流量也应该使用AES进行加密。AES的密钥可以由客户端来临时生成，不过这个临时的AES key需要使用服务器的公钥进行加密，确保只有自己的服务器才能解开这个请求的信息，当然服务器的response也需要使用同样的AES key进行加密。由于http的应用场景都是由客户端发起，服务器响应，所以这种由客户端单方生成密钥的方式可以一定程度上便捷的保证通信安全。

- 7、AES使用CBC模式

	不要使用ECB模式，原因前面已经分析过，记得设置初始化向量，每个block加密之前要和上个block的秘文进行运算。AES加密模式可自行去百度谷歌。