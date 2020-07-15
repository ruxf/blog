# 技巧篇：不一样的 Markdown 文档

在开发过程中，我们需要书写 API 文档；在记录自己的想法和灵感的时候，我们也需要一个好用的方式去书写。在以前我们会使用富文本编辑或者`Office`或者`WPS`来书写文档，现在我们更多的选择是去使用`Markdown`格式去书写，简单明了，格式简单，更重要的是常用的笔记应用和开源软件开发文档都是使用的`Markdown`。

常用的 `Markdown`语法可以参考[本篇内容](https://github.com/hom/markdown)

在本文中我想要分享的是在 `Markdown`中通过使用 `CSS`实现的一些不一样样式

## 居中展示

通常我们通过添加`# 标题` 来实现标题的突出显示，而有的时候我们想要**居中**显示该怎么办呢？

由于 `Markdown`语法就是基于 `HTML`标签来构建的页面样式，所以我们想当然的可以在 Markdown 文档中使用 `HTML` 和 `CSS` 样式

所以我们可以通过添加下面的代码来实现居中内容展示

```html
<p align="center">
  居中展示
</p>
```

展示的效果如下

<p align="center">居中展示</p>

## 添加折叠

在文档中，如果我们有一段内容简介和内容，我们可能想实现一个折叠框展示，通过使用 `HTML` 的`details` 和 `summary`能够实现这个效果。

通过在`caniuse`的[网站](https://caniuse.com/#search=details)中看到在大部分的浏览器中能够支持`details` 和 `summary` 标签都能够使用。

![caniuse](https://tva1.sinaimg.cn/large/007S8ZIlly1ggqvqgtaarj31ro0u07gj.jpg)

下面是具体的使用方法，我们把想要折叠的内容使用 `details` 包裹，把标题的内容放在`summary`标签中即可

```html
<detail>
  <summary>Details</summary>
  Something small enough to escape casual notice.
</detail>
```

效果如下:

**折叠**状态

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggqvvw2m0wj30ji0doweq.jpg)

**展开**状态

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggqw0vpe4oj30je0di74u.jpg)

**代码**样式

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggqvz0zi7tj31bc0940te.jpg)

使用[GitHub](https://github.com/hom/markdown)查看效果更佳，具体的`detail`文档我们可以查看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/details)

## 更多内容

更多的内容请关注[GitHub](https://github.com/hom)，知乎@[大手印](https://www.zhihu.com/people/meng-jun-23-36)，或者关注我的公众号@全栈开发师，我会不定时分享一些全栈方向的开发内容，谢谢分享

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggms66rig8j31bi0hcgol.jpg)