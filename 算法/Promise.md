## Promise

- 异步编程的一种解决方案，可解决回调地狱
- 三种状态两种结果

**初级版**

```javascript
class Promise{
    //定义promise，传入一个方法进去，且理解执行
    constructor(fun){
        //初始值为pending
        this.state = 'pending';
        //成功的值
        this.value = undefined;
        //失败的值
        this.reason = undefined;
        //因为promise只能从一种状态改变成另一种状态，且结果不可逆
        let resolve = (value) =>{
            if(this.state === 'pending'){
                this.state = 'success';
                this.value = value;
            }
        }
        let reject = (reason) =>{
            if(this.state === 'pending'){
                this.state = 'reject';
                this.reason = reason;
            }
        }
        //传入两个方法，供回调
        fun(resolve, reject)
    }
    //then方法，传入一个正常回调和异常回调
    then(resolve, reject){
        if(this.state === 'success'){
            resolve(this.value)
        }else if(this.state === 'reject'){
            reject(this.reason)
        }
    }
}

let p = new Promise((resolve, reject) => {
    // resolve(100)
    reject(10000)
})
p.then(res => console.log(res),reject => console.log(reject))
```

以上构造看似可以实现promise功能，但是只能执行同步方法，一旦使用定时器等异步方法，则不会输出任何结果

**改善版——支持异步**

发布订阅者模式

```javascript

class Promise{
    //定义promise，传入一个方法进去，且理解执行
    constructor(fun){
        //初始值为pending
        this.state = 'pending';
        //成功的值
        this.value = undefined;
        //失败的值
        this.reason = undefined;
        //注册成功的回调，主要为了在未来（异步中）使用
        this.resolveCallbacks = [];
        //注册失败回调
        this.rejectCallbacks = [];
        //因为promise只能从一种状态改变成另一种状态，且结果不可逆
        let resolve = (value) =>{
            if(this.state === 'pending'){
                this.state = 'success';
                this.value = value;
                //在未来调用resolve才将该回调取出一一执行
                this.rejectCallbacks.forEach(fn => fn())
            }
        }
        let reject = (reason) =>{
            if(this.state === 'pending'){
                this.state = 'reject';
                this.reason = reason;
                this.rejectCallbacks.forEach(fn => fn())
            }
        }
        //传入两个方法，供回调
        fun(resolve, reject)
    }
    //then方法，传入一个正常回调和异常回调
    then(resolve, reject){
       if(this.state === 'success'){
           resolve(this.value)
       }
        if(this.state === 'reject'){
            reject(this.reason)
        }
        //当状态还未改变时，记录回调，当触发执行时，可以执行记录下来的方法
        if(this.state === 'pending'){
            //当状态未改变时先记录成功时使用的回调，等到未来状态改变时直接使用（执行其中方法）
            this.resolveCallbacks.push(() => {
                resolve(this.value)
            })
            this.rejectCallbacks.push(() => {
                reject(this.reason)
            })
        }
    }
}

let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject(10000)
    },1000)
     
})
p.then(res => console.log(res),reject => console.log(reject))

```



  