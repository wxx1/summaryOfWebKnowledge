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

#### 二叉树最大路径和

```javascript
const maxPathSum = (root) => {
  let maxSum = Number.MIN_SAFE_INTEGER // 纪录保持者
  const dfs = (root) => {
    if (root == null) return 0 // 递归的出口
    const left = Math.max(0, dfs(root.left)) // 
    const right = Math.max(0, dfs(root.right))// 
    maxSum = Math.max(maxSum, left + root.val + right) //当前子树的maxSum挑战最大值
    return root.val + Math.max(left, right) // 向父节点提供的最大和，要包括自己
  }
  dfs(root) // 递归的入口
  return maxSum
}
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

#### 13、最大连续子数组

>给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的连续子数组，并返回其长度。如果不存在符合条件的连续子数组，返回 0。
>
> ** 示例：*
>
> ** 输入：s = 7, nums = [2,3,1,2,4,3]*
>
> ** 输出：2*
>
> ** 解释：子数组 [4,3] 是该条件下的长度最小的连续子数组。*
>
>滑动窗口法：O(n)
>
> 处理子数组，子字符串，最先想到的肯定是滑动窗口 

```javascript
const minSubArrayLen = function (s, nums) {
    let arr = [];//维护一个数组
    const len = nums.length;
    let min = len + 1; //最小连续子数组
    let sum = 0
    for (let i = 0; i < len; i++) {
        arr.push(nums[i]);
        sum += nums[i];
        while (sum >= s) {
            sum -= arr[0]
            min = Math.min(arr.length, min)
            arr.shift()
        }
    }
    return min == len + 1 ? 0 : min;
}

let s = 7
let arr = [2,3,1,2,4,3]
console.log(minSubArrayLen(s,arr))


//暴力法，两层循环找到符合条件：和 >= s 的长度，维护最小长度  O(n2)
function find(s, nums){
    const len = nums.length;
    let min = len+1;
    for(let i = 0; i < len; i++){
        let sum = 0;
        for(let j = i; j < len; j++){
            sum += nums[j]
            if(sum >= s){
                min = Math.min(min, j-i+1)
            }
        }
    }
    return min == len + 1 ? 0 : min;
}
console.log(find(s,arr))
```

#### 14、中序遍历

```javascript
//递归
let result = [];
let dfs = function(node){
    if(node){
        dfs(node.left);
        result.push(node.value);
        dfs(node.right)
    }
}
```

```javascript
//非递归
function dfs(node){
    let result = [];
    let stack = [];
    while(stack.length || node){
        if(node){
            stack.push(node)
            node = node.left;
        }else{
            node = stack.pop();
            result.push(node.value)
            node = node.right /如果没有右子树，会再次想栈内取一个节点，及双亲节点
        }
    }
    return result;
}
```

#### 15、前序遍历

```javascript
//递归
let result = []
function dfs(node){
    if(node){
        result.push(node.val);
        dfs(node.left)
        dfs(node.right)
    }
}

