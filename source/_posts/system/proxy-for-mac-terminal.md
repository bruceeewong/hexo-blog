---
title: mac命令行设置代理，别挡着我起飞
date: 2021-07-10 00:00:00
categories: [system, macOS]
tags: [macOS]
---

作为一个程序员，要是因为网络原因，不能享受全世界程序员的发展成果，那就真的太亏了=。=

比如 github 看到一个很棒的项目，激动地打下`git clone`，结果...

```
Cloning into 'awesome-project'...
fatal: unable to access 'https://github.com/god/awesome-project.git/': LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 60
```

由于网络连不通，又一位少年/少女失去了编程梦想，可惜！可惜！

本文就以作者实际探索，介绍 mac 设置命令行代理的 2 种方案：手动设置变量 与 使用`proxychains`工具 ，希望能帮到正在阅读本文的你，一起起飞 🚀

## ✅ 前置准备

> **首先，你的 mac 得有一个 socks 代理工具，以及可用的 socks 代理服务**

本文重点不是这里，but sadly，这里又是全流程的重点，得靠你自己探索了，少年。

作者的 mac 有 socks 代理工具，它自动运行着两种协议的代理端口：socks5 代理端口与配套的 http 代理端口，都可用作请求的代理转发：

```shell
socks5 127.0.0.1 1086
http 127.0.0.1 1087
```

如果你的 mac 也满足条件，那么继续往下看吧 🤓

## 🚀 方案一：直接设置终端代理环境变量

> 这是最简便的方案，无需安装任何工具

每个终端会话都可以设置 `$http_proxy` 与 `$https_proxy`环境变量，来控制终端的 http 请求代理。

首先保证当前终端会话没有设置代理：

