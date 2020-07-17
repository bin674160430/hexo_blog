---
title: Session, Cookie, Token
date: 2020-07-17 08:56:00
categories:
    - 网站
tags:
    - web
---

# Cookie 和 Session

`HTTP`协议是一种`无状态协议`，每次服务端接收客户端的请求时，都是一个全新的请求，服务器并不知道客户端的历史请求记录，`Session, Cookie` 的主要目的是为了弥补 `HTTP`的无状态特性。

# Session 

客户端请求服务端，服务端为这次请求开辟一块`内存空间`, 存储结构为 `ConcurrentHashMap`，`Session`弥补了`HTTP`的无状态特性，服务端可以利用`Session`存储客户端在同一个会话期间的一些信息

# Session 怎么判断同一会话

1. 服务端第一次接收到请求，开启了一块`Session`内存空间，同时生成了一个 `sessionId`，并通过响应头的 `Set-Cookie：JSESSIONID=XXXXXXX` 命令，向客户端发送要求设置 Cookie 的响应； 
2. 客户端收到响应后，在本机客户端设置了一个 `JSESSIONID=XXXXXXX` 的 Cookie 信息，该 Cookie 的过期时间为浏览器会话结束；
3. 接下来客户端每次向同一个网站发起请求时，请求头部都会带上`Cookie`信息，服务端通过读取请求头中`Cookie`的`JSESSIONID`， 得到`sessionId`

# Cookie

`HTTP`协议中的 cookie 包括 Web Cookie 和 浏览器 Cookie， 它是服务端发送到浏览器的一小块数据，浏览器会进行存储，并与后续的请求一起发送到服务端；多用于会话管理。

# 创建Cookie

当接收到客户端发出的`HTTP`请求时，服务端发送`Response.Headers`带有`Set-Cookie`，`Cookie`通常由浏览器存储，然后将`Cookie`与`HTTP` `Request.Headers`一同向服务端发起请求

{% image cookie-1.jpg 示例图 %}

{% image cookie-2.jpg 示例图 %}

# 两种 Cookies

1. 会话Cookies`Session Cookies`， 不包含`有效期`，没有指定`Expries(特定日期)`或`Max-Age(时间长度)`指令，存储在内存中，不会写入磁盘，客户端关闭时会删除；*注意：Web浏览器可能会使用会话还原，这会使大多数会话Cookie保持永久状态，就像从未关闭浏览器一样。*

2. 永久性Cookies `Persistent Cookie`，包含`有效期`，写入磁盘，在指定`Expries(特定日期)`或`Max-Age(时间长度)`外过期，将从磁盘中删除。

   ```javascript
   Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
   ```

# Cookie 的 Secure 和 HttpOnly 标记

安全的 `Cookie`需要经过 `HTTPS`协议通过加密的方式发送到服务器，即使是安全的，也不应该将敏感信息存储在`Cookie`中，因为`Cookie`本质上是不安全的，并且此标记不能提供真正的保护

> **HttpOnly的作用**
>
> - `HttpOnly`是微软对`Cookie`作的扩展，该值指定`Cookie`是否可通过客户端脚本访问
> - 如果在Cookie中没有设置`HttpOnly`属性为true，有可能导致`Cookie`被窃取用户信息，伪装用户进行跨站脚本攻击

# Cookie 的作用域

`Domain`和`Path`标识定义了`Cookie`的作用域：即`Cookie`应该发送给哪些`URL`

`Domain`标识指定了哪些主机可以接受`Cookie`，如果不指定，默认当前主机**（不包括子域名）**；如果指定了`Domain`，包含子域名。

```javascript
/**
	设置Domain=mozilla.org
	Cookie也包含在子域名, developer.mozilla.org
	
	设置Path=/docs  以下地址都会匹配
	/docs
    /docs/web/
    /docs/web/http
*/
```

# JSON Web Token 简称 JWT， JSON令牌

`RFC 7519`中定义的用于`安全的`将信息作为`Json 对象`进行传输的一种形式，JWT中存储的信息是经过`数字签名`的，可以被信任，可以使用`HMAC算法`或`RSA/ECDSA公用/专用密钥`进行签名。

## 主要用于：

- `认证 Authorization`: 这是使用`JWT`最常见的一种情况，一旦用户登录，后面每个请求都会包含`JWT`，从而允许用户访问该令牌所允许的路由、服务和资源。`单点登录`是目前广泛使用`JWT`的一项功能，因为它的开销很小。
- `信息交换 Information Exchange`: `JWT`是能够安全传递信息的一种方式，通过`公钥、私钥对JWT`进行签名认证，此外，由于签名是使用`head`和`payload`计算的，因此还能验证内容是否早到篡改。

## 格式：

由`Header`, `Payload`, `Signature`三部分组成，每个部分用`.`分割，一个简单定义如下所示

{% image jwt.jpg 示例图 %}

## Header

`Header`是`JWT`的标头，通常由两部分组成：`令牌类型`和使用的算法`签名算法`,例如 `HMAC SHA256 RSA`

```javascript
// Header JSON块被 Base64Url 编码形成 JWT的第一部分
{ "alg": "HS256", "typ": "JWT" }
```

## Payload

包含一个声明，声明有关实体（通常是用户）和其它数据的声明，共有三种类型声明： `registered, public, private`

- `registered`，包含一组使用的预定义声明

  |          ISS          |  签发人  |
  | :-------------------: | :------: |
  |     iss (issuer)      |  签发人  |
  | exp (expiration time) | 过期时间 |
  |     sub (subject)     |   主题   |
  |    aud (audience)     |   受众   |
  |   nbf (Not Before)    | 生效时间 |
  |    iat (Issued At)    | 签发时间 |
  |     jti (JWT ID)      |   编号   |

- `public` , 公共的声明，可以添加任何的信息，一般添加用户的相关信息和其它业务需要的信息，不建议添加敏感信息，因为这部分在客户端可以解密

- `private`, 自定义声明，同意使用塔门的各方之间共享信息，既不是注册声明，也不是公共声明

```javascript
// Payload Json 会被 Base64Url 编码形成JWT的第二部分
{
    "sub": "123456789",
    "name": "John Doe",
    "admin": true
}
```

## Signature

由 `header(base64后的), payload(base64后的), secret`

例如需要的 `HMAC SHA256`算法进行签名

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

签名用于验证消息在此过程中没有更改，并且对于使用私钥进行签名的令牌，它还可以验证 JWT 的发送者的真实身份

## Demo

把上面的三个由点分隔的 Base64-URL 字符串部分组成在一起，这个字符串可以在 HTML 和 HTTP 环境中轻松传递这些字符串。

下面是一个完整的 JWT 示例，它对 header 和 payload 进行编码，然后使用 signature 进行签名

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

{% image jwt-0.jpg 示例图 %}

原文：https://cxuan.blog.csdn.net/article/details/105322171

