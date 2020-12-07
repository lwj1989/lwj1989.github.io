---
layout: post
title: 如何让ClashX在开发的时候访问本地hosts
date: 2020-12-07 17:53:29
tags: mac
---
在使用ClashX的时候，发现本地配置的hosts中的域名无法正常解析，最后确定是代理的问题，搜索后才确定是因为ClashX使用的是远程DNS解析，所以在远程的DNS中是无法找到我们本地配置的域名的。

那么如何绕过远程解析本地的hosts呢。

### 方式一（推荐）

使用配置文件中的`hosts`

```yaml
hosts:
    smtp.gmail.com: 74.125.20.109
    '*.test': 127.0.0.1	#配置后，所有.test域名就会直接从配置的hosts解析，不能走远程DNS了。
```

这里的配置域名如果泛解析不行就直接配置全。

### 方式二

关闭ClashX配置的远程DNS解析，打开配置文件，修改如下

```yaml
dns:
    enable: false
```

接下来就可以愉快地开发了。