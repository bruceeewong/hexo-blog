---
title: 谈谈CSS的渐进增强设计
date: 2021-10-04 09:58:04
categories: [coding, frontend]
tags: [css]
---

CSS 层叠样式表作为前端三剑客之一，通过各类选择器来解耦 HTML 结构与表现，让开发者拥有专注控制样式的能力，实现了关注点分离。通过层叠机制，为规则赋予不同的重要程度，让我们的样式代码能够灵活地继承与覆盖。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/css.jpeg)

它就像精灵宝可梦里的百变怪 👾，拥有强大而奇妙的变化与适应能力，前端技术也因 CSS 的加入而变得漂亮与有趣 🤩

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319164117814.png)

本文主要分享 CSS 技术中的一个切面概念：渐进增强，希望能够从原理+实例出发，给大家在设计、编写网页样式时带来一点启发 💡

# 渐进增强

随着 HTML 与 CSS 的发展，许多新特性陆续得到浏览器厂商们的支持，比如 HTML5 中的新增标签，CSS 中的 Flexbox，Grid、calc 等，给网页带来了很多新鲜而强大的能力。
但是由于时代推移、浏览器的厂商各自对规范实现的不一致，导致不同的浏览器以及版本存在新特性不兼容或者其他 Bug 问题，让开发者在新特性与兼容性之间放弃了尝试新特性的想法 🤪

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319164211039.png)

但是！时代与技术一定是在进步的，而在新老交替的过渡期，我们可以采用渐进增强的策略，来平衡向后兼容性与最新的 HTML/CSS 特性。

## 什么是渐进增强 🧐

> ⛳️ 所谓渐进增强，即我们首先为最小公分母准备可用的内容，然后再为支持新特性的浏览器添加更多交互优化。

要实现渐进增强，意味着代码要分层，每一层增强代码都只会在相应特性被支持或被认为适当的情况下使用。听起来有点复杂，然而实际上 HTML 与 CSS 已经部分内置了这一策略。

## HTML 的渐进增强策略

> ⛳️ 对于 HTML 而言，浏览器遇到未知元素或属性时并不会报错，也不会对页面产生什么影响。

假设表单中有输入邮件的控件:

```html
<input type="text" name="field-email" />
```

我们就可以使用 HTML5 新增的 email 类型，可以拥有邮件格式检验以及移动设备中的特定格式软键盘的强化能力：

```html
<input type="email" name="field-email" />
```

尚未实现这个新类型的浏览器就会想：”这是甚么玩意儿？不明白“🧐，然后给他退化成 type="text"类型，就当无事发生。
这样，我们既渐进增强了页面，也不会对浏览器产生什么不好的影响。
类似的还有 HTML5 的文档声明<!DOCTYPE html>，这样的语法也是向后兼容的。

## CSS 的渐进增强策略

> ⛳️ 对于 CSS 来说，无法识别的属性/值都会被浏览器丢弃，所以只要提供合理的后备声明，使用新属性就不会带来不良后果。

例如，现代浏览器支持的颜色值 rgba 函数，我们可以这样定义红色：

```css
.overlay {
  background: #000;
  background-color: rgba(0, 0, 0, 0.8);
}
```

对于旧的浏览器，它会丢 rgba 的声明，应用第一条规则；对于现代浏览器，第二条规则就会覆盖第一条，显示带透明度的红色。
那么即使不是所有浏览器都支持 rgba 函数，由于提供了合理后备，我们仍然可以大胆地使用它 😎

## 如何实现渐进增强 🧐

### 1. 厂商前缀

🛠 浏览器厂商也基于同样的原理为自家浏览器引入实验特性，并加上一串特殊前缀，这样其自家浏览器就能识别而其他浏览器忽略。
例如 transform 属性：

```css
.thing {
  -webkit-transform: translate(0, 10px);
  -moz-transform: translate(0, 10px);
  -ms-transform: translate(0, 10px);
  transform: translate(0, 10px);
}
```

下面列举前缀对应的浏览器厂商：

-webkit-: 适用于 Webkit 内核的浏览器，包括 Blink 引擎的(基于 Webkit)

- Safari
- Chrome
- Opera

-moz-: 基于 Mozilla 浏览器

- Firefox

-ms-： 微软家

- Internet Explorer

### 2. 条件规则与检测脚本

`@supports`块
⚙️ 如果希望通过检测浏览器是否支持某个 CSS 特性来提供样式，可通过条件规则检测：

```css
@supports (display: grid) {
  /* 编写网格布局的规则 */
}
```

### 3. JavaScript 库

⚙️ 通过 Modernizr 这个 JS 库可以检测各种特性的支持情况。
原理是 modernizr 会根据检测情况给 html 根元素添加相应特性的类名，如 flexbox，然后在编写 CSS 时带上前缀，即可安全的增强样式。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319130957809.png)