![image.png](https://img-blog.csdnimg.cn/img_convert/8a5f018846777ec600bc4084e9fed221.png)

我们来试下拉取一个别人分享的 mysql 命令行 gist（即文本笔记 📒），保存为 mysql_gist 文件。

> 链接：https://gist.github.com/hofmannsven/9164408

```shell
curl -o mysql_gist https://gist.github.com/hofmannsven/9164408
```

![image.png](https://img-blog.csdnimg.cn/img_convert/044ea5bd1dc7fd5b4293f3b1dc106e73.png)

嗯，Connection refused，早已被拒绝习惯了 😢

擦干眼泪，我们接下来在命令行输入代理配置（=号左右不要有空格）：

```shell
export http_proxy=http://127.0.0.1:1087; export https_proxy=http://127.0.0.1:1087;
```

我们再次检查 http 环境变量：

![image.png](https://img-blog.csdnimg.cn/img_convert/4a18a1b13fa26e729384bbd036557013.png)

Yes，生效了，那再下载一遍试试看？

![image.png](https://img-blog.csdnimg.cn/img_convert/0f21f9d5b26fd6f85bb89434e57868b9.png)

芜湖！成功把 github gist 下的配置下载下来了！不再是绝望的 `Connection refused` 或者 `3kb/s` 了 🚀

### 别急，还没完

上边的设置只是针对当前会话，如果关掉当前终端再开一个，悲剧依旧会重演 💔

我们可以把配置命令作为`别名(alias)`，这里就叫 `proxy` 吧，写到`.bash_profile`里吧，方便以后取用：

```
alias proxy="export http_proxy=http://127.0.0.1:1087;export https_proxy=http://127.0.0.1:1087;"
```

那么以后在命令行，如果遇到下载不通的情况，可以在原始命令前加上 `proxy` 即可：

```
proxy curl -o mysql_gist https://gist.github.com/hofmannsven/9164408
```

![image.png](https://img-blog.csdnimg.cn/img_convert/1e8904b7fb239aec7570a07f38699ab7.png)

Nicely done!

> 不建议在 bash_profile 中写死 export http_proxy=xxx，一是显示声明总比隐式声明好，二是节省你代理服务的流量，我们大多数场景使用直连就行了。

### 取消代理

当 export 了之后，当前终端会话的 http 请求都会走代理，如果要取消，需要手动输入：

```
unset http_proxy https_proxy
```

这样的方式可行，但还不够方便，不能按需使用，比如我只想某一条命令的请求走代理，而不是全局都走，我们把这个问题留在下面的方案解决吧。

### 方案总结

优点：

1. **简单、无依赖**：只需设置环境变量`http_proxy`与`https_proxy`，即可为当前终端会话的 http 请求设置代理啦！

缺点：

1. **无法做到按需使用**：设置了代理后，当前会话之后的所有 http 请求都会走代理，如要取消需要手动 unset `http_proxy`与`https_proxy`

### 🚀 方案二：使用 proxychains 工具

> 参考文章：
>
> 📚 [Mac OSX 使用 proxychains-ng](https://medium.com/@xiaoqinglin2018/mac-osx-%E4%BD%BF%E7%94%A8proxychains-ng-91ba61472fdf)
>
> 📚 [故事：试图不关闭 SIP 在 macOS Sierra 上使用 proxychains-ng](https://www.tcdw.net/post/proxychains-with-sip/)

[proxychains](https://github.com/haad/proxychains) 是一个 UNIX 程序，可以帮助我们代理终端发出的请求，支持 http, https, socks4, socks5 的代理类型。

> ProxyChains is a UNIX program, that hooks network-related libc functions in dynamically linked programs via a preloaded DLL and redirects the connections through SOCKS4a/5 or HTTP proxies.

### 📦 安装

macOS 可以通过 `brew` 来安装，不过巨慢（一个怪圈：慢是因为没配好终端代理，而配置终端代理又要到外网下载工具 🤪

```
brew install proxychains-ng
```

还可以尝试源码安装，这里参考 [Mac OSX 使用 proxychains-ng](https://medium.com/@xiaoqinglin2018/mac-osx-%E4%BD%BF%E7%94%A8proxychains-ng-91ba61472fdf) 的安装步骤，我没试验过.

```
$ git clone https://github.com/rofl0r/proxychains-ng

$ cd proxychains-ng
$ ./configure --prefix=/usr --sysconfdir=/etc
$ make
$ make install
$ sudo make install-config # 安装proxychains.conf配置文件
```

> 注：mac 上 make install 会报错。因为 Mac 下用 Homebrew 安装的默认为/usr/local/etc/proxychains.conf

解决方法：

```
cd configure
vi config.mak
将：
bindir = /usr/bin
libdir = /usr/lib
修改为：
bindir=/usr/local/bin
libdir=/usr/local/lib
```

### 🔖 配置 proxychains

vim 打开 `/usr/local/etc/proxychains.conf` 配置文件：

在[ProxyList]下添加 socks5 代理 （115 行，vim 快捷跳转 `:115`回车 ）

```
# 代理端口一定要和shadowsocks中的保持一致
# 如果有不明白的可以查看93～110
[ProxyList]
socks5 127.0.0.1 1086   # 配socks5
# http 127.0.0.1 1087   # 也可以配为http
```

> 具体代理类型配了 socks5 和 http 有什么性能上的不同，我不清楚，有知道的大神可以在评论科普一下

### 🎼 使用前的小插曲

> 如果不是 macOS 10.11 或更新，则跳过本节 🤪

[Mac OSX 使用 proxychains-ng](https://medium.com/@xiaoqinglin2018/mac-osx-%E4%BD%BF%E7%94%A8proxychains-ng-91ba61472fdf) 提到:

> macOS 10.11 后下由于开启了 SIP（System Integrity Protection） 会导致命令行下 proxychains-ng 代理的模式失效，如果使用 proxychains-ng 这种简单的方法，就需要先关闭 SIP。

想想苹果设置这个策略肯定是有安全考虑，有没有更优雅地解决方案，网上冲浪找到这篇文章 [故事：试图不关闭 SIP 在 macOS Sierra 上使用 proxychains-ng](https://www.tcdw.net/post/proxychains-with-sip/) ，他提到 SIP 策略的细节：

> 根据苹果的 官方说明，以下路径受到保护：
>
> - /System
> - /usr (不包含 /usr/local)
> - /bin
> - /sbin
> - Apps that are pre-installed with OS X

于是解决方案是，将工具如 `curl`， `ssh` 的二进制文件拷贝到非受保护路径：

我们在当前用户目录下创建自定义目录：

```
mkdir -p ~/local/bin
```

在`~/.bash_profile`中加入 PATH：

```
export PATH=/Users/xxx/local/bin:$PATH
```

接着拷贝要用的工具即可

```
cp $(which ssh) ~/local/bin/ssh  # 拷贝 ssh
cp $(which curl) ~/local/bin/curl # 拷贝 curl
```

### 🚀 使用 proxychains

使用方法巨简单，在原始命令前加上 `proxychains4` 即可：

```
proxychains4 curl -o mysql_gist https://gist.github.com/hofmannsven/9164408
```

![image.png](https://img-blog.csdnimg.cn/img_convert/43a86cec0a622aad46947b3ed033d752.png)

成功，并且不污染全局代理配置，舒服。

同样我们为 proxychains 配置 alias，就可以简便地使用了！

```
# ~/.bash_profile

alias pc='proxychains4'
```

**方案总结**

优点：

1. 使用简便，直接在原始命令前加 `proxychains4` 前缀即可
2. 支持多种代理的类型
3. 按需使用，不污染全局代理配置

缺点：

1. 安装可能受阻，配置相对麻烦
2. macOS 10.11 之后的版本需要用小聪明绕考 SIP 策略。

## 🍳 总结

两种方案都是作者亲测可行，如果有疑问可以下方留言，或者 📧 邮件我 <bruskiwang@outlook.com>

已经没有什么能阻碍你了，享受全世界的开源代码吧 🚀
