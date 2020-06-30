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

