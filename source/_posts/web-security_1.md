---
title: web安全相关-http响应头
date: 2021-05-24 10:13:37
tags:
- web
- security
categories:
- security
---


### WEB项目的HTTP HEADER安全漏洞梳理

###
<font color="#075DC4" size=2>这个网址可查询HTTP Header信息</font>
    <https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers>


#### X-Content-Type-Options响应头缺失
  X-Content-Type-Options HTTP 消息头相当于一个提示标志，被服务器用来提示客户端一定要遵循在 Content-Type 首部中对  MIME 类型 的设定，而不能对其进行修改。这就禁用了客户端的 MIME 类型嗅探行为，换句话说，也就是意味着网站管理员确定自己的设置没有问题。

```
  response.addHeader("X-Content-Type-Options", "nosniff");
```

#### X-XSS-Protection响应头缺失
  HTTP X-XSS-Protection 响应头是 Internet Explorer，Chrome 和 Safari 的一个特性，当检测到跨站脚本攻击 (XSS (en-US))时，浏览器将停止加载页面。

```
  response.addHeader("X-XSS-Protection", "1; mode=block"); //启动XSS；mode= block:检测到攻击，浏览器将阻止页面的呈现，而不是过滤页面中的XSS内容
  # response.addHeader("X-XSS-Protection", "1; mode=block"); // 禁用XSS
```

#### 启用了不安全的HTTP方法
  检测到目标WEB服务器配置成允许下列其中一个（或多个）HTTP方法：DELETE,SEARCH,COPY,MOVE,PROPFIND,PROPPATCH,MKCOL,LOCK,UNLOCK。 这些方法表示可能在服务器上使用了WebDAV。由于dav方法允许客户端操纵服务器上的文件，如果没有合理配置dav，有可能允许未授权的用户对其进行利用，修改服务器上的文件。

```
  response.addHeader("Access-Control-Allow-Methods", "GET,POST"); // 实际上DELETE等方法还是可以使用。不过漏洞扫描不出来了
```
```
  // 在过滤器中过滤
  String method = request.getMethod();  
  if(HttpMethod.GET.toString().equals(method)|| HttpMethod.POST.toString().equals(method)){  
      return true;
  }  
  return false;
```

#### X-Frame-Options响应头缺失
  The X-Frame-Options HTTP 响应头是用来给浏览器 指示允许一个页面 可否在 <frame>, <iframe>, <embed> 或者 <object> 中展现的标记。站点可以通过确保网站没有被嵌入到别人的站点里面，从而避免 clickjacking 攻击。

```
  response.addHeader("X-Frame-Options", "DENY");  // 不能被嵌入
  response.addHeader("X-Frame-Options", "SAMEORIGIN");  // 只能嵌入本站。
  response.addHeader("X-Frame-Options", "ALLOW-FROM https://example.com/"); // 指定能嵌入的站点
```

#### 响应头总结

```
  response.addHeader("X-Frame-Options", "SAMEORIGIN");
  response.addHeader("X-XSS-Protection", "1; mode=block");
  response.addHeader("X-Content-Type-Options", "nosniff");
  response.addHeader("X-Content-Security-Policy", "default-src 'self'");
  # other
  response.addHeader("Referer-Policy", "origin");
  response.addHeader("X-Permitted-Cross-Domain-Policies", "master-only");
  response.addHeader("X-Download-Options", "noopen");
  response.addHeader("Cache-control", "public");
```

#### 检测到目标主机可能存在缓慢的HTTP拒绝服务攻击
  缓慢的HTTP拒绝服务攻击时一种专门针对Web的应用层拒绝服务攻击，攻击者操纵网络上的肉鸡，对目标Web服务器进行海量HTTP请求攻击，直到服务器带宽被打满，造成了拒绝服务。

```
  # springboot
  # application-properties文件增加
  server.tomcat.max-threads=1000
  server.tomcat.max-connections=20000
  
  # tomact
  # server.xml <Connector /> 增加maxThreads、connectionTimeout
  <Connector port="80" maxHttpHeaderSize="8192"
    maxThreads="1000" minSpareThreads="25" maxSpareThreads="75"
    enableLookups="false" redirectPort="8443" acceptCount="100"
    connectionTimeout="20000" disableUploadTimeout="true" URIEncoding="UTF-8" maxPostSize="-1" server="unknown"/>

```


#### 检测到目标URL存在http host头攻击漏洞

```
  // 头攻击检测  过滤主机名
  String requestHost = request.getHeader("host");
  if (requestHost != null && !checkBlankList(requestHost)) {
      response.setStatus(403);
      return;
  }
  //判断主机是否存在白名单中
  private boolean checkBlankList(String host){
      if(host.contains("127.0.0.1")){//此处为自己网站的主机地址
          return true;
      }
      return false;
  }
```

