---
title: 你好，测试驱动开发(TDD)
date: 2021-10-04 10:31:04
categories: Testing
tags: [TDD]
---

> 本篇文章阅读时间：10min
> 读者预期的收获是：
>
> - 认识测试驱动开发
> - 非常简单开启你的 TDD 之旅
> - 可以编写自动化测试
> - 重构、重新设计旧的代码更加自信

## 引子

（压抑背景音乐渐入——）

旁白：为何深夜的办公室传来程序员的哀嚎？

为何说好的一刀 999，砍下去伤害为 0？

为何程序员好基友反目成仇，因代码调用出问题后甩锅大打出手？

当个程序员，好难！（捂着铮亮的脑门）

程序员甲：自从用了 TDD，测试驱动开发之后，每天下班早了，BUG 变少了，基友不吵了。

程序员乙丙丁：真的吗？有这么神奇吗？！（集体星星眼）

程序员甲：没错，让我来给你们安利吧！

（雪花屏）Beep——

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090114314.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
Hi，我是 Bruski。开头的段子纯属瞎编，但其中描述的场景：代码不按预期执行、协作的接口不可靠等等，在我们日常工作中其实挺常见的。
原因可能千奇百怪，比如在犯困的午后工作，比如没想清楚就动手等等，而且在过程很糟糕的情况下，输出还没有自动化测试去保证，那线上在跑的程序很可能就是一颗不定时炸弹。

那有没有什么办法能最大程度避免以上情况呢？我会说，不妨试试极限编程（XP）中的优秀实践：测试驱动开发吧！
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022613382946.png)

## 别问，先感受

那么到底什么是测试驱动开发呢？

别急，先来感受一道小题目，非常简单：`FizzBuzz`。

