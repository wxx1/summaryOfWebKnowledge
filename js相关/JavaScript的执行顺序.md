### JavaScript的执行顺序

JavaScript的执行顺序是至上而下的，除非特别说明，JavaScript代码不会等到页面加载完毕后才执行

比如以下代码

```html
<script type="test/javascript">
	document.getElementById('ele').innerHtml = 'Hello World'
</script>

<div id='ele'>dffsfs</div>
```

运行该页面你会得到错误信息：‘’document.getElementById('ele') is null‘’，原因是当上面的JavaScript运行时，页面还没有Id为‘ele’的DOM元素

解决方法：

1. 方法一：把JavaScript放在HTML代码之后
2. 方法二：等到网页加载完毕后，运行该JavaScript代码，你可以使用传统的解决办法（load）onload事件在整个页面都加载完成后才触发，可以把JS脚本放在onload里面执行。

##### 外部script文件放页面内容后面好

 无论如何包含代码，只要不存在defer和async属性，浏览器都会按照``<script>``元素在页面中出现的先后顺序对它们依次进行解析。换句话说，在第一个``<script>``元素包含的代码解析完成后，第二个``<script>``包含代码才会被解析，然后才是第三个..... 

在head元素里包含所有JavaScript文件，就必须等到全部JavaScript代码都被下载、解析和执行完成以后，才能呈现页面的内容（浏览器在遇到<body>标签时才开始呈现内容）。在需要很多JavaScript文件时候，浏览器呈现页面会出现明显的延迟，延时期间浏览器是一片空白。

所以，外部script文件放页面内容后面。这样，在解析JavaScript代码之前，页面内容将完全呈现出来。

**有种情况是JavaScript放哪里都一样的，那就是内容是依赖JavaScript的执行渲染时候，放哪都一样。** 

### document ready和window.onload事件的区别

#### onload

load是一个事件，会在页面包含文件资源加载完成后触发

支持该事件的HTML标签：

```html
body、frame、img、link、script、iframe
```

触发时间： 当页面完全加载后（包括所有图像、JavaScript文件等外部资源），就会触发该事件。这方式跟在body上写onload效果是一样的。 

#### jQuery document ready

从原生JS来讲，并没有ready这种事件

document ready到底是什么ready： **ready事件在DOM结构绘制完成之后就会执行 **， ，这样能确保就算有大量的媒体文件没加载出来，JS代码一样可以执行。  

- jQuery怎么实现$(document).ready
  - 原理：在jQuery脚本加载时候，会监听DOMContentLoaded事件。当该事件触发时，会执行ready事件的回调



#### ready与onload谁更快

**ready**更快

 在Dom节点创建完成后执行。**通常一个页面加载的顺序是，域名解析——加载html——加载js和css——加载图片等其他信息**。那么Dom Ready应该在“加载js和css”和“加载图片等其他信息”之间。  那么Dom Load就是在页面响应加载的顺序中的“加载图片等其他信息”之后。 

用一例子来通俗地解释：比如，页面中只有一个img标签，加载页面时，window.οnlοad=function(){}中的代码要等图片完全显示出来之后才执行（假设其他代码都在图片显示之前加载完了）；而 $(function(){ }) 等``<img/>``标签加载完就执行了，这时候图片可能还没有显示出来。

比如一个图片浏览效果，通常如果图片尺寸很大的情况下，为了防止图片把页面撑开通常会限定图片的宽度和高度，如果是单张图片或者是多张规格比例统一的图片下我们可以直接在``<img>``上加个宽度或者高度属性，或者可以在css样式中加宽度或高度。但如果这些规格比例不统一的图片要浏览呢？那就可能出现问题，你设置的宽高可能造成图片严重失真。解决方式：**可以用min-width，max-width，min-height，max-width解决这些问题** 

#### 区别

- 页面加载完成触发事件时间不同：
  - ready：表示文档结构已经加载完成（不包含图片等非文字媒体文件）
  - onload：表示页面包含图片等文件在内的所有元素的加载完成
- 执行次数不同
  - ready在DOM节点创建完成后就执行，如果有多个定义则依次执行
  - onload在页面所有资源加载完成后执行，如果有多个定义则只执行最后一个

