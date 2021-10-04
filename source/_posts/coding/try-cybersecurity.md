---
title: CTF初体验
date: 2021-02-27 10:21:00
categories: coding
tags: [cyber-security]
---

今天第一次参加 CTF(Catch the flag)网络安全竞赛,体验了一把当年没复习就去考试的被爆打的感觉...

> 地址: https://cybertalents.com/competitions/mini-ctf-week-4/challenges

# 题目

## Forger

- Category: General Information
- Level: basic
- Points: 25

Powerful interactive packet manipulation program. It is able to forge or decode packets of a wide number of protocols

answers:

- wireshark(wrong)
- Scrapy

## Evil3val

- Category: Web Security
- Level: easy
- Points: 50

You dare to bypass?

根据题目名与提示语，可以知道这题是在谴责万恶的 eval 函数，如果你能绕过它设置的正则，就能让 eval 帮你做任何事情。
http://18.192.38.21:3333/

```php
<?php​$cmd = $_GET['cmd'];  // 接收query 'cmd',此时已经经过urldecode
​if(isset($cmd)){
  if(!preg_match("/echo|flag|system|php|cat|shell|\.| |\'|\`|\;|\(/i", $cmd)){
    eval($cmd); // cmd参数需通过正则才能执行eval
  }
}
else{
  highlight_file(__FILE__);
}​
// $flag = "/flag";
```

题目分析，我们传特定的 cmd 查询参数，需要绕过他制定的正则匹配，让 eval 执行读取文件并返回的 php 代码，猜想如下：

```
?cmd=
```

注意到正则表达式

```
"/echo|flag|system|php|cat|shell|\.| |\'|\`|\;|\(/i"
```

禁掉了左括号、分号、空格，就想到如何通过特殊转义，绕过这里的正则检测。

查阅 urlencode 表 W3School，得知

- `(` 对应 `%28`
- `)` 对应 `%29`
- `;` 对应 `%3B`
- 这里没有禁用 print，可以考虑

```
print%20xxx
```

查阅资料，得知 php 会对查询参数自动做 urldecode，那就构造 urlencode 后的参数
猜想：

```
?cmd=high_light%28FILE%29%3B
```

但是程序执行$\_GET 时，拿到的就是 decode 后的字符啊，此时做正则，还是通不过。
所以关键在于，如何写一条能让 eval 识别，且能绕过正则检测的 php 语句。
那就再研究下 eval 函数吧，怎么样的语句可以让它乖乖读取想要的文件。
Valid PHP code to be evaluated.

正则封死了我想到的函数执行...放弃了

## haiku_lover

- Category: Secure Coding
- Level: medium
- Points: 100

Can you fix this vulnerable code?

```python
from flask import Flask, request, render_template
from os import getcwd

app = Flask(**name**, template_folder='templates')​

@app.route('/', methods=['GET'])
def main():
  if request.args.get('haiku'):
    haiku = request.args.get('haiku')
    try: f = open(f'{getcwd()}/haikus/{haiku}', 'r')
      content = f.read()
      f.close()
      return render_template('index.html', haiku=content)
    except Exception as e:
      print(str(e))
      return render_template('index.html', err=str(e))
  else:
    return render_template('index.html')
```

这题主要是考 linux 读取路径的注入问题？

尝试了规避 haiku 中的 . 相对路径查找。但是不知道为啥提交的代码本地跑 OK，提交就连运行都报错...

## Kill Joy

- Category: Malware Reverse Engineering
- Level: medium
- Points: 100

https://hubchallenges.s3-eu-west-1.amazonaws.com/Reverse/98437398mc394yt93.rar

没学过 reverse engineering。。跳过

## Onehop

- Category: Machines
- Level: medium
- Points: 100

compromise the machine and get root.txt

```
your entry point on 3.127.39.160 on port 20022
username: cybertalents
pass: cybertalents
```

这题看起来是要去 ssh 登录机器拿到 root.txt 文件，执行命令：

```bash
ssh -p 20022 cybertalents@3.127.39.160pass: cybertalents
```

登录成功！看到 home 目录下有三个文件：

- e29eb131-6a6a-4a6e-8181-1b483e6f0206
- nmap
- suid3num.py
- 运行了 suid3num.py

好的，完全不知道他想干嘛。。。

# 总结

好的,看来没有学习基础就来做题,真的云里雾里,记录一下耻辱,然后去学习了...Cybertalents 官网有教程
