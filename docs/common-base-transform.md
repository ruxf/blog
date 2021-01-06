# JavaScript常用进制数转换

## 常见进制

在我们的生活中我们所接触到的数字都是基于十进制的数字，这也和我们双手有十个指头有关，这是人类社会中的数字的表示方式，比如 `0-9` 。然而在计算机中，只有两个数字 `0` 和 `1` ，这通常可以用来表示电压的高低或者说是电平的高低、电路的通断，所以计算机中的所有数字和信息的存储都是基于二进制的，比如 `0` `1`。

通常我们在计算机编程中所接触到的数字也是 `10` 进制的，但是当我们从网络接口或者文件 `IO` 中获取到数据进行编码时，接触的数据就是基于二进制的，为了方便我们通常将二进制的数据转化为十六进制来展示，比如 `0xABCDEF`。

因此本文介绍的就是常用的二进制、八进制、十进制、十六进制之间的相互转换

## 进制转换

在 `JavaScript` 中进制的转换主要使用 `Number.parseInt()` 和 `Number.prototype.toString()` 方法来实现，在官方文档中接受的参数为要转换的进制数

- `Number.parseInt()` 方法依据指定基数 [ 参数 **radix** 的值]，把字符串 [ 参数 **string** 的值] 解析成整数。

  >```js
  >Number.parseInt(string[, radix])
  >```

- `Number.prototype.toString()`方法返回指定 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 对象的字符串表示形式。

  > ```js
  > Number.prototype.toString([radix])
  > ```

### 二进制

```js
const binary = '0101010110';
```



#### 二进制转八进制

```js
const octal = Number.parseInt(binary, 2).toString(8);
console.log(octal)
// 526
```



#### 二进制转十进制

```js
const decimal = Number.parseInt(binary, 2);
console.log(decimal);
// 342
```



#### 二进制转十六进制

```js
const hexadecimal = Number.parseInt(binary, 2).toString(16);
console.log(hexadecimal);
// 156
```



### 八进制

```js
const octal = '177';
```

#### 八进制转二进制

```js
const binary = Number.parseInt(octal, 8).toString(2);
console.log(binary);
// 1111111
```



#### 八进制转十进制

```js
const decimal = Number.parseInt(octal, 8);
console.log(decimal)
// 127
```



#### 八进制转十六进制

```js
const hexadecimal = Number.parseInt(octal, 8).toString(16);
console.log(hexadecimal);
// 7f
```



### 十进制

```js
const decimal = 255;
```

#### 十进制转二进制

```js
const binary = Number.parseInt(decimal, 10).toString(2);
console.log(binary);
// 11111111
```



#### 十进制转八进制

```js
const octal = Number.parseInt(decimal, 10).toString(8);
console.log(octal);
// 377
```



#### 十进制转 16 进制

```js
const hexadecimal = Number.parseInt(decimal, 10).toString(16);
console.log(hexadecimal);
// ff
```



### 十六进制

```js
const hexadecimal = '0xfff';
```

#### 十六进制转二进制

```js
const binary = Number.parseInt(hexadecimal, 16).toString(2);
console.log(binary);
// 111111111111
```



#### 十六进制转八进制

```js
const octal = Number.parseInt(hexadecimal, 16).toString(8);
console.log(octal);
// 777
```



#### 十六进制转十进制

```js
const decimal = Number.parseInt(hexadecimal, 16);
console.log(decimal);
// 4095
```

### 其他进制

其他的进制，我们可以参照 `Number.parseInt()` 和 `Number.prototype.toString()` 的参数中的 `redix` 为相应要转换的进制数即可

源代码已经发布到 [GitHub](https://github.com/hom/common-base-transform) ，可以下载和执行:-

## 更多内容

更多的内容请关注[GitHub](https://github.com/hom)，知乎@[大手印](https://www.zhihu.com/people/mrmengj)，或者关注我的公众号@全栈开发师，我会不定时分享一些全栈方向的开发内容，谢谢分享

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggms66rig8j31bi0hcgol.jpg)

## 参考

[`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)

[`Number.parseInt()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

[`Number.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toString)