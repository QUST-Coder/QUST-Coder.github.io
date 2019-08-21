---

title: Hexo Next主题 使用LeanCloud统计文章阅读次数、添加热度排行页面
tags: 
    -Hexo
categories: Hexo折腾
description: Hexo折腾
cover: https://i.loli.net/2019/08/17/XAotnRqPkYDcvLG.png
abbrlink: https://blog.qust.cc/archives/48665.html
date: 2019-08-05 12:26:53
top_img: 
---

使用LeanCloud记录hexo文章的阅读量，next主题对LeanCloud进行了适配，只需简单的配置。

本文综合参考了多篇文章以及自己的部署经历。并且修复了安全漏洞。

<!-- more -->

# 1. 注册 LeanCloud

详细注册步骤略

进入之后点击存储，创建Class，名称必须为`Counter`。

ACL权限选择`无限制`，点击`创建Class`按钮。

记录相关`App ID`和`App Key`

# 2.配置

## 2.1 主题配置

修改`主题配置`文件，修改以下字段，每一项都需要修改

```yaml
# Show number of visitors to each article.
# You can visit https://leancloud.cn get AppID and AppKey.
leancloud_visitors:
  enable: true
  app_id: <App ID>
  app_key: <App Key>
  # Dependencies: https://github.com/theme-next/hexo-leancloud-counter-security
  # If you don't care about security in lc counter and just want to use it directly
  # (without hexo-leancloud-counter-security plugin), set the `security` to `false`.
  security: true
  betterPerformance: true
```

## 2.2 站点配置

打开`站点配置`文件，新增以下内容

### 2.2.1 配置安全访问



```Yaml
leancloud_counter_security:
  enable_sync: true
  app_id: <<your app id>>
  app_key: <<your app key>
  username: <<your username>>
  password: <<your password>>
```

前两项的 id 与 key 为网站提供。后两项username和 password 自己定义。

### 2.2.2 配置deploy

```yaml
deploy:
  - type: leancloud_counter_security_sync
```

注意：存在多项 deploy，需要在 type 前加上`-`

例如：

```yaml
deploy:
  - type: git
    ··············
  - type: cos
		··············
  - type: baidu_url_submitter
  - type: leancloud_counter_security_sync
```

# 3.安装

打开命令终端，切换到博客根目录。安装hexo-leancloud-counter-security插件

``` shell
npm install hexo-leancloud-counter-security --save
```

等待安装结束之后，注册一个用户

```shell
hexo lc-counter r username password
```

这里的用户名和密码与2.2.2中配置的一致



注：若出现异常，提示

```shell
Error: Cannot find module 'babel-runtime/regenerator'
```

可以运行

```shell
npm i babel-runtime --save
```

# 4.部署云引擎以保证访客数量不被随意篡改

依次点击左侧`云引擎` -> `部署` -> `在线编辑`：

![](https://file.qust.cc/images/2019-08-05-1.54.13.png)

点击创建函数

![](https://file.qust.cc/images/2019-08-05-2.05.59.png)

在弹出窗口选择`Hook`类型，在`选择 Hook`处选择`beforeUpdate`，在`选择 Class`处选择`Counter`类。粘贴下方代码后，点保存。

然后单击`创建按钮`右侧的`部署` -> `生产环境` -> `部署`

待显示`部署完成：1 个实例部署成功`即可

# 5.添加热度排行页面

###  5.1 新建页面

在博客目录下执行`hexo n page hot`新建一个hot页面，编辑其中的`index.md`文件，将其中的代码替换如下

```html
---
title: 文章热度排行
date: 2019-08-14 10:29:51
comments: false
---
<div id="hot"></div>
<script src="https://cdn1.lncld.net/static/js/av-core-mini-0.6.4.js"></script>
<script>AV.initialize("YOUR_APPID", "YOUR_APPKEY");</script>
<script type="text/javascript">
  var time=0
  var title=""
  var url=""
  var query = new AV.Query('Counter');
  query.notEqualTo('id',0);
  query.descending('time');
  query.limit(1000);
  query.find().then(function (todo) {
    for (var i=0;i<1000;i++){
      var result=todo[i].attributes;
      time=result.time;
      title=result.title;
      url=result.url;
      var content="<p>"+"<font color='#1C1C1C'>"+"【文章热度:"+time+"℃】"+"</font>"+"<a href='"+"YOUR_URL"+url+"'>"+title+"</a>"+"</p>";
      document.getElementById("hot").innerHTML+=content
    }
  }, function (error) {
    console.log("error");
  });
</script>
```

将其中的`YOUR_APPID`和`YOUR_APPKEY`替换为 learncloud 的 id 和 key

将`YOUR_URL`替换为你的博客地址，包含`协议`并以`/`结束，例如：`https://blog.qust.cc/`

###  5.2 配置主题侧边栏

打开`主题配置`文件，搜索menu，添加`hot: /hot/ || signal`

###  5.3 配置侧边栏中文

打开**hexo/theme/next/languages/zh-Hans.yml**，搜索menu，添加`hot: 热度排行`


> 本文转自Garry's Blog：[https://blog.qust.cc/archives/48665.html](https://blog.qust.cc/archives/48665.html)
