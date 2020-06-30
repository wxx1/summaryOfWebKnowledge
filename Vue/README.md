### Vue考察点

---

#### 1、路由相关钩子函数

**全局**

- `beforeEach`
- `afterEach`

**路由独享守卫**：直接在路由配置中定义

- `beforeEnter`

**组件内守卫**

- `beforeRouteEnter`
- `beforeRouteUpdate`
- `beforRouteLeave`

---

#### 2、虚拟DOM

​		DOM是文档对象模型的简写，在浏览器中通过js来操作DOM的性能很差，于是虚拟DOM应运而生。**虚拟DOM就是在js中模拟DOM对象树来优化DOM操作的一种技术或者思路。**虚拟DOM并不是真正意义上的DOM，他作为一个轻量级的javascript对象，在状态发生变化时，会进行Diff运算，来更新发生变化的DOM，对于未发生变化的DOM节点，不与操作，由于不是全部重绘，大大提高更新渲染性能。当使用render函数描述虚拟DOM时，vue提供一个函数，这个函数就时构建虚拟DOM所需的工具。光网上给他起个名字叫`createElement`。

**virtual DOM和真实DOM的区别**

virtual DOM是将真实DOM的数据抽取出来，以对象的形式模拟树形结构。

```html
<div>
    <p>
        123
    </p>
</div>
```

对应Vnode

```javascript
var Vnode = {
    tag:'div',
    children: [
        {tag:'p',text:'123'}
    ]
}
```



---

#### 3、Diff算法

要渲染真实DOM的开销很大，比如我们修改了某个数据，如果直接渲染到真实DOM上会引起整个DOM树的重排重绘，有没有可能我们只更新我们修改的那一小块DOM而不要更新整个dom呢？此时diff算法可以帮助我们

我们现根据真实DOM生成一颗虚拟DOM，当虚拟DOM某个节点的数据发生变化后就会生成一个新的Vnode,然后将Vnode和oldVnode作对比，发现有不一样的地方就直接修改在真实DOM上，然后使oldVnode的值为Vnode。diff的过程就是调用名为patch的函数，比较新旧节点，一边比较一边给真实DOM打补丁

**diff比较方式**

diff算法比较就节点时，只会在同级比较（时间复杂读O(n)）

1. 当数据发生改变时，set方法会让Dep.notify通知所有订阅者Watcher，订阅者就会调用patch给真实DOM打补丁，更新相应视图

2. 在调用patch函数时，他接受两个参数，oldVnode，vnode,

   1. 首先同层判断两个节点是否值得比较，是否相似无变化。若值得比较则执行`patchVnode`。
   2. 如果两个节点不一样，则说明Vnode完全被改变，就可以直接替换oldVnode

   （如果两个节点一样，则深入检查他们的子节点。如果同级节点不一样，则直接替换oldVnode。如果第一层不一样直接替换，而不直接深入比较第二层，对于相同子节点就不能重复利用，这算不算一个缺点）

3. 如果两个节点值得比较之后会对两个节点指定`patchVnode`方法。

   1. 如果他们带有文本节点并且不相等，则将真实Dom的文本节点设置为Vnode的文本节点
   2. 如果oldVnode没有子节点而vnode有，则将Vnode的字节带你真实化之后添加到el
   3. 如果oldVnode有子节点而Vnode没有，则删除真实DOMD的子节点
   4. 如果两者都有子节点，则执行`updateChildren`函数比较子节点
      1. `updateChildren`会先将Vnode和oldVnode的子节点提取出来，为新旧虚拟DOM分别提供头尾双指针进行比较
      2. 这些节点`sameVnode`之后就紧着着执行`patchVnode`，就这样层层递归下去，直到将oldVnode和Vnode中所有子节点对比完，也就将DOM的所有补丁都打好了

![](E:\Work\知识点总结\img\updateChildren.png)



以上是diff算法的全部过程，以下是一张总结图

![](E:\Work\知识点总结\img\diff.png)

----

#### 4、响应式数据原理

- 响应式就是指当数据发生改变时，Vue会通知到使用该数据的代码。例如，视图渲染中使用了数据，数据改变后，视图也会自动更新

- 核心`Object.defineProperty+观察者模式`
- 默认vue在初始化数据时，都会给data中属性使用`Object.defineProperty`重新定义所有属性，这样做的好处是当对data中属性修改访问时都会被`get,set`劫持，定义自己的逻辑，称为依赖收集，并各自添加watch，在调用时会触发，如果属性发生变化就会通知相应依赖数据

- 数据时被观察的一方，发生改变时，通知所有观察者，这样观察者可以做出相应。
- 把依赖数据的观察者称为watcher    data->watcher  

![](..\img\watcher.png)

---

#### 5、`v-if`和`v-show`区别

- `v-if`

  `v-if`是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建

  `v-if`也是惰性的，如果在初始渲染时条件为假，则什么也不做——直到条件第一次为真时，才会开始渲染条件块

- `v-show`

  `v-show`就简单很多——不管初始条件是什么，元素总是会被渲染，并且只是简单地进行CSS切换

- 总结
  - v-if和v-show都是用来控制元素是否渲染的，但`v-if`是真正的条件渲染，`v-show`是通过设置DOM元素的display样式属性控制显隐；
  - v-if判断是否加载，可以减轻服务器压力，在需要时加载，但有更高是切换开销。
  - v-show调整DOM元素的CSS的display属性，可以使客户端操作更加流畅，但有更高的初始渲染开销。如果需要非常频繁地切换，则使用v-show较好；
  - 如果在运行时条件很少改变，则使用v-if较好