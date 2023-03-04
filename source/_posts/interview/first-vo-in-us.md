---
title: 记第一次实习英文面试-startup小厂
date: 2023-03-03 23:00:00
categories: Interview
tags: [Ephemeral Tattoo, Startup]
---

今天下午面完了今年的第一份实习面试，Title是Fullstack Software Engineer Intern (23 Fall) ，来自一个纽约的创业小厂。自我感觉总体不错，准备的材料都有用上，表达够自信够流利，也在一些问答环节得到面试官的肯定，如果过了应该还有下一轮面试。但也打算记录分享一下我的前期准备和面试技巧，希望对英文面试比较陌生的小伙伴有启发。

<!-- more -->

个人背景：NEU ECE Master；两年国内大厂工作经验，以前端为主后端为辅（Node.js, Python）的全栈；项目经历有大厂ToC、内部项目，也有个人开源项目；英语水平过得去

面试背景：2月24号在NEU内部的NUWork招聘平台投的，公司是做可褪色纹身的（化妆美容行业？），标注的时薪$31-37，fully remote，感觉工作的scope也够大（web全栈），就投了（话说咱难道有得选嘛？？）28号直接收到面试邀请，3月3号约了30分钟的视频面试，是技术负责人来面。

# 1 - 前期准备

收到面试邮件后，我约了一个春假前的时间面试（不然晚了没坑位哭死），然后做了一些基础调研：

- 把招聘平台上发布的信息摘下来，确定时间、地点、薪水，搞清楚他的responsibility还有技术requirements
- 上官网看介绍、主要产品、公司规模 50 人以内
- 由于是年轻人喜欢的东西，上ins和tiktok搜了下，粉丝100k还行
- 搜了下公司相关的新闻，A轮融了$20million，嗯年轻人市场嘛，应该有搞头

做完以上调研，对公司有了个初步印象：一家产品为主技术为辅的start up，做美容、年轻人市场的产品，产品倒是挺有意思的，解决了想尝试纹身但怕后悔的人群的顾虑。发展阶段比较前期，如果进去应该会很忙。

接着就准备技术方面的准备：

- 后端写着是Ruby，可我只有Python经验，于是我就用ChatGPT查了一下两者区别，发现语法其实挺相似的，甚至Ruby在性能上还有优势。现学肯定来不及，如果问到的话就把两者异同点讲一下，然后一定要声明说“既然他俩很像，那对我来说上手应该不难”之类蜜汁自信的话唬一下面试官。
- 前端的面试问题我直接找的udemy课程：**100 Front End Interview Questions Challenge.** 他把一些常见General的技术问题整理成列表，挺方便的，然后重要的是英文术语&口语表达给学到了。但他有的地方讲得不清楚，那就问问神奇的ChatGPT呗！哦对了，我用Notion的Table来存这些笔记：

