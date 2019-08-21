---

title: Hexo 搭建配置
tags: 
    -Hexo
categories: Hexo折腾
description: Hexo 搭建配置
cover: https://i.loli.net/2019/08/17/nXozYKrgFCO5eqi.png
abbrlink: https://blog.qust.cc/archives/50370.html?tdsourcetag=s_pcqq_aiomsg
date: 2019-07-11 12:25:58
top_img: 
---
> 前言：本文记录博客搭建初期遇到的所有的问题，以及增添的功能、做的改动。

<!-- toc -->
<!-- more -->

# 1.中文不生效

### 文件说明：
站点配置文件：博客根目录下的`_config.yml`
主题配置文件：`themes/next`中的`_config.yml`

### 修复方法
官方文档要求将language项的值设置为zh-Hans
使用 next 主题时，检查`theme/next/languages/`目录下是否有`zh-Hans.yml`文件
一般是有`zh-CN.yml`的，将其改为`zh-Hans.yml`即可

# 2.生成文章目录插件
这个插件可以自动生成目录[https://github.com/bubkoo/hexo-toc](https://github.com/bubkoo/hexo-toc)

### 安装

在站点根目录下执行`npm install hexo-toc --save`

### 配置
在博客根目录下的`_config.yml`中如下配置：
```yaml
toc:
  maxDepth: 3
```

> `maxDepth` 表示目录深度为3，即最多生成三级目录。

### 使用

在Markdown中需要显示文章目录的地方添加 `<!-- ttoc -->`

为防止系统误识别，使用时请将`ttoc`改为`toc`

# 3.本地搜索插件

### 安装

在站点根目录下执行`npm install hexo-generator-searchdb --save`

### 编辑`站点`配置文件

新增以下内容

```yaml
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

### 编辑`主题`配置文件

修改以下内容

```yaml
# Local search
local_search:
  enable: true
```

# 4.头像设置

### 添加头像

编辑主题的 `_config.yml`，新增字段 `avatar`， 值设置成头像的链接地址。

其中，头像的链接地址可以是：

1.完整的互联网 URL

2.站点内的地址，例如：

* `/uploads/avatar.jpg`需要将你的头像图片放置在`站点`的 `source/uploads/`（可能需要新建uploads目录）
* `/images/avatar.jpg`需要将你的头像图片放置在`主题`的 `source/images/` 目录下。

### 圆形、旋转特效
修改文件`/themes/next/source/css/_common/components/sidebar/sidebar-author.styl`

```css
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  border: $site-author-image-border-width solid $site-author-image-border-color;
  /* 头像圆形样式 */
  /* start */
  border-radius: 50%
  webkit-transition: 1.4s all;
  moz-transition: 1.4s all;
  ms-transition: 1.4s all;
  transition: 1.4s all;
  /* end */
}
.site-author-name {
  margin: $site-author-name-margin;
  text-align: $site-author-name-align;
  color: $site-author-name-color;
  font-weight: $site-author-name-weight;
}
.site-description {
  margin-top: $site-description-margin-top;
  text-align: $site-description-align;
  font-size: $site-description-font-size;
  color: $site-description-color;
}
/* 头像旋转事件 */
/* start */
.site-author-image:hover {
  background-color: #55DAE1;
  webkit-transform: rotate(360deg) scale(1.1);
  moz-transform: rotate(360deg) scale(1.1);
  ms-transform: rotate(360deg) scale(1.1);
  transform: rotate(360deg) scale(1.1);
}
/* end */
```

# 5.Valine评论系统

### 注册Leancloud

[https://leancloud.cn/](https://leancloud.cn/)

注册->创建应用

 **进入应用->设置->应用key **获取你的appid 和 appkey

打开`主题配置文件` 搜索 **valine**，填入appid 和 appkey

```yaml
valine:
  enable: true # When enable is set to be true, leancloud_visitors is recommended to be closed for the re-initialization problem within different leancloud adk version
  appid: # Your leancloud application appid
  appkey: # Your leancloud application appkey
  notify: false # Mail notifier. See: https://github.com/xCss/Valine/wiki
  verify: false # Verification code
  placeholder: Just go go # Comment box placeholder
  avatar: mm # Gravatar style
  guest_info: nick,mail,link # Custom comment header
  pageSize: 10 # Pagination size
  language: # Language, available values: en, zh-cn
  visitor: false # leancloud-counter-security is not supported for now. When visitor is set to be true, appid and appkey are recommended to be the same as leancloud_visitors' for counter compatibility. Article reading statistic https://valine.js.org/visitor.html
  comment_count: true # If false, comment count will only be displayed in post page, not in home page
```

# 6.使用hexo-abbrlink为每个帖子创建一个唯一的链接

### 原理

对`标题`+`时间`进行`md5`然后再转`base64`，保存在`front-matter`中。

### 安装

```bash
npm install hexo-abbrlink --save
```

### 使用

打开`config.yml`，修改`permalink`中类似这样

```yaml
permalink: posts/archives/:abbrlink.html/
```

样例

```
https://doc.qust.cc/archives/50370.html
```



其中`:abbrlink`代表连接地址。

[hexo-abbrlink]: https://github.com/rozbo/hexo-abbrlink



# 7.发布blog 到腾讯云 cos

安装`hexo-deployer-cos`插件

```bash
npm install hexo-deployer-cos --save
```

打开博客根目录的`_config.yml`配置文件，将原来的`deploy`替换为下面的内容：

```yaml
deploy: 
  type: cos
  appId: yourAPPID
  secretId: yourSecretId
  secretKey: yourSecretKey
  bucket: yourBucketName-yourAPPID
  region: yourRegion
```

部署

```bash
hexo g -d
```

# 8.阅读全文跳转设置

修改`主题文件`的设置，，，默认`scroll_to_more`为 `true`，在点击阅读全文后，会跳转到`<!-- more -->`之下的部分，设置为`false`即可跳转到文章开始

```yaml
scroll_to_more: false
```

# 9.关闭底部信息

修改`主题配置`文件，找到`footer`项，修改子项`powered`和`theme`，将 `enable` 改为` false`

```yaml
powered:
  # Hexo link (Powered by Hexo).
  enable: false
  # Version info of Hexo after Hexo link (vX.X.X).
  version: true
theme:
  # Theme & scheme info link (Theme - NexT.scheme).
  enable: false
  # Version info of NexT after scheme info (vX.X.X).
  version: true
```
> 本文转自Garry's Blog：[https://blog.qust.cc/archives/50370.html](https://blog.qust.cc/archives/50370.html)
