---
title: 把你的博客拎到云上生长吧！
date: 2021-08-15 20:47:28
categories: coding
tags: [cloud, web, devops]
---

Ayy yo what's up，这里是Brrruski aka. 搞程序的Gatsby👨🏻‍💻 

作为第一篇正式对外的文章，想了很久要分享什么主题才会比较有意思，还要易上手，还要接地气🧐 那最近也是看到朋友的博客（基于Hexo搭建的），眼馋里面的markdown代码块、时间线timeline整理以及自动分类与标签词云呀🤩 （天知道我作为程序员是怎么忍受wordpress / ghost默认的markdown支持的🐶 

于是我兴致勃勃地鼓捣了一番Hexo博客，在本地已经装饰的漂漂亮亮了✨ 。到了该部署的环节，我一拍脑袋💡，不如摒弃我的小水管server🚰，玩一次地道的云原生部署玩法吧？

经过一早上的踩坑，终于在云上建好属于自己的一亩三分地了，简直比在深圳买了房子装修完还开心呢（醒醒，你哪来的房子

所以我决定，不如就分享一下**我是怎么把我的Hexo博客拎到云上去的吧**☁️

# 网站托管(Serving)

让自己的网站能被大家访问到，我们需要进行名为`网站托管`的一系列操作。我们先简单回答一下关于上网冲浪🏄‍♂️ 的两个灵魂发问：

1. 🧐 网页的本质是什么？
2. 👨🏻‍💻 我们为什么能在浏览器上搜到并看到网页？

## 1. 网页的本质是什么？

网页的本质其实就是一堆按格式书写的字符，即我们常说的`HTML`（超文本标记语言），文本的内容大概长这样：

```html
<!doctype html>
<html>
  <head>
    <title>Bruski's Website</title> 
  </head>
  <body>
    <h1>My Intro</h1>
    <p>Yo whatsup, this is Brrruski aka Coding-Gatsby</p>
    <img src="/image/handsome-selfie.jpg" />
    <audio src="/audio/handome-bgm.jpg" autoplay />
    <video src="/video/me-skating.mp4" controls />
  </body>
</html>
```

看到里面的标签了吗，我们通过书写这类浏览器能识别的标签，来创建内容、引用其他资源，经过**浏览器**处理后渲染到屏幕上，就变成我们看到的色彩缤纷、能够交互的网页啦。

