## 1、call和apply的区别

- call和apply都是Function原型上的方法，每个函数作为Function实例的可以调用该方法
- 目的
  - call和apply都是改变this指向的
- 区别
  - 传参方式不同
    - call参数不固定
    - apply2个固定参数

```javascript
fn.call(obj,10,20,30);
fn.apply(obj,[10,20,30]);
```

可以实现相同功能的方法：bind，但是它不立即执行，而是只改变this，预先处理this

性能上：传参3个以内，性能差不都；传参3个以上，call性能更佳，所以后期开发以call为主

## 2、类数组

**定义**

Javascript类数组对象的定义：

- 可以通过索引访问元素，并且拥有length属性
- 没有数组的其他方法，例如`push,forEach,indexof`等

```javascript
var foo = {
  0: 'name',
  1: 'age',
  2: 'job',
  length: 3
}
```

**转换**

如果类数组对象需要转化为数组，可以使用`Array.prototype.slice.call`

- `Array.prototype.slice.call(foo);` 相当于 `Array.prototype.slice.call(foo, 0);`  是把取一个数组（或者类数组）的子集，并作为一个数组返回。所以当后面的作用对象是一个类数组时，就会把这个类数组对象转换为了一个新的数组。 

**特性**

类数组只有索引值和长度，没有数组的各种方法，所以如果要类数组调用数组方法，就需要使用`Array.prototype.method.call`来实现

```javascript
//如遍历一个类数组，可以这样实现
Array.prototype.forEach.call(foo,function(item){
    console.log(item)
})

//添加一个元素
Array.prototype.push.call(foo, 'PHP');
```

**最常见的类数组就是` argumengs `**

---

## 3、常见异步操作及回调地狱

**常见异步操作**

> 异步编程在执行了某个异步操作时，直接返回，不会再去理会，而我们只需要为其添加一个回调函数，用于当异步操作结束时，再执行这个回调函数即可

- 回调函数

  - 使用回调函数对解耦非常不利，有可能会出现`fn1(fn2(fn3(fn4(...))))`这样的情况。形成回调地狱。

- 事件发布/订阅

  - `fn1,fn2,fn3`都可以视作一个事件的发布者，只要执行它，将会发布一个事件，这个时候，我们可以通过一个事件的订阅者去批量订阅并处理这些事件，包括他们的先后顺序。

  ```javascript
  //增加一个事件的订阅者
  class AsyncFunArr{
      constructor(...arr){
          this.funcArr = [...arr]
      }
      //增加一个处理函数，订阅某个事件，并把事件发生时的处理函数作为观察者添加上去，当事件发生时，发布者会执行观察者这个处理函数。它是回调函数的事件化
      next(){
          let fn = this.funcArr.shift();
          if(typeof fn === 'function'){
              fn()
          }
      }
      //执行
      run(){
          this.next()
      }
  }
  //首先将fn1、fn2、fn3订阅
  const asyncFunArr = new AsyncFunArr(fn1, fn2, fn3)
  //将fn1、fn2、fn3作为发布者分别调用其next()方法
  function fn1(){
      console.log('function 1')
      asyncFunArr.next()
  }
  function fn2(){
      setTimeout(() => {
          console.log('function 2')
          asyncFunArr.next()
      },1000)
  }
  function fn3(){
      console.log('function 3')
      asyncFunArr.next()
  }
  asyncFunArr.run()
  ```

  

- Promise

  - promise是异步编程的一种解决方案，简单的说就是一个容器，里面保存了某个未来才会结束的事件（通常是一个异步操作）的结果
  - 有三种状态，且对象的状态不受外界影响，只有异步操作的结果才能决定当前是哪一种状态，其他操作都无法改变这个状态；一旦状态改变就不会再变
  - 使用Promise的方式就不需要额外编写一个消息订阅者函数，只需要异步函数返回一个Promise即可
  - 使用回调函数有两个最大好处，一把函数嵌套改为链式调用，二使得函数之间解耦
  - new Promise会立即执行，then会分发到微任务

- async和await

  - 放在一个函数前的async有两个作用：
     1.使函数总是返回一个promise
     2.允许在这其中使用await

    promise前面的await关键字能够使JavaScript等待，直到promise处理结束

**settimeout、async、promise 混用的执行顺序**

```javascript
 async function async1() {
     console.log("2");
     await  async2();//执行这一句后，输出async2后，await会让出当前线程，将后面的代码加到任务队列中，然后继续执行调用之处后面代码
     console.log("7");
 }
async function async2() {
    console.log( '3');
}
console.log("1");
setTimeout(function () {
    console.log("8");
},0);
async1();  //执行函数
new Promise(function (resolve) {
    console.log("4");//立即执行
    resolve();
}).then(function () {
    console.log("6");
});
console.log('5');


//1 2 3 4 5 7 6 8

```



---

## 4、事件订阅和发布系统

- 事件订阅与发布系统

  我们定义一个平台，在这个平台上发布这个事件，平台它将会通知订阅了这个事件的人，有事件发生了

  对平台而言：1. 谁要发布这个事件；2. 我对什么样的事情感兴趣

  接口设计：

  1. 发布事件的接口：`dispatch_event(event_name, 用户数据(udata))`
  2. 订阅事件接口：`add_event_listener(event_name,caller,func)`
  3. 取消关注：`remove_event_listener(event_name,func)`

-----

## 5、回调地狱

**什么是回调地狱**

- 函数作为参数层层嵌套
- 连续的回调，接口A需要接口B的res，接口B需要使用接口C的res....当许多功能需要连续调用，环环相扣依赖是，代码将会一层层的嵌套，看起来很庞大，就产生了回调地狱
- 主要是因为功能逻辑代码嵌套的层次太多，导致可读性降低，维护困难。

**解决回调函数的方法**

- `promise,async/await`

---

