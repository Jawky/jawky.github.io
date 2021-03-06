---
title: 对称加密和非对称加密
subtitle: "对称加密和非对称加密"
author: Jawky
layout: post
date: 2018/4/27 23:04:33   
tags:
    - 学习
---

> 面试中有被问到过对称加密和非对称加密的原理，当时一下子想不起来，虽然开发中有用到过MD5、RSA、AES等加密方式，但是从没有去把它们对号入座到对称和非对称加密中。

# **对称加密**	

**简介：**

&emsp;&emsp;对称加密(也叫私钥加密)指加密和解密使用相同密钥的加密算法。有时又叫传统密码算法，就是加密密钥能够从解密密钥中推算出来，同时解密密钥也可以从加密密钥中推算出来。而在大多数的对称算法中，加密密钥和解密密钥是相同的，所以也称这种加密算法为秘密密钥算法或单密钥算法。它要求发送方和接收方在安全通信之前，商定一个密钥。对称算法的安全性依赖于密钥，泄漏密钥就意味着任何人都可以对他们发送或接收的消息解密，所以密钥的保密性对通信性至关重要。

**特点：**

&emsp;&emsp;对称加密算法的特点是算法公开、计算量小、加密速度快、加密效率高。
不足之处是，交易双方都使用同样钥匙，安全性得不到保证。此外，每对用户每次使用对称加密算法时，都需要使用其他人不知道的惟一钥匙，这会使得发收信双方所拥有的钥匙数量呈几何级数增长，密钥管理成为用户的负担。对称加密算法在分布式网络系统上使用较为困难，主要是因为密钥管理困难，使用成本较高。而与公开密钥加密算法比起来，对称加密算法能够提供加密和认证却缺乏了签名功能，使得使用范围有所缩小。在计算机专网系统中广泛使用的对称加密算法有DES和IDEA等。美国国家标准局倡导的AES即将作为新标准取代DES。

**具体算法：**

&emsp;&emsp;DES算法，3DES算法，TDEA算法，Blowfish算法，RC5算法，IDEA算法。

**原理应用：**

&emsp;&emsp;对称加密算法的优点在于加解密的高速度和使用长密钥时的难破解性。假设两个用户需要使用对称加密方法加密然后交换数据，则用户最少需要2个密钥并交换使用，如果企业内用户有n个，则整个企业共需要n×(n-1) 个密钥，密钥的生成和分发将成为企业信息部门的恶梦。对称加密算法的安全性取决于加密密钥的保存情况，但要求企业中每一个持有密钥的人都保守秘密是不可能的，他们通常会有意无意的把密钥泄漏出去--如果一个用户使用的密钥被入侵者所获得，入侵者便可以读取该用户密钥加密的所有文档，如果整个企业共用一个加密密钥，那整个企业文档的保密性便无从谈起。

# 非对称加密

**简介：**

&emsp;&emsp;非对称加密算法是一种密钥的保密方法。

&emsp;&emsp;非对称加密算法需要两个密钥:公开密钥(publickey)和私有密钥(privatekey)。公开密钥与私有密钥是一对，如果用公开密钥对数据进行加密，只有用对应的私有密钥才能解密;如果用私有密钥对数据进行加密，那么只有用对应的公开密钥才能解密。因为加密和解密使用的是两个不同的密钥，所以这种算法叫作非对称加密算法。 非对称加密算法实现机密信息交换的基本过程是:甲方生成一对密钥并将其中的一把作为公用密钥向其它方公开;得到该公用密钥的乙方使用该密钥对机密信息进行加密后再发送给甲方;甲方再用自己保存的另一把专用密钥对加密后的信息进行解密。

&emsp;&emsp;另一方面，甲方可以使用乙方的公钥对机密信息进行签名后再发送给乙方;乙方再用自己的私匙对数据进行验签。

&emsp;&emsp;甲方只能用其专用密钥解密由其公用密钥加密后的任何信息。 非对称加密算法的保密性比较好，它消除了最终用户交换密钥的需要。

**特点：**
	
&emsp;&emsp;算法强度复杂、安全性依赖于算法与密钥但是由于其算法复杂，而使得加密解密速度没有对称加密解密的速度快。对称密码体制中只有一种密钥，并且是非公开的，如果要解密就得让对方知道密钥。所以保证其安全性就是保证密钥的安全，而非对称密钥体制有两种密钥，其中一个是公开的，这样就可以不需要像对称密码那样传输对方的密钥了。这样安全性就大了很多。

**具体算法：**

&emsp;&emsp;RSA、Elgamal、背包算法、Rabin、HD,ECC（椭圆曲线加密算法）。