//非递归
function dfs(tree){
    let result = []
    let stack = []
    stack.push(tree)
    while(stack.length){
        let node = stack.pop()
        result.push(node.value);
        node.right && stack.push(node.right);
        node.left && stack.push(node.left)
    }
}
```

#### 16、复原IP地址

>给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。
>
>*有效的 IP 地址正好由四个整数（每个整数位于 0 到 255 之间组成），整数之间用 ‘.’ 分隔。*
>
>*示例:*
>
>*输入: “25525511135”*
>
>*输出: [“255.255.11.135”, “255.255.111.35”]*
>
>
>
>*这道题思路主要是考察回溯和剪枝，逐步构造一棵树， 树的每层都对应ip地址的每一段，最好能到达叶子节点的路径就是符合的结果*

```javascript
var restoreIpAddresses = function(s){
    if(s.length < 4 || s.length >12) return []

    const result = []

    for(let i = 1; i < 4; i++){
        let str = s.substr(0,i);
        if(parseInt(str) > 255 || (str.length > 1 && str[0] === '0')) continue;
        defaultStatus([str], i, 1);
    }

    function dfs(arr, begin, depth){
        if(begin >= s.length){
            if(arr.length === 4){
                result.push(arr.join('.'));
            }
            return;
        }
        let leftLen = s.length - begin;
        if (leftLen > (4 - depth) * 3) return;    //当剩下的字符串长度已经超过最大长度，直接返回
        for (let i=1;i<4 && i<=leftLen; i++) {
            let str = s.substr(begin, i);
            if (parseInt(str) > 255 || str.length > 1 && str[0] === '0') continue;   //若当前字符段不满足规则，则跳过
            let temp = arr.slice();
            temp.push(str);
            d(temp, begin + i);
        }
    }
    return result;
}
```

#### 17、零钱兑换

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

```
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1
```

> 动态规划：尝试分解子问题 
>
> 假设给出的不同面额的硬币是[1, 2, 5]，目标是 120，问最少需要的硬币个数？ 
>
> dp[i]: 表示总金额为 i 的时候最优解法的硬币数 
>
>我们想一下：求总金额 120 有几种方法？下面这个思路关键了 !!!
>  一共有 3 种方式，因为我们有 3 种不同面值的硬币。
>  1.拿一枚面值为 1 的硬币 + 总金额为 119 的最优解法的硬币数量
>    这里我们只需要假设总金额为 119 的最优解法的硬币数有人已经帮我们算好了，
>    不需要纠结于此。(虽然一会也是我们自己算，哈哈)
>    即：dp[119] + 1
>  2.拿一枚面值为 2 的硬币 + 总金额为 118 的最优解法的硬币数
>    这里我们只需要假设总金额为 118 的最优解法的硬币数有人已经帮我们算好了
>    即：dp[118] + 1
>  3.拿一枚面值为 5 的硬币 + 总金额为 115 的最优解法的硬币数
>    这里我们只需要假设总金额为 115 的最优解法的硬币数有人已经帮我们算好了
>    即：dp[115] + 1
>    
>  - 所以，总金额为 120 的最优解法就是上面这三种解法中最优的一种，也就是硬币数最少
>    的一种，我们下面试着用代码来表示一下：
>    
>  - dp[120] = Math.min(dp[119] + 1, dp[118] + 1, dp[115] + 1);
>    
>  - 推导出「状态转移方程」：
>    dp[i] = Math.min(dp[i - coin] + 1, dp[i - coin] + 1, ...)
>    其中 coin 有多少种可能，我们就需要比较多少次，那么我们到底需要比较多少次呢？
>    当然是 coins 数组中有几种不同面值的硬币，就是多少次了~ 遍历 coins 数组，
>    分别去对比即可
>    
>  - 上面方程中的 dp[119]，dp[118]，dp[115] 我们继续用这种思想去分解，
>    这就是动态规划了，把这种思想，思考问题的方式理解了，这一类型的题目
>    问题都不会太大。
>

```javascript
var coinChange = function(coins, amount) {
  let dp = new Array( amount + 1 ).fill( Infinity );//无穷大
  dp[0] = 0;
  
  for (let i = 1; i <= amount; i++) {
    for (let coin of coins) {
      if (i - coin >= 0) {
        dp[i] = Math.min(dp[i], dp[i - coin] + 1);
      }
    }
  }
  
  return dp[amount] === Infinity ? -1 : dp[amount];
}

```

#### 18、合并两个有序数组

1、将两个数组合并后排序

2、暴力比较

3、 o(m+n)

![](..\img\merge1.png)

- nums1 、 nums2 有序，若把 nums2 全部合并到 nums1 ，则合并后的 nums1 长度为 m+n
- 我们可以从下标 m+n-1 的位置填充 nums1 ，比较 nums1[len1] 与 nums2[len2] 的大小，将最大值写入 nums1[len]，即
  nums1[len1]>=nums2[len2] ，nums1[len--] = nums1[len1--] ,这里 -- 是因为写入成功后，下标自动建议，继续往前比较
  否则 nums1[len--] = nums2[len2--]
- 边界条件：
  若 len1 < 0 ，即 len2 >= 0 ，此时 nums1 已重写入， nums2 还未合并完，仅仅需要将 nums2 的剩余元素（0…len）写入 nums2 即可，写入后，合并完成
  若 len2 < 0，此时 nums2 已全部合并到 nums1 ，合并完成

```javascript

var merge = function(nums1, m, nums2, n) {
    let length = m + n
    while(n > 0) {
        if(m <= 0) {
            nums1[--length] = nums2[--n]
            continue
        }
        nums1[--length] = nums1[m-1] >= nums2[n-1] ? nums1[--m]: nums2[--n]
    }
};

```

#### 19、买卖股票最佳时机

给定一个数组，它的第  i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。

示例 1:

输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
示例 2:

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

>循环版
>暴力循环，i 为卖出的天数，j 为买入的天数。
>
>i 从 1 开始，因为第 0 天不可能卖出。
>
>j 从 0 开始到 i 结束，因为买入的天数一定小于卖出的天数。
>
>然后在这两者间的差价中找最大值。
>

```JavaScript
let maxProfit = function (prices) {
  let max = 0;
  for (let i = 1; i < prices.length; i++) {
    for (let j = 0; j < i; j++) {
      let price = prices[j];
      let sale = prices[i] - price;
      max = Math.max(max, sale);
    }
  }
  return max;
};

