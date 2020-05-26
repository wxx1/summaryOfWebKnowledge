### HTML文件开头的 Doctype有什么作用

> 每个HTML文件理开头的有一个很重要的东西``<!DOCTYPE html>``,可能你在开始写html页面的时候并不注意这句话，但是它非常重要

- 什么是DOCTYPE

  DOCTYPE是document type的简写，它不是什么HTML标签，也没有结束标签，它是一种标记语言的文档理性声明，即告诉浏览器当前HTML是用什么版本编写的。**它的目的是要告诉标准通用标记语言解析器，它应该使用什么样的文档类型定义（DTD）来解析文档

  ``<!DOCTYPE>``声明必须在html文档的第一行，位于标签之前

- 什么是DTD

  - 文档类型定义（DTD）可定义合法的XML文档构建模块。它使用一系列合法的元素来定义文档的结构。DTD可被成行声明与XML文档中，也可做为一个外部引用(想javaz中定义servlet映射时就要外部引用DTD等)

  - **为什么使用DTD**
-  通过 DTD，独立的团体可一致地使用某个标准的 DTD 来交换数据。 
    
-  可以使用 DTD 来验证您自身的数据 
    
-  通过 DTD，您的每一个 XML 文件均可携带一个有关其自身格式的描述
  
html4部分DTD内容：
  
  ```xml
  <!ENTITY % events
   "onclick     %Script;       #IMPLIED  -- a pointer button was clicked --
    ondblclick  %Script;       #IMPLIED  -- a pointer button was double clicked--
    onmousedown %Script;       #IMPLIED  -- a pointer button was pressed down --
    onmouseup   %Script;       #IMPLIED  -- a pointer button was released --
    onmouseover %Script;       #IMPLIED  -- a pointer was moved onto --
    onmousemove %Script;       #IMPLIED  -- a pointer was moved within --
    onmouseout  %Script;       #IMPLIED  -- a pointer was moved away --
    onkeypress  %Script;       #IMPLIED  -- a key was pressed and released --
    onkeydown   %Script;       #IMPLIED  -- a key was pressed down --
    onkeyup     %Script;       #IMPLIED  -- a key was released --"
    >
  还定义一些html4标签之类
  ```
  
  - DOCTYPE的作用
  
    - 在所有 HTML 文档中规定 DOCTYPE 是非常重要的，这样浏览器就能了解预期的文档类型， 告诉浏览器要通过哪一种规范（DTD）解析文档（比如HTML或XHTML规范）。
       DOCTYPE会影响代码验证，并决定了浏览器最终如何显示你的Web文档。
  
    - **声明文档解析类型，避免浏览器的怪异模式**
  
      - 怪异模式：浏览器使用自己的怪异模式解析渲染页面
      - 标准模式：浏览器使用W3C的标准解析渲染页面
  
      