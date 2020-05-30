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

