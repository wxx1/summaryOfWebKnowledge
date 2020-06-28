## BFC

- 定义

  -  在解释BFC之前，先    说一下文档流。我们常说的文档流其实分为定位流、浮动流和普通流三种。而普通流其实就是指BFC中的FC。FC是formatting context的首字母缩写，直译过来是格式化上下文，它是页面中的一块渲染区域，有一套渲染规则，决定了其子元素如何布局，以及和其他元素之间的关系和作用。常见的FC有BFC、IFC，还有GFC和FFC。BFC是block formatting context，也就是块级格式化上下文，是用于布局块级盒子的一块渲染区域 。

  **BFC：块级格式化上下文，指一个独立的渲染区域或者说是一个隔离的独立容器**

  - 触发条件

  1. 根元素，即HTML元素。
  2. 浮动元素，float的值不为none(可为 left,right)。
  3. overflow的值不为visible（为hidden，scroll，auto）。
  4. display为以下其中之一的值inline-block，table-cell，table-caption。
  5. position的值为absolute或者fixed。

  - 特性
    - 内部的盒子会在垂直方向上一个接一个的放置
    - 垂直方向上的距离由margin决定；（解决外边距重叠问题）
    - BFC的区域不会和float元素区域重叠（防止浮动文字环绕）
    - 计算BFC高度时，浮动元素也参与计算（清除浮动）
    - BFC就是页面上的一个独立容器，容器里面的子元素不会影响外面元素

- 作用

  1. 可以阻止元素被浮动元素覆盖

  2. 可以包含浮动元素

     通过改变高度塌陷的父盒子的属性值，让其成为BFC，以此来包含浮动盒子

  3.  属于同一个BFC的两个相邻块级子元素的上下margin会发生重叠，(设置writing-mode:tb-rl时，水平margin会发生重叠)。所以当两个相邻块级子元素分属于不同的BFC时可以阻止margin重叠 