![111](https://static.bruski.wang/picgo/20230304012634-5fc34734d41f05750451846bd30aafb0.png)

- Behavioral问题精心准备了下 Tell me about yourself, Why are you interested in this role/ our company, Examples that show your leadership 三个
- 简历问题准备了工作经历&项目可能会问的问题
- 最后，出于前端工程师的习惯，我拿chrome devtool测了一下他们官网架构（SSR还是SPA），performance，网络请求，responsive design，并且列了一下pros and cons，设想了一些优化方案

其他方面的准备：

- 穿一件正式一点的白衬衫
- 提前5分钟进入Zoom，打开摄像头
- 复习了一遍自我介绍
- 保持围笑，等面试官上来2333

# 2 - 面试

面试官准时上来，是一个很朋克的大哥（头发蓝紫色），是公司的技术负责人，主要负责数据团队。

打完招呼后，他跟我说他看了我的个人网站，点了点头说 “yeah it’s pretty fancy” （哈哈不亏我肝了一个周末做特效）然后还提到我在网站上放的座右铭“clean code that works”，我说我是在 Kent Beck 的 Test-driven Development 那本书里看到的，之后就是我的座右铭；他说他也是从另一本书上读到，有共鸣；我们就 “clean code” 这个点居然直接聊起了，破冰比想象中顺利好多2333

接下来就是面试环节，我先把我记得的所有问题列出来：

1. Tell me about yourself?
2. Why are you interested in our company?
3. Could you tell me about your experience in previous companies?
4. Are you going to put more efforts on frontend or backend in the future?
5. Do you know Ruby?
6. Since you said you know Python, what framework did you use?
7. Do you know what is MVC?
8. Do you know postgreSQL?
9. What would you do if you were assgned a task for features that you don’t know before?
10. To step further, What would you do if you were given a codebase and need to figure out some functions?
11. Do you have any offer right now?
12. Do you have anything to ask me?

挑几个有意思的问题讲讲吧：

比如第2题关于公司兴趣，我分了两点去说：一是提到我对hiphop文化比较感兴趣，纹身在我看来就很hiphop，自由表达，我一直没尝试是因为纹身纹了就是一辈子的事，然后看到你们公司的产品我眼前一亮，挺吸引我这样的年轻一代去尝试可褪色纹身的。二是技术层面，start up的工作scope就更大，而且title是全栈，相信比起大厂拧螺丝会成长更快。

第5题果然如我所料，问我知不知道Ruby，我就按照准备地去答：我不没用过，但我知道python，也查了Ruby和Python很像，比如都是解释性语言、类似的函数定义语法，然后Ruby性能上还有优势。既然很像，我相信我学起来应该不成问题。于是成功用类比化解了一个关于完全不懂领域的问题2333

第8题关于postgreSQL，我是真没用过也没做准备，那我就换了一个“请教”思路去答。我说我熟悉MySQL，这个数据库语言也是relational的吗？然后面试官跟我解释说，对啊，他跟MySQL功能很像，语法有点差异，但是支持更多好用的功能比如fulltext searching之类的。松了一口气，说明这个面试官人也很nice，愿意跟我分享讨论，也就以虚心请教的方式答完了一题，总比愣住说“Sorry I don’t know”要强吧！

第10题是最有意思的，他是临时加的，因为我第9题答的成了“what is your approach to solve problems”，他就说假设一个你不能google的场景，你要搞懂一段函数是干啥的，你会咋做？我就按照我的工作习惯回答：首先，我会看看函数名字，有没有注释；然后，我会用IDE快捷键，查找这段函数的all the references位置，了解上下文是怎么调用的；（到这里面试官好像不太满意。。。）最后，我会查看有没有对应的单元测试—— “That is what I wanna hear!!” 面试官激动地打断了我，把我都惊到了哈哈哈哈哈。看来这个老板很关注代码质量呀，可以可以。

11题问offer，我只能惭愧地说 I do not。。。面试官说如果拿到了其他的offer发邮件跟他同步，感觉这个竞争关系还是挺微妙的。。。

最后到我问问题，我挑了身份相关的+从udemy课程里学来的问题：

1. Does your company support return offer and H1B?
2. How is the development team? Size, colleages, and daily workflow?
3. Do you use Github private repo or GitLab to store the codebase?
4. What am I expected to do if I were in?
5. When will this program start? Is it fully remote?
6. What frontend framework does your team use for the website?

面试官挺认可我问的问题，都会说that’s a good question。反正结论是sponsership不太清楚，return offer得看情况，但这段COOP只要我这边CPT没有问题就行；也了解到团队很小，fully remote，前端就1个senior带一个实习生，面试官带几个人的后端+数据团队。开发工作活挺多的，也有一些重构、测试的工作。

在最后一个问题，我还提了一嘴说我给你们网站做了个性能测试，你们的SEO做的不错哦，满分呢！面试官也笑了说你的准备工作做的挺足啊2333

# 总结

整个面试氛围比较轻松，第一轮没有coding题，看的是项目经历；面试官人很nice；自己前期做的准备派上用场，所以没有很被动的情况。现在就等有没有机会进下一轮面试啦！

前期信息准备很重要，根据jobs描述猜题，提前多做一些思考和预案。

还有就是口语练习，自我介绍一定不要卡壳！一定要自信，对简历上的内容要特别熟悉，讲的时候不要太在乎语法，流利度更重要，努力精简地把关键点答道。每个问题可以用“that is how I xxx”的强调来结束

分享到这里，希望对大家有启发，也祝大家面试顺利！