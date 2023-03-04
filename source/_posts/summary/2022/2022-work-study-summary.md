---
title: 2022工作学习总结
date: 2023-01-02 23:59:59
categories: Summary
tag: [Annually]
---

# 2022年度目标

- [x]  被美国一所大学录取，赴美留学
- [x]  All in Web3, Be a builder
- [x]  培养写作习惯，创立个人学习分享IP
- [ ]  培养阅读习惯，每月至少阅读1本书
- [ ]  学习后端编程语言，Python为主，C++为辅

自评: ★★★★☆

<!-- more -->

# 工作

## Suiet Wallet

![img](https://static.bruski.wang/picgo/20230103010045-dd3d2a7628a46adaedda8edb22632da0.jpeg)

> Github仓库： [https://github.com/suiet/suiet](https://github.com/suiet/suiet)
> 

和本科朋友组团队，从8月开始投入，做到12月已经成为Sui钱包生态的Top3，推特粉丝数破100k。

- 前期主要负责前端的架构设计和主要的页面实现。
- 中期开始针对[Wallet Standard](https://github.com/MystenLabs/sui/tree/main/sdk/wallet-adapter/wallet-standard)进行实现、推进各DApp接入，优先支持他们的开发需求，占据主流产品的前列位置。
- 后期主要是迭代与修bug，也开始深入钱包底层原理。根据[巴比特的开源文章](https://www.8btc.com/books/834/ethereum-book/_book/%E7%AC%AC%E5%85%AD%E7%AB%A0.html)学习底层技术，了解到所谓钱包其实就是本地的私钥管理和签名工具，配合与区块链交互的RPC API即可实现良好体验的钱包应用。

通过开发钱包Chrome插件，我的前端整体架构设计能力得到锻炼。Chrome钱包插件的架构涉及跨上下文通信，主要利用service worker实现类后端服务，通过[Chrome Messaging机制](https://developer.chrome.com/docs/extensions/mv3/messaging/)建立通信通道，封装成JS Bridge来给前端UI弹窗提供API。存储则是利用IndexedDB存储持久化数据，[Persist Redux](https://github.com/rt2zz/redux-persist) + [Chrome Storage](https://developer.chrome.com/docs/extensions/reference/storage/)存储内存数据。

![img](https://static.bruski.wang/picgo/20230103014836-0da63732c8a9d2c00225bdbfce72ec65.png)

在Wallet Standard的实现过程中，整体交互还是比较复杂的，涉及主体有：DApp网页环境、Chrome Content Script沙盒、钱包Service worker、钱包UI。光这块的消息传递就很麻烦：DApp网页环境与Content Script沙盒环境是通过window postMessage广播传递消息，这里我JS事件编程利器[RxJS](https://rxjs.dev/)把广播收发包机制包装成类似RPC函数调用（屏蔽底层通信细节），实现独立连接的效果；Content Script与Service worker又得通过Chrome Messaging来通信。

其次是wallet-standard的自动检测钱包适配器的机制实现。本质上就是DApp引入包含wallet-standard的包（如 [suiet wallet kit](https://kit.suiet.app/)），然后wallet-standard会开启一个特定事件监听register-wallet listener，wallet们各自在页面加载后触发注册事件，注册符合standard的adapter，从而完成DApp对钱包的auto-detection。

![img](https://static.bruski.wang/picgo/20230103014849-6f7a7e154445882efd311c4fbbe86e51.png)

Suiet是我们团队第一个作品，凝结了我们对Web3的期望和激情。Team Work的过程中我不仅是技术上得到提升，和不同领域的伙伴协作也是拓宽了我的眼界。币圈里做技术的人良莠不齐，很多基建也是不太可靠，开发体验远不如web2。但反过来想，这正是我们年轻工程师的机会呀，把web2里锤炼过的技术，再注入对web3的热情，Build here a better world。

![img](https://static.bruski.wang/picgo/20230103010059-aec4059c16a5fa10734ad9639d3c95ce.png)

投入度：★★★★★

产出度：★★★★★

影响力：★★★★★

## Suiet Wallet Kit

![img](https://static.bruski.wang/picgo/20230103010119-f0c4786c1b1c1f8ecf124abb287c17f6.jpeg)

> Github仓库：[https://github.com/suiet/wallet-kit](https://github.com/suiet/wallet-kit)
> 

Wallet Kit是我们做钱包的副产物，初衷是抢占与dapp合作的机会，通过all in one的钱包连接管理套件助力铺开我们钱包。这块由团队其他两个小伙伴完成初版的搭建，那会儿文档不太完善，可配置性也不尽人意，接入效果并不好。在wallet-standard之后，因我比较熟悉其整体逻辑，我开始着手重构wallet kit，主要围绕条件判断逻辑冗杂与可配置性这两块做优化。

造成第一点条件判断冗余的背景是：钱包适配器们对standard里features功能的实现不尽相同，甚至是缺失的。于是我抽象出一个通用适配器接口和实现类，底层基于wallet-standard约定的函数调用方式+自定义错误处理将异化逻辑包裹起来，这样做能够保证暴露给用户的函数签名是一致的，优化DApp开发者的体验。

第二点可配置性，作为React插件包，我们既提供了Hooks又提供了UI组件（按钮、弹窗），这对于开发能力较弱的团队来说是很方便的开箱即用，但是，对于大一点的团队，他们对UI的定制要求很高，甚至是只用我们的Hooks逻辑部分，UI用自己的。

所以，我首先针对暴露给开发者的Context上下文进行了重设计，把钱包项的配置列表以及从浏览器中自动检测到的钱包项都提供出来，有助于开发者们构建自己的钱包选择器UI。其次，我支持了钱包预设选项的传入，让开发者能自己决定要展示的预设钱包列表和排序。

在重构的过程中，我学到一个很重要的经验，那就是**「向下兼容」**！做任何破坏性的更新，一定给已经在用的开发者一个接口的平稳过渡的时间，否则开发者一更新你的包，发现原来的函数改掉了或者直接没有了，项目因升级而运行失败是很糟糕的体验，更何况币圈的开发者能力有得真的弱到让人大跌眼镜（比如文档写的清清楚楚但是还是跑来私戳你要怎么做）。**正确的做法应该是：如果可以做到只加参数而不影响旧的功能，那就直接改；否则，将旧的接口标注为deprecated，并行启用新的函数接口，并且写好migration迁移文档，设定合理的弃用时间节点并告知开发者们。**

最后我还把我们的文档给翻新了，用更有趣的方式来介绍用法。我发现我对emoji风格的文字叙述还挺在行的哈哈。

![img](https://static.bruski.wang/picgo/20230103010128-82c875baadc689e934abd17110b8f47a.png)

Anyway, 这是我第一个做出来有项目在用的开源项目：Github 20+ repo的引用，以及npm周峰值800+的下载量，完全超出了我今年定下的要为开源社区贡献能力的目标～好有成就感，哈哈！

![img](https://static.bruski.wang/picgo/20230103010134-667a4388ac7df90f66ef39335fc6fb8a.png)

![img](https://static.bruski.wang/picgo/20230103010140-748e59d9ef77ac2946ed09f959596180.png)

投入度：★★★☆☆

产出度：★★★★☆

影响力：★★★★☆

## Let.sh

> Website: [https://let.sh/](https://let.sh/)
> 

Let.sh是我和lzb第一个合作的项目，是一个all-in-one的开发部署平台。支持如static files和web服务的一键命令行部署（类似vercel），并且支持如本地内网穿透、部署后的metrics检测等features，是比较适合个人开发者的开发工具。

最初是因为想直接集成Arweave——做Web3永久存储的一条区块链， 打他们的hackerthon来入圈。于是我们在let.sh原有的逻辑上增加了静态文件部署至Arweave链，实现所谓真正的DApp（Decentralized App去中心化应用）。但是实际并不好，1是每次部署都要扣gas，2是部署和访问都很慢，比不上web2体验，主要受限于ar链的出块共识机制以及通过ipfs做路由寻找peers的速度。

![img](https://static.bruski.wang/picgo/20230103010148-7f1beea6a6d052a59e660b6db244b053.png)

另外还增加了文件树与预览的新特性，树组件真的是前端做页面少数要用到算法的地方了2333。

![img](https://static.bruski.wang/picgo/20230103010154-90e314fc7160eea9a8f6812c2a63f82e.png)

除了搭建系统之外，还做了一些跨领域的尝试：

- 横向调研相关deployment的Web2、Web3 PaaS产品
- 初次接触pitch deck概念，了解基本流程
- 制作全英文演示视频 for AR Demo Day

这是一个我挺喜欢的产品，lzb这种本想着做给自己用结果发现可以发展成side project的技术热情，我挺佩服的，也要向他学习呀。

投入度：★★★☆☆

产出度：★★☆☆☆

影响力：★☆☆☆☆

## 其他

- **Leetcodes**：从12月开始才正式刷leetcodes，找到了 [labuladong 的刷题教程](https://labuladong.github.io/algo/)，把算法解题总结成框架与套路，真的能做到举一反三；而且它做到完全免费开源，这点非常respect！（我今年也有在考虑做免费的开源教程哦，教大家做些能顶到实习里的入门前后端项目，期待一下吧）希望能在一月份跟着他把有代表性的200题刷完！
- **小游戏Web发布系统**：在TME时期做的内部静态资源发布系统，也是我离职前的最后一个项目，第一次尝试用vue3来做，挺有意思的，界面和交互上我也花了点心思；和Milu & 显维大佬一起开发，还是要和技术好的人一起做事学的快呀！
- **Magician教学管理系统**：和一个魔术培训的朋友讨论做一套教材管理系统，解决企业培训机密材料容易泄漏盗版的痛点。做前后端全栈项目，用的antd pro+strapi cms，结合腾讯云的ppt转 html的云服务，实现了ppt云端存储与web端播放的能力，既然能做到web那就能加入鉴权了！可惜，这个项目最后因我工期无法排期而夭折，并且说实话我目前一个人做全栈还是有些吃力，主要是后端的设计还是嫩了点。真对不起我的合作伙伴呀，没有说到做到。希望新的一年，可以成长为名副其实的全栈开发者。

# 学习

## 学业

入学研究生前后，在专业方面重新做回学生，静心打基础：

- 7374 - Fundamentals of Networks：成绩A，班级第二名，把计算机网络从应用层到数据链路层扎实地学了一遍，喜欢Dimitros教授一丝不苟的教学风格，但workload非常重，期末考前一周还在赶它的大作业。我甚至收到了他的phd邀请！下学期还选了他的project课，先进他的wireless实验室一探究竟吧！
- 7205 - Fundamentals of Engineering：成绩A-，妥妥的算法课。华人教授，讲课不太用心啊，课上学到的很有限。但是作业和考试都非常有挑战，直接拿google算法面试题来当作业也是吊的不行。但是被锤了之后还是回顾了不少算法基础，对刷题很有帮助。
- MIT CS Online Course：暑假上了几节MIT的公开课，没坚持下去，纯当练听力了。
- English：英语能力大大提升！通过开学参加的Hackerthon，得到了好多用英语听说读写的机会，果然环境就是最好的语言学习要素。

## Web3

- Crypto Wallet ：学习了[区块链钱包](https://www.8btc.com/books/834/ethereum-book/_book/%E7%AC%AC%E5%85%AD%E7%AB%A0.html)的底层原理。
- Sui & Move: 学习了[Sui区块链](https://docs.sui.io/devnet/learn)的设计特色以及其智能合约语言Move的基础，没有深入学，2023年应该会重点去学，毕竟要在这片土地开拓事业！
- 币圈基础：强烈安利[币圈李白老师的Youtube分享](https://www.youtube.com/@biquanlibai)，很务实的技术讲解，把什么是区块链到里面的设计细节都讲的明明白白，从小白到入门就靠李白老师的视频！

# 阅读

- ✅ Introduction to Algorithms - Third Edition：算法教科书，从学术的角度来学算法，各种数学证明还是挺头疼的，但是会对算法的正确性和精巧有深入的理解。
- ✅  Computer Networking - Kurose Ross：网络教科书，讲的很好，还附带许多Wireshark实操实验，是入门计算机网络的好书。
- ✅  Tokenomic paper：了解了什么是币圈项目的tokenomic（代币经济学），本质就是其对应虚拟货币的供给与产出的机制设定。货币供给如何分配：团队、投资人、社区的比例；货币如何产出，限不限量，如何发行如何销毁。大致就是用白皮书的形式来阐述项目所对应的虚拟资产该如何运作，来更好地激励整个社区与项目持续壮大。
- ✅  EIP，BIP: 阅读了有关以太坊和比特币的improvement proposals，相当于行业规范一个的存在。从本质去理解了何为Token，何为NFT等概念，对开源社区的协作方式进一步体悟。
- 🔖 System Design Interview：在看的系统设计面试书，里面介绍了系统架构该如何演进地去设计，并且提供了很多不同的案例，感觉是很棒的书，适合要转后端的我。
- 🔖 《重构》：已经看过的代码指导神书，再次翻开，却迟迟没看完，羞愧。
- 🔖 《图解密码技术》：在看的课外知识拓展书，讲计算机密码学发展历史，以及各个密码原理的讲解，有意思的一本书，还在看。
- 🔖 《算法图解》：上7205的课时候求助于它理解动态规划，是本有趣的书，python实现也好理解，打算抽时间把他看完。
- 🔖 《鲁迅全集》：在11月国内zz动荡的时候，结合b站up主智能路障讲解的鲁迅系列，心血来潮看了其中几章经典的如《狂人日记》、《孔乙己》、《药》，鲁迅先生的振聋发聩的文字给我很大的鼓舞，要兀自地发光，摆脱冷气，向上走。目前电脑旁摆着鲁迅先生的头像，激励我每一天。
- 🔖 C++ Primer Plus - 6th Edition：看了一部分的C++经典教材，做网络那门课的时候回顾C++用，当工具书来用了，不会了就来查查。

# 写作

- [2022年度总结·破与立](https://blog.bruski.wang/2022/12/31/2022/20221231-annual-summary/)
- [周刊02｜不瞒你说，我其实是MIT的学生](https://blog.bruski.wang/2022/06/11/2022/20220611-weekly/)
- [周刊01 | 两个月搞定美国研究生申请？我居然做到了](https://blog.bruski.wang/2022/05/29/2022/20220529-weekly/)
- [周刊开刊｜开窗便有拂面风](https://blog.bruski.wang/2022/05/22/2022/20220522-weekly/)
- [暂别啦，鹅厂和TME](https://blog.bruski.wang/2022/03/07/2022/goodbye-tx-tme/)
- **[2022，Round2](https://blog.bruski.wang/2022/02/13/2022/2022-annual-target/)**

2022在写作上是有突破的！开启了个人公众号，学着技术偶像[Airing的Weekly周刊](https://weekly.ursb.me/)，也开始写周刊，并且写出了一篇爆款文章"暂别啦！鹅厂&TME"，分享了自己如何在大厂工作和出国留学中做抉择的心理历程，4700+阅读还有184点赞，只发了一条在自己朋友圈，完全就是自然增长哦（比如朋友转给他们的朋友看）。非常意外触动到许多和我一样被互联网卷得不舒服的朋友们，他们纷纷来私戳我，说看了我的文字很受触动，佩服我做了他们还不敢做的事，也收到了很多祝福。我才体会到，原来文字真的能传达勇气和力量呀！2023，也请继续保持写作，用知识和故事影响更多人吧！

![img](https://static.bruski.wang/picgo/20230103010207-ad44e402a0950a38261a02fc1ab672c3.png)

![1](https://static.bruski.wang/picgo/20230103013108-9c580a8956f03091f7e825d591947f0b.png)

细数2022，我做了很多新尝试，建立了自己的crypto团队，做出了一些不可思议的成就。技术的增长还是主要在前端这块，但是今年已经踏入了web3，增加了对区块链的理解，并且还做了许多跨领域尝试，希望在2023能够保持对跨领域的热情，并且把根在区块链和后端扎的再深一点，逐渐转型为后端工程师，理由嘛，当然是不满足于只跟浏览器打交道咯。

2023还有一个很大的愿望，那就是通过自己的知识去变现，为他人提供价值，很大胆的想法，希望能做到！

最后希望自己在新的一年，能够更聚焦少数领域，成为某个领域的专家，再努力自律一点，把影响力再扩大一点，把收入再增加一点，再爱生活一点！

> PS：最后贴一下我的公众号，顺手关注一波吧！

![qrcode_for_gh_1630cf794b90_258](https://static.bruski.wang/picgo/20230103010521-fa839aeab39800eb4f0ca78d4fa997cd.jpg)