更多关于新特性的浏览器支持情况，可以到 http://caniuse.com 查阅 🔎

## 🌰 例子 1：在浮动之上应用 Flex

这里有一个使用 float 布局实现的卡片布局，我们来看看如何在不破坏原有代码的情况下，用 Flexbox 特性增强这个页面。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319140242022.png)

这个页面的使用了 float 来实现网格布局效果，主要原理是

- 💡 使用 col 类名定义浮动组件，row 类名定义清除浮动的父组件
- 💡 加上 row-quartet 定义子项列宽，实现网格布局效果
- 核心代码如下：

```html
<div class="row row-quartet">
  <div class="col"><article></article></div>
  <div class="col"><article></article></div>
  <div class="col"><article></article></div>
  <div class="col"><article></article></div>
</div>
```

```css
// index.css/* 行组件 */
.row:after {
  content: "";
  display: block;
  clear: both;
  height: 0;
}
.row-quartet > * {
  width: 25%;
} /* 列组件 */
.col {
  float: left;
}
```

通过图片可以看到上述代码并未实现列等高效果，然而要实现浮动元素的列等高效果比较麻烦，详情方案参考：[https://juejin.cn/post/6844904048278290440#heading-22](https://juejin.cn/post/6844904048278290440#heading-22)

而使用 Flexbox 可以轻松实现，原理是 Flex 的等高机制：父容器定义为 Flexbox 容器之后，控制辅轴的属性 align-items 默认为 strech，即自动拉伸子项的高度以填满空间。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319143719986.png)

那么此时我们想通过 Flexbox 来实现列等高效果，又不破坏已实现的部分，该怎么做呢 🧐
**⛳️ 答案是：引入检测机制，在检测块内编写新特性的规则。**

上面也提到有两种方式：

1. 使用 @supports 块检测，但由于此检测语法本身就比较新，所以对于旧浏览器不友好。

```css
@supports (display: flex) {
  /* 编写 flexbox 布局的规则 */
}
```

2. 使用 Modernizr 库检测，然后使用带.flexbox 前缀的类名来编写增强的规则。

首先我们将 Modernizr 的 script 写在所有 link 的前面（保证检测脚本先加载）。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319142146671.png)

添加后，Modernizr 检测完毕后，我们的<html>标签上就将支持的特性名字生成 classname，测试浏览器是当前最新的 Chrome 89, 所以可以看到 flexbox 赫然在列。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319142415220.png)

接下来就基于此类名前缀，编写增强代码：

```css
// index.css​/_ flexbox 增强代码 _/
.flexbox .row {
  display: flex;
}
.flexbox .col {
  display: flex;
  flex-direction: column;
}
.flexbox .col > _ {
  flex: 1;
}
​/_ float 实现相关代码 _/.row:after {
  content: "";
  display: block;
  clear: both;
  height: 0;
}
.row-quartet > _ {
  width: 25%;
}
.col {
  float: left;
}
```

我们来看下效果，这里用到浏览器测试工具：browserstack ，选择在 Chrome 下打开页面，展示了等高效果，说明应用增强代码：

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319145639197.png)

而在 IE 10 浏览器上就降级为无 flexbox 的效果：

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319145712720.png)

查阅 CanIUse 网站，看到 IE10 只支持带 -ms- 前缀的 flexbox 属性，所以也解释了上述表现。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319150211608.png)

总结一下 在浮动之上应用 Flex 方案，有几个关键点：

- 不理解 flex 关键字的浏览器会忽略它。
- 给标签定义了 display: flex 之后，浏览器会忽略该标签定义的 float，clear 与 display:inline-block 等声明。

举一反三，其他场景我们也可以遵循首先写一个适合任何场景的布局，然后再通过 新特性检测 + 运用 Flexbox / Grid 等新特性来渐进增强我们的网页。