> 题目模板地址： git clone https://github.com/bruceeewong/tdd-kata.git -b kick-start
>
> 题目来源：[极客学院](https://www.jiker.com/)-测试驱动开发实战营

---

FizzBuzz 是一个简单的猜数字游戏。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090141391.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
想象你是个小学 5 年级的学生，现在还有 5 分钟就要下课，数学老师带全班同学玩一个小游戏。他会用手指挨个指向每个学生，被指着的学生就要依次报数：

第一个被指着的学生说“1”，第二个被指着的学生说“2”，如果一个学生被指着的时候，应该报的数**是 3 的倍数**，那么他就不能说这个数，而是要说“Fizz”。**5 的倍数**也不能被说出来，而是要说“Buzz”。

于是游戏开始了，老师的手指向一个个同学，他们开心地喊

着：“1！”，“2！”，“Fizz！”，“4！”，“Buzz！”……

终于，老师指向了你，时间仿佛静止，你的嘴发干，你的掌心在出汗，你仔细计算，然后终于喊出“Fizz！”。运气不错，你躲过了一劫，游戏继续进行。

为了避免在自己这儿失败，我们想了一个作弊的法子：最好能提前把整个列表打印出来，这样就知道到我这儿的时候该说什么了。

### 题目要求

> 写一个程序，打印出从 1 到 100 的数字，将其中 3 的倍数替换成“Fizz”，5 的倍数替换成“Buzz”。既能被 3 整除、又能被 5 整除的数则替换成“FizzBuzz”。

要求：

- 代码整洁，没有重复代码
- 有单元测试，单元测试覆盖率 100%
- 5 分钟内完成

### 题目解析

相信大家应该都能很快地实现题目的要求，不过，关于单元测试部分，大家写的是否轻松呢？接下来我想给大家展示下我的做题思路——用 TDD 的方式。

测试驱动开发的要义是：测试先行，没有失败的测试，就不允许实现。所以，在动手前我们需要想清楚题目要实现什么，即拆解需求。再回顾下题目要求：

> 打印出从 1 到 100 的数字，将其中 3 的倍数替换成“Fizz”，5 的倍数替换成“Buzz”。既能被 3 整除、又能被 5 整除的数则替换成“FizzBuzz”。

打印出 1 到 100 的数字？也许会有人开始构思程序：一个 for 循环，if-else 一下，再 console.log 一下。等等，输出打印到控制台的话，我们怎么写测试验证输出是否正确呢？所以不妨转换下思路，沿着函数的本质：`input -> process -> output`来思考，其实我们要做的是：

> 实现一个函数
>
> 输入： 1~100 的数字
>
> 处理：
>
> - 3 的倍数替换成"Fizz"
>
> - 5 的倍数替换成“Buzz”
> - 3 和 5 的公倍数（或者 15 的倍数）替换成“FizzBuzz”
> - 其他数字则转换为字符串
>
> 输出：字符串

将需求完全拆解后，对应的测试用例也就信手捻来了，就让我们从最最简单的测试开始，函数就叫 fizzbuzz 吧，接收参数 1,返回字符串“1”。（这种直白的语法就叫断言(Assertion)，即把预期输出与实际输出作对比以验证程序是否正确运行）

```js
// 以下语法为Jest.js的测试写法
const fizzbuzz = require("./fizzbuzz");

describe("fizzbuzz", () => {
  test("测正常数字返回", () => {
    expect(fizzbuzz(1)).toEqual("1");
  });
});
```

执行`jest`命令运行测试，结果不出所料地报红了：`fizzbuzz is not a function`，毕竟我们此时连函数都没声明。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090225187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

那我们赶紧定义函数：

```js
function fizzbuzz(num) {
  return "1";
}
module.exports = fizzbuzz;
```

有人会说，函数体返回常量，你在骗自己吗？别急，再执行一下 jest 命令运行测试：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090306364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
Yes，测试通过，变为绿色！没错我是硬编码返回了，但这是 TDD 的第二个重要的要义：`只写让测试恰好通过的代码`。好吧我知道留着这样的代码，是不敢入睡的，那就再多加一条测试：

```js
const fizzbuzz = require("./fizzbuzz");

describe("fizzbuzz", () => {
  test("测正常数字返回", () => {
    expect(fizzbuzz(1)).toEqual("1");
    expect(fizzbuzz(98)).toEqual("98");
  });
});
```

再运行测试，闭着眼睛都知道会失败，自动化的测试还非常贴心地帮我们指出了期待输出和实际输出的差异。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090318461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
有了失败的测试，我们才开始动手写实现，实现也相当简单：

```js
function fizzbuzz(num) {
  return num.toString();
}
```

执行测试，OK，测试通过，结果又变回绿色。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090334824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
这时候我们知道第一条需求已经被解决，无情划掉它：

- 3 的倍数替换成"Fizz"
- 5 的倍数替换成“Buzz”
- 3 和 5 的公倍数（或者 15 的倍数）替换成“FizzBuzz”
- ~~其他数字则转换为字符串~~

那就写下第二条测试用例：

```js
test("测3的倍数返回", () => {
  expect(fizzbuzz(3)).toEqual("Fizz");
});
```

执行测试，结果 3 原样返回，测试不通过：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090349113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
又是一条失败的测试，快速实现它让它翻绿！

```js
function fizzbuzz(num) {
  if (num % 3 === 0) {
    return "Fizz";
  }
  return num.toString();
}
```

再次运行测试：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090402278.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
那此时再加几条测试，结果肯定是正确的：

```js
test("测3的倍数返回", () => {
  expect(fizzbuzz(3)).toEqual("Fizz");
  expect(fizzbuzz(6)).toEqual("Fizz");
  expect(fizzbuzz(99)).toEqual("Fizz");
});
```

再划掉一项需求！

- ~~3 的倍数替换成"Fizz"~~
- 5 的倍数替换成“Buzz”
- 3 和 5 的公倍数（或者 15 的倍数）替换成“FizzBuzz”
- ~~其他数字则转换为字符串~~

接下来想必大家都知道了，复制一下 3 的测试用例，改成 5,然后执行测试，失败，然后复制一条 if 语句....等等！难道你忘了，Copy-Paste 是魔鬼吗？难道我是在教你成为一名 CV 工程师吗？好了，这里引出 TDD 又一条要义：`消除所有重复`。其实通过将运算抽象为函数，很容易就能消除重复的直白代码：

```js
function fizzbuzz(num) {
  function canDivideBy(num, divideNum) {
    return num % divideNum === 0;
  }
  if (canDivideBy(num, 3)) {
    return "Fizz";
  }
  if (canDivideBy(num, 5)) {
    return "Buzz";
  }
  return num.toString();
}
```

运行测试，干净漂亮地通过了测试：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090411305.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
最后再补充一条 3 和 5 的公倍数测试用例，使用抽象好的函数实现，运行测试，测试通过后，那么整个需求就完成了。下面是完整的测试用例&实现&测试截图：

```js
// fizzbuzz.test.js
const fizzbuzz = require("./fizzbuzz");

describe("fizzbuzz", () => {
  test("测正常数字返回", () => {
    expect(fizzbuzz(1)).toEqual("1");
    expect(fizzbuzz(2)).toEqual("2");
    expect(fizzbuzz(98)).toEqual("98");
  });
  test("测3的倍数返回", () => {
    expect(fizzbuzz(3)).toEqual("Fizz");
    expect(fizzbuzz(6)).toEqual("Fizz");
    expect(fizzbuzz(99)).toEqual("Fizz");
  });
  test("测5的倍数返回", () => {
    expect(fizzbuzz(5)).toEqual("Buzz");
    expect(fizzbuzz(10)).toEqual("Buzz");
  });
  test("测3和5的公倍数返回", () => {
    expect(fizzbuzz(15)).toEqual("FizzBuzz");
    expect(fizzbuzz(45)).toEqual("FizzBuzz");
  });
});
```

```js
// fizzbuzz.js 实现
function fizzbuzz(num) {
  function canDivideBy(num, divideNum) {
    return num % divideNum === 0;
  }
  if (canDivideBy(num, 15)) {
    return "FizzBuzz";
  }
  if (canDivideBy(num, 3)) {
    return "Fizz";
  }
  if (canDivideBy(num, 5)) {
    return "Buzz";
  }
  return num.toString();
}

module.exports = fizzbuzz;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022609042694.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
划掉所有需求，爽，可以下班了！

- ~~3 的倍数替换成"Fizz"~~
- ~~5 的倍数替换成“Buzz”~~
- ~~3 和 5 的公倍数（或者 15 的倍数）替换成“FizzBuzz”~~
- ~~其他数字则转换为字符串~~

最后，执行 Jest 命令`jest --coverage`生成测试覆盖率报告：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090435934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
怎么样？100%的测试覆盖率，没有重复、多余的代码，漂亮地完成所有需求。如果你不放心，多加几条测试用例，多运行几遍测试命令，这就是测试驱动开发产出的有质量保证的代码。

有了自动化测试做保障，测试通过，我就敢说在我所预见的情况中，他会一直通过，除非，除非产品经理的需求又变了...
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226135125936.jpeg)
总结一下，在做 FizzBuzz 题目的过程中，用 TDD 的节奏开发流程如下图：![执行测试覆盖率检查](https://img-blog.csdnimg.cn/20210226132744676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

如果大家有关注到图中的颜色，那么请大家跟我念一句 TDD 的咒语，念三遍：

`Red / Green / Refactor`

`Red / Green / Refactor`

`Red / Green / Refactor`
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090448172.gif)
这就是贯穿测试驱动开发的整个流程的循环，也是 TDD 的节奏。其中：

- `Red`表示测试不通过时，IDE 的状态条报红，此时我们有了失败的测试；
- `Green`表示测试通过时，IDE 的状态条回到绿色状态，此时通过快速小步地开发，让测试恰好通过；
- `Refactor`表示重构代码，消除所有重复的逻辑。

接下来，让我们跟随 Kent 大叔深入地琢磨下测试驱动开发吧！

## 深入测试驱动开发

到底什么是测试驱动开发（Test-driven Development）呢？

按照 Kent 大叔的原话：

> TDD is an awareness of the gap between decision and feedback during programming, and techniques to control that gap.

粗浅地翻译过来意思是：

> TDD 是一种编程意识，关注着程序设计与实现结果反馈之间的间隙；同时 TDD 也是用以填平该间隙的一系列技巧。

是的，概念总是过于晦涩与抽象。作者还提供了不同角度的定义来帮助理解：

- 测试驱动开发是一种管理编程中的恐惧的方式。(Test-driven development is a way of managing fear during programming. )
- 测试驱动开发是一种开发风格：我们通过自动化的测试来驱动开发（we drive development with automated tests, a style of development called Test-Driven Development）

你一定要记住，TDD 的终极目标是：

> Clean code that works. （产出干净且可用的代码）

这是《测试驱动开发》序章的第一句话，也是我编程的座右铭。

### TDD 开发模式

首先我们要搞清楚 3 个问题：

1. 什么是测试？
2. 测什么？
3. 什么时候测试？

#### 什么是测试

`测试`作为动词，是`“去验证”`的意思。测试作为名词，是`对预期得出可接受或者不可接受判断的一个过程`。

按 Kent 大叔的意思是：

> 测试是开发者的基石，也是将对程序运行结果从未知的恐惧转化为熟知的手段。

不喜欢写测试的程序员，通常将经历这样的消极循环：`感到恐惧，因为没测试` -> `越恐惧，压力越大` -> `压力越大，越不会测试`。

而善用测试的程序员呢？正向循环应该像这样：`越感到恐惧，越执行测试 -> 越测试，恐惧越小 -> 压力越小，越愿意测试`。

#### 测什么

我们这里指的，是程序级别的单元测试(Program Level)，主要关注`逻辑`与`数据`。

对于`逻辑`的测试，一般来说等同于需求，我们要对需求进行编程级的拆解，即要能拆解为可以动手编码的若干步骤，通过不断地写下你的期望与实际输出的测试语句（即断言），然后实现代码让其通过，从而一步步达成目的。

对于`数据`的测试，这里我也没有很多实践，有几点可以分享:

1. 不要使用真实的数据（数据库数据、网络请求等）
2. 按照预期的数据结构，构造直观的伪造数据来满足测试。

#### 什么时候测试

按照测试驱动开发的节奏，每当：

- 动手编程前，先写出一条会失败的测试
- 重构前，保证测试通过

了解完前置概念后，又该怎么落笔我们的第一个测试用例？

### Red Bar Patterns

> Red Bar，顾名思义：因执行测试失败而显示红色的状态栏

要让测试失败，那首先要写下你的测试，我们上一节介绍了需求拆分，得到了一份 todo-list，那我们究竟该自顶向下实现（即从大问题的具体用例开始实现），还是自底向上实现（从小模块开始，再逐步聚合）呢？

其实这两者都不能很好描述 TDD 的实现过程，准确地说，实现顺序没有太大关系，因为 TDD 是基于 `known-to-unknown` 的模式进行，即用已知的来推出未知的。我们在拆分需求为一条条可编程验证的用例时，就是将未知的庞然大物拆解成不废力气就能达成的小目标，我们知道如果一步步实现了所有子测试，最终需求就能实现。

在 TDD 这里，万事开头难，但测试开头易。第一个测试应该写一条`测什么都不做的操作`的测试，这里看似没什么意义，但是它确实验证了：

- 这个操作属于哪里？
- 什么是正确的输入？
- 什么是基于正确输入的正确输出？

### Green Bar Patterns

> Green Bar，顾名思义：因执行测试成功而显示绿色的状态栏

在 FizzBuzz 实现的过程中，我们用到了几种快速让测试通过的技巧，分别是：

#### Fake It ('Til You Make It) 伪造数据

比如在 FizzBuzz 最开始的时候，为了让测试通过，直接在函数里返回常量。

为什么要写早晚要换掉的实现？原因有两点：

- 心理暗示
  - 测试成功比测试失败好
- 范围控制
  - 专注在解决当前测试上，避免过度设计
  - 保证当前代码始终可用

#### Triangulate 三角测量

- 从不同角度测试代码，让伪造数据的代码失败，然后抽象、实现，让测试通过。例如我们前面用两条测试，宣告了硬编码返回"1"的代码实现的死亡。

#### Obvious Implementation 最简实现

- 既然用例已经拆分成小步，一定可以快速实现，否则，反思步子是否迈大。

- 写恰好实现的代码。

至此，结合 FizzBuzz 的解析，我们已经体验完测试驱动开发最核心的流程。

### 来总结下吧

再回到定义，测试驱动开发本质上是一种编程思考和实践的一种风格/方式，比起一开始的顶层设计，他更关注需求与实现之间的距离，要求程序员能拆解成若干可测试、可实现的步骤，然后借助自动化测试工具，按照一定的节奏`Red / Green / Refactor`、测试技巧和编程手法，从而产出干净的、可工作的代码。

- 因为测试先行，倒逼我们必须思考清楚问题应该如何解决，避免了低效地走一步看一步的浑浑噩噩；
- 因为测试先行，我知道做到什么程度算完成，并且自信地认为在我所预期的情况内，程序可以良好地工作。
- 测试用例可以作为更棒的注释而存在，让协作的同事更清楚地知道函数的用途和用法。
- 提交代码时，看着绿色的状态栏，心情愉悦，安心下班！

## TDD 的挑战

TDD 更多的应用在程序级别的单元测试，这一块是开发人员完全自主掌控的部分。而在此之外的一些场景，TDD 也许就不那么合适，比如：

- 对于 GUI 的测试（网页、App 级别的 UI 测试）
- 对于依赖数据库的测试（通常我们使用 mock 对象测试）
- 不要去测第三方的代码，那应该有他们的开发去保证（如框架等）
- 不能测试编译器之类的东西。

## 写在最后

作为一名 Web 前端开发，在开发业务逻辑时，我都会有意识地使用 TDD 的方式来实现。（UI 方面的测试实践并不多，还要继续学习！）
TDD 测试驱动开发带给我的开发体验是：

1. **享受可预测、尽在掌握的开发体验**
   1. 当通过了所有测试、开发也就结束了
   2. 并且开发结束了，可预见的场景不会有太多 bug
2. **给自己留一瓶后悔药**
   1. 第一次的实现可以很烂，但只要有测试，再回过头来，只要测试是通过的，就可以放心地重构。
   2. 如果祖传代码没有测试，那就尝试找到程序输入输出的接缝处，给他补充测试，这样可以最大程度确保重构不会大刀阔斧地破坏原有逻辑。
3. **保持代码年轻的秘诀？**
   1. 如果是测试驱动出来的代码，将拥有输入输出清晰、幂等性特点。
   2. 每当添加新特性前，先思考清楚，先写测试，代码不会随乱涂乱改而腐败。
4. **同事协作时之间更放心**
   1. 你产出的代码值得信赖。
   2. 同事也用 TDD，看着测试用例就知道怎么用了，真香。

这篇文章只是展示 TDD 的基础玩法，想要深入了解测试驱动开发，去读下 Kent Beck 的 《Test-Driven Development By Example》，感受 Kent 大叔的幽默与智慧吧。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226090517230.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
愿你的程序无 Bug，早点下班！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226134408831.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
