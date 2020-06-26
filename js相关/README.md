#### 1、call和apply的区别

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

#### 2、类数组

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

