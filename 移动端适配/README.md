### 移动端适配

由于移动端的特殊性，屏幕的尺寸碎片化严重，要想很好的适配不同的尺寸的设备，需要我们前端开发相比PC端要做一些基层的适配方案（消除横向滚动条）

#### 两种像素

像素是计算机屏幕中显示特定颜色的最小区域，当设备尺寸相同时，像素越密集，画面就越精细

那么，当我们在CSS中为一个元素设置属性`width:250px`时，这个像素的宽度究竟是多少像素呢？

**物理像素（设备像素）**

指的是设备屏幕的物理像素，任何设备的物理像素都是固定的

**CSS像素**

是CSS和JS中使用的一个抽象概念。它和物理像素之间的比例却决于屏幕的特性以及用户进行缩放，由浏览器自行换算。

 在 Apple 的视网膜屏（Retina）中，每 4 个像素为一组，渲染出普通屏幕中一个像素显示区域内的图像，从而实现更为精细的显示效果。此时， 250px 的元素跨越了 500 个物理像素的宽度。 

#### 视口

视口一般移动端关注较为多，有苹果公司提出，主要做到布局视口和视觉视口分离(屏幕可视区)

**布局视口**

一般移动设备的浏览器的默认设置了一个viewport元标签，定义一个虚拟的布局视口 ，默认的布局视口宽度为 980px。如果要显式设置布局视口，可以使用 HTML 中的 meta 标签 ， 布局视口使视口与移动端浏览器屏幕宽度完全独立开。CSS 布局将会根据它来进行计算，并被它约束。 

**视觉视口**

 视觉视口是用户当前看到的区域，用户可以通过缩放操作视觉视口，同时不会影响布局视口 

