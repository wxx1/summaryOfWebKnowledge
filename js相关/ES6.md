## ES6

#### 1、var，let和const

- var在JS引擎预解析阶段会发生变量提升，let和const没有变量提升
- 全局声明的var会挂载在window上，而let和const不会
- let，const的作用范围是块级作用域，而var的作用域范围是函数作用域
- 同一作用域下let和const不能重复定义，，而var可以重复定义
- 同一作用域下在let和const声明前会存在暂时性死区



#### 2、箭头函数

- 箭头函数没有arguments
- 箭头函数没有prototype属性，不能作用于构造函数（不能用new关键字调用）
- 箭头函数没有自己的this,它的this是词法的，引用的是上下文的this。



#### 3、函数的参数默认值

```javascript
// ES6；
function printText(text = 'default') {
    console.log(text);
}
```



#### 4、Promise(常用)



#### 5、字符串模板

```javascript
// bad
const foo = 'this is a' + example;

// good
const foo = `this is a ${example}`;
```



#### 6、对象

##### 6.1 对象属性/方法简写

- ES6允许当对象的属性和值相同时，省略属性名；省略的是属性名而不是值，值必须是一个变量
- ES6允许当一个对象的属性的值是一个函数，可以使用简写形式

```javascript
let x = 0;
let obj = {
    x,
    fun(){},//方法简写
    fun1:() => {}//箭头函数
}
```



#### 5、模板字符串