![](https://file.bruski.wang/images/WeChat7bce94b87be2bc5b8fd3bbdace6092a7.png)

## 2. 我们为什么能在浏览器上搜索并看到网页？

![](https://file.bruski.wang/images/WeChatcc9a47b3aadb64ede85cef84fe2c270d.png)

设想我们在网上买衣服，我们先按名字搜到某个牌子的衣服，如果找到了提供该衣服的商铺，购买下单，商家处理好之后发货，不久后你能穿上心仪的衣服啦。

同理，当我们在浏览器的地址栏中输入某个网址的时候，浏览器会发出寻找该网址对应服务的请求，如果找到了，提供该网站服务的服务器会把相应的网页内容返回给浏览器，浏览器解析后，网页内容就呈现在我们眼前了🤩

所以一个网页要想能被别人访问到，需要具备满足以下条件：
1. 一个能找到你网页的地址（IP+端口或者域名）
2. 一个能处理浏览器的请求，把资源返回去的服务（HTTP Web服务）

**所以网站托管做的事情就是：**
1. 📦 把网页等资源上传到某个地方。
2. 🤖 启动一个能对外提供服务的HTTP Web服务，把我们的网页内容发送给请求方。
3. ⛳️ 设置这个服务的访问地址，可以是IP+端口，也可以起别名（域名）。

## 网站托管的方式

通过提供HTTP Web服务进行网站托管的方式大致可以分为：
1. 借助能处理静态资源的Web Server，如Apache，Nginx。
2. 由Web Server动态生成HTML内容，如JSP。

由于我们今天的主题是博客托管，我们只讨论第一种，只提供静态资源的方式。

# 云原生(Cloud Native)

![cloud native](https://file.bruski.wang/images/cloudnative.jpg)

首先我们简单快速了解一下`云原生`这个概念，这里引用了掘金的文章[《什么是云原生？这回终于有人讲明白了》——华为云开发者社区](https://juejin.cn/post/6844904197859590151 "什么是云原生？这回终于有人讲明白了")。

> 云原生是一种基于云计算技术的构建和运行应用程序的方法，是一套技术体系和方法论。

> 
>
> 云原生（CloudNative）是一个组合词，Cloud+Native。
> - Cloud表示应用程序位于云中，而不是传统的数据中心；
> - Native表示应用程序从设计之初即考虑到云的环境；

Pivotal公司的Matt Stine于2013年首次提出云原生（CloudNative）的概念，对云原生的定义总结为4个要点 **DevOps + 持续交付 + 微服务 + 容器**。一句话概括：**原生为云而设计，在云上以最佳姿势运行，充分利用和发挥云平台的弹性+分布式优势。**

![图片来自掘金-华为云开发者社区的文章](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/6/23/172df2b96424af31~tplv-t2oaga2asx-watermark.awebp)

其实结合实际理解，云原生已经具象化地存在于各大云服务厂商的官网中：云服务器、云存储、云容器、DevOps流水线等等，他就是一个船新的软件开发和部署的新生态，帮助开发者们更简单、更快地打造、发布与维护产品🔫

## 传统网站托管 VS 云原生网站托管

![传统网站托管 VS 云原生网站托管](https://file.bruski.wang/images/WeChatd49ddc5ff8c5d8104f9ea7a298962cd2.png)

传统模式托管和云原生托管最大的不同在于：**资源部署的维度不同**。

传统模式按**硬件资源**为单位部署，云托管按**功能服务**为单位部署，两者带来的服务架构设计、实际操作与效果都有着很大的差别。

**传统网站托管**: 我们需要自己维护服务器，把文件上传到服务器的具体路径，接着设置Web Server啊，安装证书 ￥&！# ，一顿操作之后才能完成网站托管。

**云原生托管**：文件打包后，上传到`对象存储服务`，设置一下存储桶为静态网站托管模式，嗯就可以了，什么域名啊、证书啊全部自动生成。什么，你想让你的网站在全国各地的访问速度都更快一点？那再到网页上点击配置一下`CDN加速服务`，让它将你的网页分发到全国各个边缘节点中，通过统一的加速域名来访问，用户访问速度杠杠的。

由此可见云原生托管不仅简单便捷、灵活按需、省心省钱，而且服务的效果和质量都比传统模式强😎 既然云托管这么香，那我们赶紧进入实操环节体验一把🤨

# 实战: 把这只Hexo博客拎到云上吧

⛳️ 明确我们的目标：**将Hexo博客项目快速地部署，可以通过HTTPS域名访问。**

> 注意：实战的云服务商选择[腾讯云](https://cloud.tencent.com/)，其他云服务商操作同理

## 前置准备

- 安装好[git](https://git-scm.com/)、[NodeJS](https://nodejs.org/en/)与Npm的环境
- 一个[hexo](https://hexo.io/)博客工程，并设置好github仓库关联（其他代码托管服务同理）
- 一个[腾讯云账户](https://console.cloud.tencent.com/)

Hexo博客工程可以下载示例工程：
```bash
# Github
git clone https://github.com/bruceeewong/hexo-demo.git
# Gitee码云
git clone https://gitee.com/bruceeewong/hexo-demo.git
```

安装npm依赖
```bash
npm install
```

预览页面效果
```bash
npm run server  
```

通过浏览器访问的本机地址 `http://localhost:4000`，查看效果：

![](https://file.bruski.wang/images/WeChat1ecd3787f1fd528997160c78c5536131.png)


最后测试打包命令是否正常：
```bash
npm run build
```

如果在当前目录下新增`public`目录，说明打包命令可用，就进入下一步。

![](https://file.bruski.wang/images/WeChatb64c9fe224838158fb6b52da886c7d5f.png)

## 开通网站托管相关的云资源

我们需要开通以下资源：
- [(必须)对象存储服务-腾讯云COS](https://console.cloud.tencent.com/cos5)：用于存储网页等静态资源
- [(可选)DevOps服务-Coding CI](https://console.cloud.tencent.com/coding)：用于搭建CI/CD流水线，将构建与部署自动化
- [(可选)内容分发网络](https://console.cloud.tencent.com/cdn)：用于提升网页与其资源的访问速度

### 开通对象存储服务&上传静态资源

![](https://file.bruski.wang/images/WeChat3d6934a49c0d0d345f5de7a278eecc7b.png)

![](https://file.bruski.wang/images/WeChat72fbcdfcfe9df3d4c57a33b3fd2977ca.png)

根据指引，为了存储我们的静态资源，我们需要创建一个`存储桶`资源。

![](https://file.bruski.wang/images/WeChat2ea534e24f4fb12741262e55302c0c55.png)

> 注意存储桶的访问权限我们先设置为 **公有读私有写**，方便在没有接入CDN服务前直接访问网页。

接下里来两步直接按默认的来，点击创建。

![](https://file.bruski.wang/images/WeChat50030689141f3a57cea6683d4f419f08.png)
![](https://file.bruski.wang/images/WeChatb8839e6cb3014f838816f6df024dba10.png)

存储桶创建好之后，我们找到`文件列表>上传图片`按钮，挨个把本地构建好的`public`下的文件夹&文件上传（好累，这里只是让你体会一下没有自动化工具的辛苦😂

![](https://file.bruski.wang/images/WeChat34bf0361a4989ab529e348966d21b6f2.png)
![](https://file.bruski.wang/images/WeChat967cc3b8f3b05af2b6cf45551ce8e1a0.png)

到这里，我们已经把静态资源都传到存储桶中了，接下来就是设置其访问方式。

### 开启静态网站托管模式

![](https://file.bruski.wang/images/WeChatcd8aa69b676f64ee1dc88d913c34db4e.png)

到这里，网站的托管就完成了，是不是不敢相信？我们把`访问节点`的URL复制到浏览器试试：

![](https://file.bruski.wang/images/WeChat470ea752769e22d855ee7eeada46cac3.png)

怎么样，我们是不是已经完成了定下的目标：**将Hexo博客项目快速地部署，并可以通过HTTPS域名访问。**

### 你能做的，岂止如此

复盘一下刚才的操作，最费时的就是手动上传静态文件了（可能还不如 `scp` 传到服务器快呢）如何摆脱手动上传文件？解决的办法无疑就是将这部分操作**自动化**，让我们接入`Coding CI DevOps`服务，创建一条CI/CD流水线，来拉开跟手工部署的差距。

搜索Coding CI服务：
![](https://file.bruski.wang/images/WeChat19217857e403ec93c3866aa03ba853cd.png)

创建项目，这里只勾选 `构建流水线` 即可：
![](https://file.bruski.wang/images/WeChat2b623dfd4b271d4019e3c839fce36835.png)

选择流水线模板 `React + COS`（我们要的只是对接COS上传的部分）
![](https://file.bruski.wang/images/WeChatab21299e84db5e3641f3e6017ac5eb0b.png)

代码仓库选择 Github 或 码云（需完成授权），选中我们的仓库；关掉的单元测试选项（我们的hexo项目没有此命令）
![](https://file.bruski.wang/images/WeChatd9184f38f7f538573157f72f192c2353.png)

接下来的上传COS Bucket配置部分参考下图。注意不勾选“创建后触发构建”，还有一些要配置的地方。
![](https://file.bruski.wang/images/WeChat770c73f81ce400bea6ce966292869acb.png)
![](https://file.bruski.wang/images/WeChata76386c869e0bb1b2f0dabd7e557f0ba.png)
![](https://file.bruski.wang/images/WeChatf330c69e1e2dc80fc7a4ff1c80415011.png)

修改一下`环境变量>产物的路径名`（hexo的产物路径叫`public`）
![](https://file.bruski.wang/images/WeChat7582ac6783ba3999ed08387c6e7c8b2d.png)
如果选择的是github，触发的分支注意有可能需要设置为 `main`（不知道微软为啥要改掉master）
![](https://file.bruski.wang/images/WeChat932f6cdbb2945ef7e0b8b66097f634c0.png)
最后点击构建，短短23秒流水线就执行完成了。
![](https://file.bruski.wang/images/WeChat346090d97677523ac0f028ce028b057e.png)

**这意味着我们以后只需编辑与提交代码，构建和部署上传的工作交给流水线去做就好了**😆

### 🚀 最后一步，配置CDN加速服务

CDN内容分发网络的工作方式大致如下，通过CDN服务的接入，把源站的文件分发至各个边缘节点。

![](https://file.bruski.wang/images/WeChat50b359e3ac992e22b206d2a8cc137cf1.png)

为了能让用户能从最近的CDN节点获取资源，我们应该只对外开放CDN域名，隐藏存储桶的访问路径（可以设置为私有读写）

![](https://file.bruski.wang/images/WeChatda1dd4e845c01bf2cdb7362066037d0d.png)

落到腾讯云这，有两种方案：
- **使用COS提供的默认CDN加速域名**
  - 优点：简单快捷，一键生成带ssl证书的域名
  - 缺点：域名太长
- **配置自定义加速域名**
  - 优点：可定制域名
  - 缺点：配置稍微复杂一些，需要前往CDN控制台配置
  
限于篇幅有限，就只介绍默认CDN加速域名的配置:
![](https://file.bruski.wang/images/WeChat87dc7e24aeb543c1a696f0c5c09d0f37.png)

> 鉴于我们的hexo博客是将markdown生成html文件，为了防止因为缓存而导致用户不能看到最新更新的文章，我们还需要设置CDN的缓存配置。

配好默认CDN域名后，把html文件的CDN节点与浏览器缓存都设为 **不缓存**。

![](https://file.bruski.wang/images/WeChatc84d990e1a39ed90d8ba04f1db454dfe.png)
![](https://file.bruski.wang/images/WeChatde14f1d0b4d51ab4570286323a72d73a.png)
![](https://file.bruski.wang/images/WeChat9329c11db22ab53978b0a5335af873e0.png)

最后，我们通过CDN加速域名 `https://hexo-demo-1257249827.file.myqcloud.com/` 来访问一下我们的页面:

![](https://file.bruski.wang/images/WeChatb155afe3324e48434f01fdcfb3138cc5.png)

快速地打开了，没有任何问题 🚀

# 总结

至此，我们的Hexo博客就已经正式在云上托管了☁️，开罐啤酒庆祝吧，Hooray🍺 

我们不仅完成了基础目标：快速地部署，并可以通过HTTPS域名访问，还通过添加devops服务与CDN服务，让我们的开发与访问速度都提升了🚀

其实能折腾的东西还有很多，像我还做了Hexo网站主题定制、自定义加速域名、强制HTTPS转换、SEO优化、ICP+公安备案等，这些操作留给你们去探索吧，我只是那个把你们带上云端的男人😆 

今天就写到这了，还有更多有趣的云原生玩法，一边实践再一遍分享吧🔥
