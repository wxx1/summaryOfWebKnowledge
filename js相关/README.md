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

## 6、实例对象和函数对象

- 实例对象：用new创建的函数称为实例对象
- 函数对象：将函数作为对象使用时，简称为函数对象

```javascript
function Fn(){}
const fn = new Fn() //Fn是构造函数，fn是实例对象
console.log(Fn.prototype)//Fn是函数对象，因为使用 . 方法
Fn.bind({}) //Fn是函数对象，可以使用.bind方法是因为函数对象继承于Function对象，但是fn则不能使用.bind方法 
```

---

## 7、两种类型的回调函数

- 同步函数

  - **立即执行，完全执行完了才结束，不会放入回调队列中**
  - 例子：数组遍历相关的回调函数/ promise的excutor函数

  ```javascript
  const arr = [1,2,3]
  arr.forEach(item => { //遍历回调，同步回调函数,将数组中元素遍历完成之后，才会执行接下来代码
      console.log(item)
  })
  console.log('forEach')
  //1  2  3  forEach
  ```

- 异步函数

  - 不会立即执行，会放入回调队列中将来执行
  - 定时器回调/ajax回调/promise的成功失败回调

---

## 8、JS错误处理

- 错误类型

  - Error：所有错误父类型
  - ReferenceError：引用错误（引用了一个不存在的变量）
  - TypeError：数据类型不正确
  - RangeError：数据值不再其允许的范围内
  - SyntaxError：语法错误

- **错误处理**

  - 捕获错误`try...catch`

  ```javascript
  try(){
      let b 
      console.log(b)
  }catch(error){
      console.log(error.message)
  }
  ```

  - 抛出错误` throw error`

  `throw new Error('自定义错误文本,由调用者处理')，之后使用try...catch`

---

## 9、Promise

- Promise是JS中解决异步编程的解决方案（之前是纯回调函数）
- 从语法上说 是一个构造函数
- 从功能上说Promise对象封装一个异步操作并可以获取其结果
- 三个状态两个结果，每个promise只会改变一次
- Promise传入一个函数，执行异步操作，成功执行resolve();失败执行reject,

```javascript
const p = new Promise((resolve, reject) => {//执行器函数，同步执行
    //执行异步操作任务
    
    //如果成功，调用resolve
    //如果失败，调用reject
})

p.then(res => {
    
}, err => {
    
})
```

**为什么使用Promise**

- 指定回调函数的方式更加灵活：

  旧的：必须在启动异步任务前指定

  promise：启动异步任务=>返回promise对象=>给promise对象绑定回调函数（甚至可以在异步任务中指定回调，定时器中处理p.then，数据不会丢失）

- 支持链式调用（所以可以解决回调地狱）

  - 回调地狱：回调函数嵌套调用，串式执行，外部回调函数异步执行的结果是嵌套的回调函数执行的条件,代码水平向右发展
  - 缺点：不便于阅读，不便于处理异常
  - promise的链式调用解决回调函数，处理异常简单（直接使用一个catch——异常穿透）
  - 终极解决方案：async/await，肉眼中没有回调函数了（虽然底层封装）


**API——语法**

- all：（函数对象方法）传入一个promise数组，只有所有promise都成功才成功，只要有一个失败就直接失败
- race：返回一个新的promise，第一个完成的promise结果状态就是最终结果状态

```javascript
let first = () => (new Promise(resolve, reject) => {
    console.log(3)
    let p = new Promise((resolve, reject) => {
        console.log(7)
        setTimeout(() => {
            console.log(5)
            //状态只能改变一次，此时改变状态不会再输出
            resolve(6)
        },0)
        resolve(1)
    })
    resolve(2)
    p.then((arg) => {
        console.log(arg)
    })
})
first().then((arg) => {
    console.log(arg)
})
console.log(4)

//3 7 4 1 2 5 
```



---

## 10、async和await（异步编程终极解决，无.then）

- async函数

  函数的返回值为promise对象

  promise对象的结果由async函数执行的返回值决定

- await表达式

  await右侧的表达式一般为promise对象，但也可以是其他值

  如果表达式时promise对象，await返回的是promise成功的值

  如果表达式是其他值，直接将此值作为await的返回值

  `await` 用于替代 `then` 使编码更优雅

- 注意

  await必须写在async中，但是async函数中可以没有await

```javascript
async function fn1(){//async返回值是一个promise对象
	return 1
}
const result = fn1()
console.log(result) //打印出Promise对象
```

下例会在await这行暂停执行，知道等待Promise返回结果才继续执行

```javascript
async function hd() {
    console.log(2);
  return new Promise(resolve => {
      console.log(3);
    setTimeout(() => {
        console.log(4);
      resolve("fulfilled");
    }, 2000);
  });
}
async function run() {
    console.log(1);
  let value = await hd();
  console.log("houdunren.com");
  console.log(value);
}
run();
//1
//2
//3
两秒后执行
//4
//houdunren.com
//fulfilled
```














