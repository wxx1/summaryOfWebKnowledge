> 事件是JavaScript与HTML交互的基础。要实现用户与页面的交互，先要对目标元素绑定特定的事件、设置事件处理函数、然后用户触发事件，事件处理函数执行，产生交互效果。

## DOM事件级别

DOM级别有DOM0、DOM1、DOM2、DOM3,其为DOM API的不同标准

- DOM0级事件

  ```html
  <button id="btn" type="button"></button>
  
  <script>
  	var btn = document.getElementBy('btn')
      btn.onclick = function(){
          console.log('Hello World')
      }
  </script>
  
  ```

	缺点：无法设置给同一元素设置多个事件处理函数, 这些方法都是在当前元素事件行为的冒泡阶段(或者目标阶段)执行的。 
	
- DOM2级事件

  ```html
<button id="btn" type="button"></button>
  
  <script>
  	var btn = document.getElementById('btn')
      btn.addEventListener('click', showFn, false)
      btn.addEventListener('click', showFn2, false)
      // btn.removeEventListener('click', showFn, false) // 解绑事件 
       function showFn() {
          alert('Hello World');
      }
       function showFn2() {
          alert('Hello World2');
      } 
  </script>
  ```
  
   可以为事件设置多个事件处理函数，可以通过第三个参数 ( useCapture ) 设置在什么阶段执行事件处理函数，默认是 false， 即在事件冒泡阶段执行事件处理函数。   IE8 及以下版本需要用 `attachEvent` 和 `detachEvent` 实现 
  
- DOM3级事件
  
  DOM3级事件是在DOM2级事件的基础上添加了更多的事件类型，允许自定义事件。
  
  ```javascript
  //自定义事件
  var event = new Event('test')
  //给元素绑定事件
  domElement.addEventListener('test', function(){
      console.log("evvent test")
  })
  //触发事件  通过手动的使用dispatchEvent()方法派发的事件。
  setTimeout(function() {
      domElement.dispatchEvent(event)
  }, 1000)
  ```
  
  增加的事件类型：UI事件、焦点事件、鼠标事件、滚轮事件、键盘事件、文本事件(textInput)
  
  
  
  
  
  

