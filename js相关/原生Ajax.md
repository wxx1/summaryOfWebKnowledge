## 原生AJAX请求教程

ajax本质是在HTTP协议的基础上以异步的方式和服务器进行通信（可实现网页的局部刷新）

**异步**：指某段程序执行时不会阻塞其它程序执行，其表现形式为程序的执行顺序不依赖程序本身的顺序

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET','test.php',true);
xhr.send();
```

| 方法                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| open(method,url,async) | 规定请求类型、URL以及是否异步处理请求async：true(异步)，false(同步) |
| send(string)           | 将请求发送到服务器。string：仅用于POST请求                   |

#### POST请求

post请求需要添加一个请求头，让后台知道我们的请求参数格式，这样后台才能解析我们的数据。**另外，传输的数据需要格式化到send方法中**

```javascript
var xhr = new XMLHttpRequest();
xhr.open('POST'，'test.php',true);
//设置http头协议,把传递的post数据转化为xml格式,setRequestHeader方法必须放在open方法的后边调用
//浏览器的原生 <form> 表单，如果不设置 enctype 属性，那么最终就会以 application/x-www-form-urlencoded 方式提交数据,提交的数据按照 key1=val1&key2=val2 的方式进行编码
xhr.sendRequestHeader('content-type':'application/x-www-form-urlencoded');
xhr.send('fname=Bob&lname=Ford');//字符串类型
```

####  封装Ajax

- post  

```javascript
function ajaxPost(url, data,callback){
    var xhr = new XMLHttpRequest();
    xhr.open('POST',url,true);
    xhr.setRequestHeader('Content-type','application/x-www-form-urlencoded');
    xhr.send(data);
    
    xhr.onreadtstatechange = function(){
        if(xhr.readyState == 4 && xhr.status == 200){
            callback(xhr.responseText);
        }
    }
}

//调用
ajaxPost('/api/user','id=9&com=aicoder',function(data){
    //后台返回的数据就是字符串类型。要转成json，必须自己手段转换。
    var user = JSON.parse(data);
})
```

