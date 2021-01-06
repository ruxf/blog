# 踩坑指南：JavaScript解构赋值

## 开篇

自从 2015 年`ES6`发布之后，到现在在项目中使用`ES6`语法应该是基本的操作，比如：`const` `let` `class` `=>` 等等，就在今天我在使用 `ES6` 的解构赋值的时候翻车了...

以下是翻车现场

在项目开发中我经常使用解构赋值来获取 `Object` 的属性或者 `Array`的值

```js
# 要解构属性的对象
const result = { a: 'a', b: 'b' }
# 使用解构来获取 a 和 b
const { a, b } = result
```

```js
# 要解构的数组
const list = [1, 2, 3]
const [item, ...rest] = list
```

然而在项目中解构`null` 属性的对象的时候翻车了

```json
# 后台返回的数据时这样的
{
  username: '',
  identity: 'admin',
  avatar: null,
  token: 'f0a74a994e84e8ed3be11ed9f4bb5417'
}
```

然后在解构数据的时候为 `avatar` 和 `username` 设置了默认值

```js
const { username = '默认用户', identity = 'ADMIN', avatar = 'https://unavatar.now.sh/github/hom' } = result
```

在接下来使用avatar 的时候，发现值为 `null`的 `avatar`没有赋值为默认的属性，导致后面的`vuex`赋值报错。而当我将赋值语句更改为`ES5`的赋值默认值语法的时候运行正常

```js
const avatar = result.avatar || 'https://unavatar.now.sh/github/hom'
```

然后我就去查看 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)关于解构赋值的文档，大概知道了为什么。这里也建议如果开发中对`Javascript`或者`CSS`有疑惑的地方，首选应该去查看`MDN`的文档

## 什么是解构赋值

> **解构赋值**语法是一种 Javascript 表达式。通过**解构赋值,** 可以将属性/值从对象/数组中取出,赋值给其他变量。

在 `JavaScript`开发中我们会频繁使用 `object`和 `array`的值，按照以往使用其值得方式是通过`.`或者`[]`来使用，而在 `ES6`中有新的方式去使用，就是解构赋值。

### 基本使用

常用的使用方式就是以字面量`[]`或者`{}`来获取 `array`或者 `object`的值

#### 数组解构赋值

```js
let a, b, rest;
[a, b] = [10, 20];

console.log(a);
// expected output: 10

console.log(b);
// expected output: 20

[a, b, ...rest] = [10, 20, 30, 40, 50];

console.log(rest);
// expected output: Array [30,40,50]
```

#### 对象解构赋值

```js
({ a, b } = { a: 10, b: 20 });
console.log(a); // 10
console.log(b); // 20

({a, b, ...rest} = {a: 10, b: 20, c: 30, d: 40});
console.log(a); // 10
console.log(b); // 20
console.log(rest); // {c: 30, d: 40}
```



### 常规用法

使用解构赋值经常使用到默认值的赋值，和使用 `function`的默认参数一样，使用 `=` 来添加属性的默认值

```js
let a, b;

[a=5, b=7] = [1];
console.log(a); // 1
console.log(b); // 7
```

```js
let {a = 10, b = 5} = {a: 3};

console.log(a); // 3
console.log(b); // 5
```

## 问题

本篇文章出现的原因就是在使用解构赋值绑定默认值的时候出现了预期之外的结果。我们在解构源对象不存在的属性设置默认值得时候是没有问题的，但是在解构源对象已有属性，属性的值决定了我们设置默认值是否成功

### 现象

```js
const { a = '1', b = '2' } = { a: 'a' }
# output:
# a = 'a'
# b = '2'

const { a = '1', b = '2' } = { a: 'a', b: null }
# output:
# a = 'a'
# b = null

const { a = '1', b = '2', c = '3', d = '4', e = '5' } = let object = { a: 'a', b: null, c: undefined, d: false, e: '' }
# output:
# a = 'a'
# b = null
# c = '3'
# d = false
# d = ''
```

我们可以看到只有值在 `undefined` 的时候默认赋值才能够赋值成功，而当我们使用 `ES5` 的语法来绑定默认值和解构赋值的结果有些不一样

```js
const result = { a: 'a', b: null, c: undefined, d: false, e: '' }

# a
let a = result.a || '1'
# output: a = 'a'

# b
let b = result.b || '2'
# output: b = '2'

# c
let c = result.c || '3'
# output: c = '3'

# d
let d = result.d || '4'
# output: d = '4'

# e
let e = result.e || '5'
# output: e = '5'
```

是不是有一些意外，使用 `ES5` 方式的默认赋值和解构赋值的结果不一样，`ES5` 赋值的属性值为`null` `undefined` `false` `''` 的时候均能够赋值默认值

### 思考

在看到赋值的结果的时候我们需要思考一下为什么会出现这样的结果，3 分钟......

#### 解构赋值

在 `MDN`的文档中我们看到这样一句话

> 为了防止从数组中取出一个值为`undefined`的对象，可以在表达式左边的数组中为任意对象预设默认值。

对象的默认值绑定是在 `array` 的基础中添加的功能。什么时候会出现值为`undefined` 的属性名呢？答案是没有定义这个变量或者变量的值设置为 `undefined`的时候，而`null` `''` `false` 在变量解析的过程中都是有值得情况，所以我们默认赋值会不成功，这儿可以参考一下函数的默认值

```js
function (a, b = '2') {
  // function content
}
```

使用函数的默认值得时候，只要在当前的函数位置有参数，默认值解析式就会跳过默认值赋值，所以不管你是`null`还是`''`，都按照原始格式传入函数中。

所以我们在之后的开发中，如果只是根据属性的存在与否来绑定默认值的时候，可以使用解构赋值

#### 使用`||`赋值

在 `ES5` 中的默认赋值我们会使用要判断的属性名加上`||`再加上默认值来绑定

```js
const a = result.a || '1'
```

也正是由于使用了 `||` 来判断对象值得存在与否，所以只要对象的属性值能够转换为`boolean`的 `false`，默认值绑定就会成功，所以上文中我们使用的`null` `undefined` `false` `''`都可以赋值成功。

如果使用`||`来绑定默认值的时候，同样也需要注意一点，由于`||`是通过将值转换为`true`和`false`来进行短路操作，所以如果对象的值本身就是`false`的时候，如果你的默认赋值是其他类型的值，比如我在实例中使用的`'1'`和`false`或者是默认值为`true`

```js
const result = { a: false }
# 下面是错误的示例
const is = result.a || true
```

在上面的示例中是否发现了什么问题？对，就是`is`的值无论什么时候都是`true`，而忽略了`result`的原始值，这一点是我们需要注意的，可能会导致一些不可复现的 `bug`

### 总结

在上面的讨论之后，我们发现之后再使用默认值绑定，可以通过解构赋值来确定属性值是否存在为`undefined`，随后在程序中使用`||`来判断是否需要对逻辑值为`false`的属性进行默认值绑定

```js
const result = { a: '' }

let { a, b = 'default b' } = result

let a = a || 'default a'
```

使用示例代码所使用的方法我们就可以对我们想要绑定默认值得属性进行正确的操作了。

## 参考

- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)解构赋值的语法

## 更多内容

更多的内容请关注[GitHub](https://github.com/hom)，知乎@[大手印](https://www.zhihu.com/people/meng-jun-23-36)，或者关注我的公众号@全栈开发师，我会不定时分享一些全栈方向的开发内容，谢谢分享

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggms66rig8j31bi0hcgol.jpg)