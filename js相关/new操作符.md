## new操作符

在使用构造函数实例化时，必须使用new操作符，此时调用构造函数实际上会经历以下四个步骤

1. 创建一个新空对象
2. 将构造函数的的作用域赋值给新对象（因此this就指向了这个新对象） 这个对象可以访问到构造函数原型上的属性 
3. 执行构造函数中的代码（为这个新对象添加属性）
4. 返回新对象

```javascript
function create(Con, ...args){
    let obj = {}
    Object.setPrototypeOf(obj, Con.prototype)//等同于obj.__proto__ = Con.protorype
    let result = Con.apply(obj,args)
    return obj
}
```