```

>**动态规划版**
>
>状态转移方程: 当天的最大收益 = max(  当天卖出：当天的价格 - 过去几天最低的价格,  当天不卖：过去几天的最大收益 ) 

```JavaScript
/**
 * DP版
 */
let maxProfit = function (prices) {
  let n = prices.length;
  if (!n || n === 1) return 0;

  // 最大收益
  let prevMax = 0;
  // 最小价格
  let prevMin = prices[0];

  for (let i = 1; i < n; i++) { //循环迭代
    let price = prices[i];

    prevMax = Math.max(price - prevMin, prevMax);
    prevMin = Math.min(price, prevMin);
  }

  return prevMax;
};
```

#### 20  最长不含重复字符的子字符串 

> 滑动窗口法
>
> 解题思路： 使用一个数组来维护滑动窗口
>
> 遍历字符串，判断字符是否在滑动窗口数组里
>
> - 不在则 push 进数组
>
> - 在则删除滑动窗口数组里相同字符及相同字符前的字符，然后将当前字符 push 进数组
>
> - 然后将 max 更新为当前最长子串的长度
>
>   遍历完，返回 max 即可

```javascript
var lengthOfLongestSubstring = function(s) {
    let arr = [], max = 0
    for(let i = 0; i < s.length; i++) {
        let index = arr.indexOf(s[i])
        if(index !== -1) {
            arr.splice(0, index+1);
        }
        arr.push(s.charAt(i))
        max = Math.max(arr.length, max) 
    }
    return max
};

```

#### 21 判断是否回文字符串 

 给定一个非空字符串 `s`，**最多**删除一个字符。判断是否能成为回文字符串。 

>### 解题思路一
>
>1. 回文字符串，字符串`正序反序都一样`。同样也是`对称`的。
>2. 正反指针，一个从`头`，一个从`末尾`，对比。
>3. 找到不同的位置，去掉该位置的值。（可能为`i`，也可能为`length-1-i`）
>4. 若两种情况中`有一种`是回文。那就返回`true`。否则返回`false`
>5. 找不到不同的值当然也返回`true`

```javascript
var validPalindrome = function(s) {
    let sArr = s.split('');
    for (let i = 0; i <= (sArr.length >> 1); i++) {
        if (sArr[i] !== sArr[sArr.length - i - 1]) {
            let f = [...sArr];
            f.splice(i,1);
            let f2 = [...sArr]; 
            f2.splice(sArr.length - i - 1,1);
            if ((f+'') == ([...f].reverse()+'') || (f2 + '') == ([...f2].reverse() + '')) {
                return true
            } else {
                return false
            }
        }
    }
    return true
};
```

#### 22 找出第K大元素

```javascript
/**
 * n 为数组的长度
数组中的第 1 大元素，即，从小到大排序后，下标为 n - 1 的元素
数组中的第 K 大元素，即，从小到大排序后，下标为 n - k 的元素
我们希望位置 n - k 的左边是比它小的，右边是比它大的，那么 nums[n - k] 就是第 k 大的元素
我们把 n-k 看作 pivot ，用快速排序的手法去 partition “分区”
 */

