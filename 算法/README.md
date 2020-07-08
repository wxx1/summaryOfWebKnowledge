## 前端面试中常见算法

#### 1、两个超大数相加

>在JS中存放整数的时候有一个安全范围，一旦数字超出这个范围便会损失精度，我们不能拿精度损失的数字进行运行，因为运算结果一样会损失精度的
>
>所以，超大数相加使用字符串来表示数据（不会丢失精度）
>
>将字符串分割存在数组中，取较长字符串长度作为基准，较短字符串前位补0

```javascript
function bigNumAdd(num1, num2) {
    const checkNum = num => typeof num === 'string' && !isNaN(Number(num))
    if (checkNum(num1) && checkNum(num2)) {
        //取两个数字的最大长度
        let maxLength = Math.max(num1.length, num2.length);
        //将字符串分割为数组，并反转
        let arr1 = num1.split('').reverse();
        let arr2 = num2.split('').reverse();
        //存进位
        let temp = 0;
        //存最终相加结果
        let result = []
        //以较长的数字为基准进行从前往后逐个相加
        //为避免像个数相加最高位进位后导致结果长度大于两个数字中的长度，for循环长度为最长数字长度+1
        for (let i = 0; i < maxLength + 1; i++) {
            //不足位补0
            let n = arr1[i] || 0;
            let m = arr2[i] || 0;
            //
            let tempAdd = Number(n) + Number(m) + temp;
            //当相加数字大于10的情况下，进行位操作，将要进位的数字赋值给temp，在下一轮使用
            result[i] = tempAdd % 10;
            temp = tempAdd > 9 ? 1 : 0;
        }
        //计算完成，将数组反转回来
        result.reverse()
        //在数组for循环中多加了一位进行处理，如果最高位没有进位则结果第一位为0，可进行截取
        //当结果第一位为1时，则发生了进位，保留该位
        return result[0] > 0 ? result.join('') : result.join('').slice(1)
    } else {
        console.log('big number type error');
    }
}
```

>```js
>//用0去补齐长度，使用API
>a = a.padStart(maxLength , 0);//"0009007199254740991"
>b = b.padStart(maxLength , 0);//"1234567899999999999"
>```

#### 2、跳台阶

```javascript
//一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
function jumpFloor(number){
    if(number <= 0){
        return 0
    }
    if(number === 1){
        return 1;
    }
    if(number === 2){
        return 2;
    }
    let temp = 0,
        a = 1;
        b = 2;
    for(let i = 3; i <= number; i++){
        temp = a + b;
        a = b;
        b = temp;
    }
}

var climbStairs = function(n) {
    const dp = [0, 1, 2];
    for(let i = 3; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n]
};


```



#### 3、前k小

```javascript
//.输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。
function getLeastNumbers(input, k){
    if(!input || input.length < k){
        return []
    }
    return input.sort((a,b) => {
        return a - b;
    }).slice(0, k)
}

console.log(getLeastNumbers([1,6,3,7,2],3))
```

#### 4、二叉树最大深度

```javascript
//采用递归，如果当前节点为空，返回0，如果不为空，返回 1 + 左右子节点的最大深度。
var maxDepth = function(root) {
    if (root === null) {
      return 0;
    } else {
      var leftDepth = maxDepth(root.left),
          rightDepth = maxDepth(root.right);
      var childDepth = leftDepth > rightDepth ? leftDepth : rightDepth;
      return 1 + childDepth;
    }
}

var maxDepth = function(root, deep = 0) {
    if(!root) return deep;
    return Math.max(maxDepth(root.left) + 1, maxDepth(root.right) + 1);
};
  
let tree = {
    val :3,
    left:{
        val: 9,
        left: null,
        right: null
    },
    right: {
        val: 20,

        left: null,
        right: null
    }
}
```

#### 5、二叉树每条路径

```javascript
var binaryTreePaths = function (root) { 
    if (root === null) return []; 
    if (!root.left && !root.right ) { 
        return [root.val.toString()]; 
    } 
    var left = binaryTreePaths(root.left), 
        right = binaryTreePaths(root.right);
    //输出每个路径
    return left.concat(right).map(x => root.val + "->" + x)
    //求每条路径的和
    // return left.concat(right).map(x => root.val + Number(x));
 };

 let root = {
     val: 1,
     left:{
         val:2,
         right:{
             val:5
         },
         left:null
     },
     right: {
         val:3
     }
 }

 console.log(binaryTreePaths(root))
```

#### 6、模拟实现loadash中的_.get()函数

```javascript
const obj = {  
    selector: { to: { toutiao: 'FE coder' } },  
    target: [1, 2, { name: 'byted' }]  
}; 
// 运行代码 
get(obj, 'selector.to.toutiao', 'target[0]', 'target[2].name') 
//  输出结果：// ['FE coder', 1, 'byted'] 
```

```javascript
function get(object){
    const path = Array.prototype.slice.call(arguments, 1);
    let result = path.map(item => {
        return (item.replace(/\[/g, '.').replace(/\]/g, '').split('.')).reduce((o,k)=>((o || {})[k]), object) // || 'error' //可添加默认值
    })
    return result
}
```

#### 7、二叉树中是否存在给定值的路径

- 如果是叶子节点，则若等于sum则返回true，否则返回false
- 如果是非叶子节点，则递归左右树，看是否存在和为sum-val的路径
- 有两种递归终止条件：一种是为叶子节点；另一种是为null。

```javascript
/**
* function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
*/
function hasPathSum(root, sum){
    if(root === null){
        return false;
    }
    if(root.left === null && root.right === null){
        return root.val == sum
    }
    
    return hasPathSum(root.left, sum-root.val) || hasPathSum(root.left,sum-root.val)
}
```

