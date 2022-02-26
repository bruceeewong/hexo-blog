---
title: 在Hexo（Next主题）中添加友情链接
date: 2021-08-16 23:27:07
categories: hexo
---

在网上搜索了一下文章标题，找到一篇博客：[在 Next 主题中添加友情链接](https://www.mls-tech.info/hexo/hexo-set-friend-links/)，这里记录一下步骤，也方便后来者~

找到你的 hexo next 配置文件，我的 next 配置文件是根目录下的 `_config.next.yml`，搜索 `Blog rolls` 注释，配置下方的 Links 即可：

```yaml
# Blog rolls
links_settings:
  icon: fa fa-user-friends
  title: My friends
  # Available values: block | inline
  layout: block

# 这里添加友链~
links:
  Airing的小屋: https://me.ursb.me/
  Hedwig: https://hedwig.pub/
  Wall-E の Paradise: https://www.qirencloud.com/
```

效果如图：

![hexo-add-friend-link.png](https://file.bruski.wang/images/hexo-add-friend-link.png)

快去添加你的好友吧~
