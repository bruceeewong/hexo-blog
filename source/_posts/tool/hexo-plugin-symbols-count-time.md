---
title: 给Hexo加上阅读时长和字数统计吧
date: 2021-08-16 23:45:07
categories: Tool
tag: [Hexo]
---

读到博文[Hexo常用插件介绍 hexo-symbols-count-time](https://www.mls-tech.info/hexo/hexo-plugin-symbols-count-time/), 发现[hexo-symbols-count-time](https://github.com/next-theme/hexo-word-counter)这个好用的插件，能**自动统计每篇博客的字数**和**计算估计阅读时间** ，赶紧下载下来玩玩。


```shell
$ npm install hexo-word-counter
```

在 `_config.yml` 中配置

```yaml
symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
  exclude_codeblock: false
  awl: 2
  wpm: 300
  suffix: "mins."
```

其中关键参数解释：
- `awl` 平均字符长度
  - CN ≈ 2
  - EN ≈ 5
  - RU ≈ 6
- `wpm` 每秒阅读的单词数
  - Slow ≈ 200
  - Normal ≈ 275
  - Fast ≈ 350
- [更多参数 ](https://github.com/next-theme/hexo-word-counter#parameters)

> 中文的话推荐配置 `awl` = 2, `wpm` = 300

在你的Next主题配置文件 `_config.next.yml` 中配置：

```yaml
post_meta:
  item_text: true

symbols_count_time:
  separated_meta: true
  item_text_total: false
```

最后启动博客前记得清除以前的构建数据：

```shell
$ hexo clean
$ npm run server
```

效果如图:

![hexo-plugin-count-time](https://file.bruski.wang/images/hexo-plugin-count-time.png)

是不是很有用，继续鼓捣吧！