![](https://user-gold-cdn.xitu.io/2018/10/5/16643994a5a962cf?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 视觉视口和缩放比例的关系为：  当前缩放值 = 理想视口宽度  / 视觉视口宽度 

所以，当用户放大时，视觉视口将会变小，CSS 像素将跨越更多的物理像素。 

**理想视口**

它对设备而言是最理想的布局视口尺寸。显示在理想视口中的网站具有最理想的宽度，用户无需进行缩放。 

**布局视口与理想视口的宽度一致 , 是响应式布局的基础 **

- viewport

```html
<!-- 设置当前的布局视口的宽度为320像素-->
<meta name="viewport" content="width=320">

<!-- 设置当前的布局视口的宽度为视觉视口的宽度，根据设备自动修改值-->
<meta name="viewport" content="width=device-width">

<!-- 通过设置缩放比例，让浏览器自动设置布局视口的宽度-->
<meta name="viewport" content="initial-scale=1.0">

<!-- 通过设置缩放0.5倍  当宽度为600px时，设置缩放0.5比例时，则实际在屏幕上显示300px，如在视口为370的移动端上，可以显示完全宽度为600px的盒子，因为设置缩放0.5倍-->
<meta name="viewport" content="initial-scale=0.5">
```

**当width和initial-scale同时设置时，取其数值大者**

如果将布局视口的宽度=device-width(设备宽度，也就是:物理像素/dpr)时，此时页面100%的宽度正好能在视口中完全显示，不需要缩放查看页面了，而且在不同尺寸下能基本表现一直，此时的布局窗口的状态我们就成为**理想视口**

所谓理想视口是：

- 不需要用户缩放和横向滚动就能正常的查看网站的所有内容
- 显示的文字大小是合适的。比如一段14px大小的文字，不会因为在一个高密度像素屏幕里显示得太小而无法看清，理想的情况是这段14px文字无论是在何种密度屏幕，任何分辨率下，显示出来的大小都是差不多的。其他元素也是这个道理

##### viewport的其他设置

- maximum-scale

  在移动端，你可能会考虑用户浏览不便，然后给予用户放大页面的权力，但同时又希望是在一定范围内放大，这就使用maximum-scale来约束

- minimum-scale：用于指定用户能够缩小的比例

- user-scalable来约束用户是否可以通过手势对页面进行缩放，默认值为yes，即可被缩放，当你的应用不打算让用户拥有缩放权限时，可以将该值设置为no



#### 常见的适配方案

##### 1、百分比+固定高度布局方案

- 固定屏幕为理想视口宽度viewpoint
- 少许的媒体查询设置字体
- 父容器宽度的百分比做布局，水平百分比布局
- 水平方向部分也可以使用弹性布局

弹性盒子+固定布局

高度不变，宽度虽然又变化，但是左右两个盒子都基本上靠两个，可以用福鼎实现，也可以用弹性盒子(space-betwwen)

---

##### 2、rem布局方式+媒体查询

- rem的大小取值：根据页面的dpr动态改变

rem是css3中新增加的单位，rem表示根元素字体的大小

比如，如下需求：随着设备尺寸的变宽，元素高度和宽度都响应放大

所有单位都用rem时，在响应式布局中，只要改变根元素的字体大小，所有尺寸的字体大小会随之改变，一般配合媒体查询使用

```css
/*媒体查询以@media开头，然后后面可以跟上判断的条件。比如：screen表示屏幕设备，and是并且的意思*/
@media screen and (min-width: 320px){
    html {font-size: 50px;}
}
@media screen and (min-width: 360px){
    html {font-size: 56.25px;}
}
@media screen and (min-width: 400px){
    html {font-size: 62.5px;}
}
@media screen and (min-width: 440px){
    html {font-size: 68.75px;}
}
@media screen and (min-width: 480px){
    html {font-size: 75px;}
}
@media screen and (min-width: 640px){
    html {font-size: 100px;}
}
```

以上代码表示：根据CSS的媒体查询设备的屏幕的宽度，根据宽度的大小设置html根元素的字体大小，由于rem根据不同的屏幕尺寸设置了不同的大小值，所以根据rem布局的元素会根据根元素不同值进行适配

缺点：媒体查询不能完全枚举，毕竟android屏幕尺寸碎片化严重，各种尺寸的又，不能完全覆盖，只能答题覆盖，如果想要精确覆盖要么通过js实现，要么使用最新的浏览器已经支持的calc实现

**js实现动态改变根元素的字体大小**

通过js动态获取屏幕宽度（`document.clientWidth`）然后根据屏幕宽度动态计算出rem的实际值。

> 假设，640px的设备 1rem = 100px
>
> 公式：rem =  document.documentElement.clientWidth /640*100px;

```javascript
!(function(doc, win){
    var docEle = doc.documentElement,
        //orientationchange事件在设备的纵横方向改变时触发。
        //文档视图调整大小时会触发 resize 事件。
        evt = 'onorientationchage' in window ? 'orientationchange' : 'resize',
        fn = function(){
            var width = docEle.clientWidth;
            width && (docEle.style.fontSize = 100 * (width/640) + 'px');
        };
    win.addEventListener(evt, fn, false);
    //当初始的 HTML 文档被完全加载和解析完成之后，DOMContentLoaded 事件被触发，而无需等待样式表、图像和子框架的完全加载。注意：DOMContentLoaded 事件必须等待其所属script之前的样式表加载解析完成才会触发。
    doc.addEventListener('DOMContentLoaded', fn, false);
}(document, window))
```

**使用calc**

calc() 用一个表达式作为它的参数，用这个表达式的结果作为值 

```CSS
/* calc(100vw):得到当前屏幕的宽度*/
html{
    font-size:calc(100*(100vw/640))
}
```

----

##### 3、固定设计稿的宽度开发+根据设备动态适配缩放

- 开发直接俄按照设计稿编写固定尺寸元素
- 页面加载完成后用js动态根据dpr改变页面的缩放值
- 解决方案：淘宝的flexible方案/[hoss]([http://imochen.github.io/hotcss/) 

简单点说就是，开发是时候根据设计稿完全还原像素，然后 根据屏幕的宽度通过js动态改变页面的所发，恰好是理想视口的大小。

**原理核心就是修改页面的mate标签的缩放**

这样开发人员直接像素还原，开发效率高