const findKthLargest = (nums, k) => {
    const n = nums.length;
  
    const quick = (l, r) => {
      if (l > r) return;
      let random = Math.floor(Math.random() * (r - l + 1)) + l; // 随机选取一个index
      swap(nums, random, r); // 将它和位置r的元素交换，让 nums[r] 作为 pivot 元素
      /**
       * 我们选定一个 pivot 元素，根据它进行 partition
       * partition 找出一个位置：它左边的元素都比pivot小，右边的元素都比pivot大
       * 左边和右边的元素的是未排序的，但 pivotIndex 是确定下来的
      */
      let pivotIndex = partition(nums, l, r);
      /**
       * 我们希望这个 pivotIndex 正好是 n-k
       * 如果 n - k 小于 pivotIndex，则在 pivotIndex 的左边继续找
       * 如果 n - k 大于 pivotIndex，则在 pivotIndex 的右边继续找
       */
      if (n - k < pivotIndex) { 
        quick(l, pivotIndex - 1);
      } else {
        quick(pivotIndex + 1, r);
      }
      /**
       * n - k == pivotIndex ，此时 nums 数组被 n-k 分成两部分
       * 左边元素比 nums[n-k] 小，右边比 nums[n-k] 大，因此 nums[n-k] 就是第K大的元素
       */
    };
  
    quick(0, n - 1); // 让n-k位置的左边都比 nums[n-k] 小，右边都比 nums[n-k] 大
    return nums[n - k]; 
  };
  
  function partition(nums, left, right) {
    let pivot = nums[right];             // 最右边的元素作为 pivot 元素
    let pivotIndex = left;               // pivotIndex 初始为 left
    for (let i = left; i < right; i++) { // 逐个考察元素，和 pivot 比较
      if (nums[i] < pivot) {             // 如果当前元素比 pivot 小
        swap(nums, i, pivotIndex);       // 将它交换到 pivotIndex 的位置
        pivotIndex++;                    
      }
    }                                    // 循环结束时，pivotIndex左边都是比pivot小的
    swap(nums, right, pivotIndex);       // pivotIndex和right交换，更新pivot元素
    return pivotIndex;                   // 返回 pivotIndex 下标
  }
  
  function swap(nums, p, q) {
    const temp = nums[p];
    nums[p] = nums[q];
    nums[q] = temp;
  }
```

#### 23 promise

```javascript
//三种状态，两种结果

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
        // const promise2 = new Promise((resolve, reject) => {
        //     if(this.state === 'success'){
        //         //进入下一个事件循环
        //         setTimeout(() => {
        //             const x = resolve(this.value)//返回值可能为promise|| 123 || '1233'
        //             resolvePromise(promise2, x, resolve, reject)
        //         })
                
        //     }
        // })
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
    // resolve(100)
    setTimeout(() => {
        reject(10000)
    },1000)
     
})
p.then(res => console.log(res),reject => console.log(reject))


```

#### 24 深克隆

```javascript
const clone = parent => {
  // 维护两个储存循环引用的数组
  const parents = [];
  const children = [];

  const _clone = parent => {
    if (parent === null) return null;
    if (typeof parent !== 'object') return parent;

    let child, proto;

    if (isType(parent, 'Array')) {
      // 对数组做特殊处理
      child = [];
    } else if (isType(parent, 'RegExp')) {
      // 对正则对象做特殊处理
      child = new RegExp(parent.source, getRegExp(parent));
      if (parent.lastIndex) child.lastIndex = parent.lastIndex;
    } else if (isType(parent, 'Date')) {
      // 对Date对象做特殊处理
      child = new Date(parent.getTime());
    } else {
      // 处理对象原型
      proto = Object.getPrototypeOf(parent);
      // 利用Object.create切断原型链
      child = Object.create(proto);
    }

    // 处理循环引用
    const index = parents.indexOf(parent);

    if (index != -1) {
      // 如果父数组存在本对象,说明之前已经被引用过,直接返回此对象
      return children[index];
    }
    parents.push(parent);
    children.push(child);

    for (let i in parent) {
      // 递归
      child[i] = _clone(parent[i]);
    }

    return child;
  };
  return _clone(parent);
};

/*****/
function deepClone (obj) {
        var newobj = Array.isArray(obj) ? [] : {};
        if(obj && typeof obj !== 'object'){
           for(let key in obj){
               if(obj.hasOwnProperty(key)){
                   newObj[key] = (obj && typeof obj[key] === 'object') ? deepClone(obj[key]) : obj[key];
               }
           }
        }  
        return newobj
}
```

#### 25 防抖节流

```javascript
//当频繁触发一个事件时，一个频繁触发的事件，只在规定时间内，让最后一次生效，前面的不生效
function debounce(fun,delay){
    //记录上一次定时器
    var timer = null; //第一次设置为空
    //使用闭包防止每次调用时将timer初始化为null
    return function(){
        //清除上一次延时器
        clearTimeout(timer);
        //重新设置定时器
        timer = setTimeout(function(){
            fun.call(this,...arguments); //修正函数this指向
        },delay);
    }
}

/**
 * 节流
 * 当一个函数需要频繁被调用时，会消耗大量资源，使用节流的方式会减少资源的消耗
 * 即在规定时间内只让函数触发的第一次生效
 * @param {*} fn 
 * @param {*} delay 
 */
function throttle(fn,delay){
    //记录上一次触发时间
    var lastTime = 0;
    //使用闭包能防止lastTime的值每次调用时都初始化为0
    return function(){
        var nowTime = new Date().getTime();
        if(nowTime - lastTime > delay){
            fn.apply(this, arguments);
            //同步时间
            lastTime = nowTime;
        }
    }
}
```

