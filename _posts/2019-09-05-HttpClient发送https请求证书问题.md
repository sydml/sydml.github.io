---
layout:     post
title:      
subtitle:   2019-09-05-HttpClient发送https请求证书问题
date:       2019-09-05
author:     SYDML
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - java
    - SSL
    - http
---
# 概念与理解
* http的中文叫做超文本传输协议,它负责完成客户端到服务端的一系列操作,是专门用来传输注入HTML的超媒体文档等web内容的协议,它是基于传输层的TCP协议的应用层协议
* https:https是基于安全套接字的http协议,也可以理解为是http+ssl/tls(数字证书)的组合
# 区别
* HTTP 的 URL 以 http:// 开头，而 HTTPS 的 URL 以 https:// 开头
* HTTP 是不安全的，而 HTTPS 是安全的
* HTTP 标准端口是 80 ，而 HTTPS 的标准端口是 443
* 在 OSI 网络模型中，HTTPS的加密是在传输层完成的,因为SSL是位于传输层的,TLS的前身是SSL,所以同理
* HTTP无需认证证书,而https需要认证证书 

# HTTPS工作原理
* 首先服务端给客户端传输证书,这个证书就是公钥,只是包含了很多的信息,比如说证书的办法机构,证书的过期时间
* 客户端进行证书的解析,比如说验证办法机构,过期时间,如果发现没有任何问题,就生成一个随机值(私钥),然后用证书对这个私钥进行加密,并发送给服务端
* 服务端使用私钥将这个信息进行解密,得到客户端的私钥,然后客户端和服务端就可以通过这个私钥进行通信了
* 服务端将消息进行对称加密(简单来说就是讲消息和私钥进行混合,除非知道私钥否则服务进行解密),私钥正好只有客户端和服务端知道,所以信息就比较安全了
* 服务端将进行对称加密后的消息进行传送
* 客户端使用私钥进行信息的解密
* **https就是使用了非对称加密(一对公私钥进行加密解密)进行公钥传输,然后客户端通过公钥加密将自己的私钥发给服务端,以后就可以使用这个私钥进行消息的收发了**
# SSLException问题解决
```
    public static CloseableHttpClient createSSLClientDefault() {
        try {
            //使用 loadTrustMaterial() 方法实现一个信任策略，信任所有证书
            // 信任所有
            SSLContext sslContext = new SSLContextBuilder().loadTrustMaterial(null, (chain, authType) -> true).build();
            //AllowAllHostnameVerifier:  作为主机名验证工具，实质上关闭了主机名验证，它接受任何
            //有效的SSL会话并匹配到目标主机。
            X509HostnameVerifier hostnameVerifier = new AllowAllHostnameVerifier();
            SSLConnectionSocketFactory sslConnectionSocketFactory = new SSLConnectionSocketFactory(sslContext, hostnameVerifier);
            return HttpClients.custom().setSSLSocketFactory(sslConnectionSocketFactory).build();
        } catch (KeyManagementException | KeyStoreException | NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
        return HttpClients.createDefault();
    }
```

`_摘自：https://blog.csdn.net/zhttly/article/details/82895713_`