#### 8、实现map、reduce

```javascript
/**
 * map方法
 * 对每一项执行的函数
 *      该函数接收三个参数：数组项的值,数组项的下标,数组对象本身
 * 指定 this 的作用域对象
 * map 方法返回每次函数调用结果组成的数组。
 * arr.map(function(item, index, arr) {}, this);
 */
Array.prototype.myMap = function myMap(fn, context) {
    if (typeof fn !== 'function') {
        throw new TypeError(`${fn} is not a function`);
    }
    let arr = this;
    let temp = [];
    for (let i = 0; i < arr.length; i++) {
        let result = fn.call(context, arr[i], i, arr);
        temp.push(result);
    }
    return temp;
}
```

```javascript
/**
 * reduce是并归方法，接收两个参数
 * 第一个参数是在每一项上调用的函数
 *      该函数接收 4 个参数：前一个值 prev，当前值 cur，项的索引 index，数组对象 array
 * 第二个可选参数是作为归并基础的初始值
 * reduce 方法返回一个最终的值。
 * 
 * arr.reduce(function(prev, cur, index, arr){}, initialValue)
 * 
 * 归并
        与之前的迭代不同，归并不是对每一项都执行目标函数，而是可以概括为如下：
            不断地对数组的前两项“取出”，对其执行目标函数，计算得到的返回值
            把上述返回值“填回”数组首部，作为新的 array[0]
            持续循环执行这个过程，直到数组中每一项都访问了一次
            返回最终结果
 */
Array.prototype.myReduce = function myReduce(fn, base) {
    if (typeof fn !== 'function') {
        throw new TypeError(`${fn} is not a function`);
    }

    let initArr = this;
    let arr = initArr.concat() //得到副本
    if (base) arr.unshift(base)
    let index, newValue;
    while (arr.length > 1) {
        index = initArr.length - arr.length + 1;
        newValue = fn.call(null, arr[0], arr[1], index, initArr);
        // arr.splice(0, 2);//删除前两项，影响原数组
        // arr.unshift(newValue);//把 fn(arr[0],arr[1]) 的结果从数组首部推入
        arr.splice(0, 2, newValue); // 直接用 splice 实现替换
    }
    // index += 1;
    // let result = fn.call(null, arr[0], arr[1], index, initArr);
    return newValue;
}
```

#### 9、求一个数组中出现次数大于等于n的元素

```javascript
//先统计数组中每个元素出现的个数，用数组保存
function findIndex(arr, n){
    let obj = {}
    let temp = []
    arr.map(item => {
        if(obj[item]){
            obj[item] ++
        }else{
            obj[item] = 1
        }
    })

    for(let key in obj){
        if(obj[key] == n){
            temp.push(key)
        }
    }
    return temp
}

```

#### 10、输入一个字符串，统计字符串中每个字母出现的次数，按照字母表顺序输出

```javascript
/**
 * 输入一个字符串，统计字符串中每个字母出现的次数，按照字母表顺序输出
 * 利用桶排序，创建一个26位数组并初始化为0
 */
function find(str){
    const arr = new Array(26).fill(0);//创建一个数组并且填充为0
    const temp = []
    for(let i = 0; i < str.length; i++){
        let index = str.charCodeAt(i)-97
        arr[index] ++
    }
    for(let i = 0; i < arr.length; i++){
        if(arr[i] !== 0){
            let obj = {}
            obj.name = String.fromCharCode(i+97)
            obj.size = arr[i]
            temp.push(obj)
        }
    }
    return temp
}

let str = 'aqwertaaa'
console.log(find(str))
```

#### 11、二叉树反转

```javascript
/**
 * 二叉树翻转
 * 操作给定二叉树，将其变换为源二叉树的镜像
 */
function reverseTree(root){
    //为空则结束
    if(root == null || (root.left == null && root.right == null)){
        return;
    }
    //交换左右子树
    swap(root)

    //左右子树反转自己去处理就行了
    reverseTree(root.left)
    reverseTree(root.right)

}

function swap(node){
    var temp = null;
    temp = node.left;
    node.left = node.right;
    node.right = temp
}


let tree = {
    val: 8,
    left: {
        val: 6,
        left:{ val:5 },
        right:{ val: 7 }
    },
    right:{
        val:10,
        left:{ val: 9 },
        right: { val: 11 }
    }
}
reverseTree(tree)
console.log(tree)
```

#### 12、判断是否为循环链表

- 只要普安段有没有Null指针，如果没有指向null的指针，头节点又重复穿线，那必定是循环列表（只适合头尾相连的循环单链表）

  ```javascript
  function isCircularLis(list){
      let currentNode = list.head;
      while(currentNode.next !== null){
          currentNode = currentNode.next;
          if(currentNode.value === "head"){
              return true
          }
      }
      return false
  }
  ```

  

- 快慢指针（类似6小循环列表）

  - 定义两个指向头节点的指针fast、slow。他们的步长不一样，然后让他们同时从头节点开始遍历链表。如果链表是循环的，也就是有环的，那么快慢指针总有再相遇的时候，就像操场跑步，操场是个环，跑的块的同学总会再遇上跑的慢的同学

```javascript
function isCircularList2(list){
    let fast = slow = list.head //快慢指针均指向链表头节点
    //没有达到链表尾部，则继续前进，考虑fast.next.next不存在情况
    while(slow.next !== null && fast.next !== null && fast.next.next !== null){
        slow = slow.next;  //
        fast = fast.next.next;
        if(slow === fast ){ //若两个指针相遇，则均不为NULL则存在环
            console.log('循环')
            return true
        }
    }
    console.log('非循环')
    return false
}
```

