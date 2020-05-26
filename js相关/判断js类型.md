## 判断JS数据类型的四种方法

> 基本数据类型：String、Number 、Boolean、Symbol、Undefined、Null
>
> 引用类型：Object、Function、Array

基本类型也称简单类型，由于其占据空间固定，是简短的数据段，为了便于提升变量查询速度，将其值存储在栈内

引用类型也称复杂类型，其值存储在堆内，而存储在变量处的值是一个指针，指向存储对象的内存中，即按址访问

以下介绍四种常用的方法判断js数据类型

#### 1、typeof

typeof 是一种操作符，其右侧跟一个一元表达式，并返回这个表达式的数据类型。返回的结果用该类型的全小写字母字符串表示，包括7种：number、string、boolean、symbol、undefiend、object、function

**不能检测出Array类型**

特点：

- 对于基本类型，除 null 以外，均可以返回正确的结果 
- 对于引用类型，除function以外，一律返回object类型
- 对于null，返回object类型（null有自己类型Null），对于function，返回function类型

**引用类型中数组、日期、正则都有属于自己的具体类型，而typeof对于这些类型的处理，只返回了原型链最顶端的Object类型**



#### 2、instanceof

instanceof是用来判断A是否为B的实例的，表达式： A instanceof B，如果A是B的实例，则返回true,否则返回false。**instanceof检测的是原型**

```javascript
[] instanceof Array;//true
[] instanceof Object;//true
new Date() instanceof Date; //true

function Person(){};
new Person() instanceof Person;
```

由以上我们能判断出[]是Array的实例，但也认为[] 是Object的实例。

**instanceof运算符用来判断一个构造函数的prototype属性所指向的对象是否存在另一个要检测对象的原型链上**所以instanceof只能判断两个对象是否属于实例关系，而不能判断一个对象具体属于哪中类型

- 缺陷
  - 当网页中包含多个框架，实际上就存在多种不同的全局环境，从而存在不同版本的Array构造函数，如果从一个框架传入另一个框架一个数组，此时创建的两个数组就具有不同的构造函数，此时用instanceof判断数组可能会出错；**针对数组这个问题，ES5提供了Array.isArray()方法。该方法用以确认某对象本身是否为Array类型，而不区分该对象在哪个环境中创建**



#### 3、constructor（一个高效但危险的变量类型判断）

constructor判断变量类型，工作非常高效完美，但是constructor判断变量类型有一个致命的缺陷，就是当检测null或者undefined类型的constructor属性时，js会报错！！

所以在利用变量的constructor属性来判断变量类型时，必须要先保证变量不会有null或者undefined

```javascript
function cstor(variable) {
    if (variable === null || variable === undefined) {
        return 'Null or Undefined';
    }

    var cst = variable.constructor;

    switch (cst) {
        case Number:
            return 'Number'
        case String:
            return 'String'
        case Boolean:
            return 'Boolean'
        case Array:
            return 'Array'
        case Object:
            return 'Object'
    }
}
```

> 1、null 和 undefined 是无效的对象，因此是不会有 constructor 存在的，这两种类型的数据需要通过其他方式来判断。
>
> 2、函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object



#### 4、toString（正确判断变量姿势）

toString是Object的原型方法，调用该方法，返回格式为[object xxx]，其中xxx就是对象的类型

对于Object对象，直接调用toString，就能返回[object Object]。而对于其他对象，则需要通过call/apply来调用才能返回正确的类型信息

```javascript
Object.prototype.toString.call('') ;   // [object String]
Object.prototype.toString.call(1) ;    // [object Number]
Object.prototype.toString.call(true) ; // [object Boolean]
Object.prototype.toString.call(Symbol()); //[object Symbol]
Object.prototype.toString.call(undefined) ; // [object Undefined]
Object.prototype.toString.call(null) ; // [object Null]
Object.prototype.toString.call(newFunction()) ; // [object Function]
Object.prototype.toString.call(newDate()) ; // [object Date]
Object.prototype.toString.call([]) ; // [object Array]
Object.prototype.toString.call(newRegExp()) ; // [object RegExp]
Object.prototype.toString.call(newError()) ; // [object Error]
Object.prototype.toString.call(document) ; // [object HTMLDocument]
Object.prototype.toString.call(window) ; //[object global] window 是全局对象 global 的引用
```