> 详情代码：CodePen:
>
> - [Float 布局版本](https://codepen.io/brrruski/pen/JjbQvGjCodePen)
> - [Flexbox 增强版本](https://codepen.io/brrruski/pen/KKNjRyb)

## 🌰 例子 2：响应式图片

> 响应式 Web 设计出现以后，何时加载合适的图片是前端开发者要面对的问题。很多开发者不管设备、屏幕大小，一律使用相同的图片，这种做法导致小屏幕看大图浪费带宽与内存、大屏幕展示小图看不清楚等问题。而浏览器会对网页进行预处理，图片等资源会在浏览器构建完页面或运行 JavaScript 之前就开始下载，这意味着不可能仅凭脚本就完美解决图片响应式的问题。所以 HTML5 提出了响应式图片的解决方案。

响应式图片(Responsive Image)是指给 HTML5 规范给<img>标签添加的 srcset、sizes 新属性，旨在解决不同的条件下告诉浏览器加载不同的图片。

- `srcset`: 哪个是当前图片的可替换源文件，其宽度是多少像素？
- `sizes`: 在各个断点中，图片的 CSS 宽度是多少？

### Hi，img 标签的新属性

来看个例子 🌰：

```html
<img
  src="http://dummyimage.com/300x150"
  srcset="
    http://dummyimage.com/300x150   300w,
    http://dummyimage.com/600x300   600w,
    http://dummyimage.com/1200x600 1200w
  "
  sizes="(max-width: 600px) 300px, (max-width: 1200px) 600px, 1200px"
/>
```

分析一下：

- srcset 的值是一组图片 URL+实际像素宽度（不是 CSS 像素），定义了一组资源后，还要告诉浏览器怎么使用这些图片。
- sizes 通过开头可选的媒体查询条件 + 图片展示宽度值(CSS 单位，可为 px, em, vw)。

如果某条媒体查询条件为真，浏览器将取得条件后面定义的宽度，然后去匹配到最接近尺寸的图片，执行下载。最后，图片将按照定义的宽度进行展示。

在 Chrome 上运行的效果:

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319170223252.png)
![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319170313075.png)
![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319170348656.png)

需要注意的点：

- 在有图片缓存的情况下，浏览器可能会加载较大的图片。
- 在网速慢或者电量低的情况下，浏览器会加载较小的图片。
- 浏览器知道当前设备是不是高分辨率屏幕，从而决定是否自动加载大图。

通过 srcset，我们渐进增强了原有的图片组件，让不同宽度的屏幕加载更合适尺寸的图片。

### 进一步加强：picture 标签

> MDN 介绍：picture 元素除了在多个不同分辨率的图片间切换，还有几个很重要的响应式图片的应用场景：

响应式图片在大小屏幕分别需要不同的裁切方式，根据浏览器的支持加载不同格式的图片。如 Google 的 Webp 格式会比 JPG 体积小 25%-34%，可以优化网页的性能。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319161122021.png)

这些问题的标准解决方案是：picture 元素，他作为 img 元素的容器，同时扩展了 srcset 和 sizes 的能力。举个例子：

```html
<picture>
  <source
    type="image/webp"
    srcset="https://www.gstatic.com/webp/gallery/4.sm.webp" />
  <img src="https://www.gstatic.com/webp/gallery/4.sm.jpg" alt="tree"
/></picture>
```

picture 元素包含 source 标签与 img 标签，在支持 picture 与 webp 格式的浏览器下，就会加载 webp 格式的图片；否则忽略将 picture 与 source 元素，应用 img 标签中的 jpg 格式图片。以下是在 browerstack 的测试情况：图一在 IE11 上测试，IE11 不支持 webp 图片，所以加载 jpg。

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319160015765.png)

Chrome85 支持 webp，加载 webp 图片

![](http://strapi.gridsome.bruski.wang/content/images/2021/03/image-20210319160123786.png)

除此之外，picture 还可以添加 media 属性，结合媒体查询进一步控制浏览器选择图片的逻辑：

```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg" />
  <source media="(min-width: 800px)" srcset="elva-800w.jpg" />
  <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva"
/></picture>
```

对于开发者来说，我们在不破坏 img 原有能力的基础上，大大增加了操控图片的可能性，也得感谢 HTML 的渐进增强策略。

# 总结一下

在前端新标准、新技术百花齐放的时代，我们应该积极关注、拥抱新技术，用渐进、可降级的方式去为旧项目赋能焕新 ✅ 其实渐进增强，在我看来是一种严谨、巧妙的思维方式，感受其中的分层、容错、可扩展等设计理念，并尝试运用在日后的编程中吧 🤓

# 辅助工具

这里就列举几个辅助工具，有助于我们写出有良好兼容性的样式代码：

- PostCSS 预处理器插件 - [Autoprefixer](https://github.com/postcss/autoprefixer)， 根据 CanIUse 网站与你项目所支持的浏览器，自动为你的 CSS 代码添加相应前缀。
- 静态分析及 Linter- [Stylelint](https://stylelint.io/)，能检查语法错误，也能检查选择符/声明中的有问题的规则。
- 浏览器测试工具：[Browserstack](https://www.browserstack.com/)，是一款基于 Web 的实时浏览器测试工具，支持虚拟机测试全平台&浏览器版本，收费 💰
- HTML/CSS/JS 兼容性查询平台：[CanIUse](https://caniuse.com/webp)

# 相关资源

- 《精通 CSS 高级 Web 标准解决方案》（第三版）——埃米尔·比约克隆德
- MDN - Responsive images
