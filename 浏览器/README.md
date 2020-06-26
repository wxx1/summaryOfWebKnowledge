## 跨域

#### 什么是跨域

跨域是指一个域下的文档或脚本试图去请求另一个域下的资源，这里的跨域是广义的。

广域的跨域：

> 1) 资源跳转：a链接、重定向、表单提交
>
> 2）资源嵌入：`<link> <script> <img> <frame>`等dom标签
>
> 3）脚本请求：js发起的ajax请求、dom和js对象的跨域操作

我们通常说的跨域是狭义的，是由浏览器同源策略限制的一类请求场景

**同源策略**

同源策略是一种约定，如果缺少了同源策略，浏览器很容易受到XSS，CSFR等攻击。所谓同源是指“协议+域名—+端口”三者相同，即使两个不同的域名指向同一IP地址，也非同源。

同源策略限制以下几种行为：

1）cookie、LocalStorage和IndexDB无法读取

2）DOM和JS对象无法获得

3）AJAX请求不能发送

**跨域解决方案**

1. 通过jsonp跨域

   ​	**利用`<script>`没有跨域限制**

   ​	当需要通信时，创建一个`<script>`元素，地址指向第三方的API网址，如`<script src="http://www.example.net/api?param1=param2"/>`，并且提供一个回调函数来接收数据（函数名可约定，或通过地址传参），第三方响应一个json数据包装 

2. 跨域资源共享（CORS）

   ​	 只服务端设置Access-Control-Allow-Origin（允许访问的域）即可 

   - 过程：浏览器发送一个cros请求，在请求头部添加一个Origin字段，来说明来自哪个源（协议 + 域名 + 端口），服务器根据这个值来决定是否同意此次请求
     如果服务器不允许，则返回的信息中不会有```Access-cortol-allow-origin```
     如果服务器许可，则服务器返回的响应中会多出Access-Control-字段
   - 非简单请求
     请求方法是put\delete,Content-type为application/json
     在正式通信前则会增加一次options请求，预检请求
     询问服务器，网页所在的预是否在服务器许可名单中，，
         "预检"请求的头信息包括两个特殊字段。Access-Control-Request-Method(请求方法)put...
                                       Access-Control-Request-Headers 指定浏览器CORS请求会额外发送的头信息字段 
     只有得到肯定答复,浏览器才会发出正式XMLHTTPRequest请求，否则会报错

3. nginx代理跨域

   ​	 同源策略是浏览器的安全策略，不是HTTP协议的一部分。服务器端调用HTTP接口只是使用HTTP协议，不会执行JS脚本，不需要同源策略，也就不存在跨越问题。 

   ​		 通过nginx配置一个代理服务器  做跳板机，反向代理访问 

   - 配置
     events：用于nginx工作模式的配置
     server：用于进行服务器访问信息的配置
     location：用于进行访问路由的配置
     upstream：用于进行负载均衡的配置

4. webpack proxy

