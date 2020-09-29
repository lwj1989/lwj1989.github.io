---
title: 什么是 Opcache，如何使用 Opcache
date: 2020-09-29 11:27:19
tags: php
---

### Opcode 是啥？

我们先看一下 **PHP** 的执行过程：

1. PHP 初始化执行环节，启动 Zend 引擎，加载注册的扩展模块。
2. 初始化后读取 PHP 脚本文件，Zend 引擎对 PHP 文件进行词法分析，语法分析，生成语法树。
3. Zend 引擎编译语法树，生成 **Opcode**。
4. Zend 引擎执行 **Opcode**，返回执行结果。

在 PHP-FPM 模式下，**步骤 1** 在启动时执行一次，后续的请求中不再执行；**步骤 2 3 4**每次请求都需要执行一遍。

2 和 3 生成的语法树和 Opcode ，同一个PHP 脚本每次运行的结果都是一样的。

而 **OPcache** 就是用来缓存 Opcode 的。

### OPcache

OPCache 是 Zend 官方出品的，开放自由的 Opcode 缓存扩展，还具有代码优化功能，省去每次加载和解析 PHP 脚本的开销。

OPcache 通过将 PHP 脚本预编译的字节码存储到共享内存中来提升 PHP 的性能， 存储预编译字节码的好处就是 省去了每次加载和解析 PHP 脚本的开销。

> PHP5.5.0以后的版本已经默认包含了 OPCache 扩展。

OPCache 缓存的机制主要是：**将 PHP 编译产生的字节码以及数据缓存到共享内存中，在每次请求，从缓存中直接读取编译后的 opcode，进行执行。**

#### OPcache 的更新策略

是缓存，都有过期时间。

OPCache 的更新策略非常简单，到期数据置为 Wasted，达到设定值，清空缓存，重建缓存。

因为在高流量的场景下，重建缓存是一件非常耗费资源的事情，所以建议：**不要设置 OPcache 的过期时间**。

每次发布新代码时，都会出现反复新建缓存的情况。如何避免呢？

- **不要在高峰期发布代码**，这是任何情况下都要遵守的规则
- 代码预热，比如使用脚本批量调 PHP 访问 URL，或者使用 OPCache 暴露的 API 如 `opcache_compile_file()` 进行编译缓存

#### Opcache 的安装

因为PHP5.5.0 以后的版本都已经默认安装了 OPCache，但是默认是没有开启的，需要手动开启。

**开发方法：**编辑 `php.ini`

```ini
;开启扩展
zend_extension=opcache.so
[opcache]
;允许在 web 环境使用
opcache.enable=1	
;允许在 cli 环境使用
opcache.enable_cli=0
```

重启 PHP-FPM 和 Nginx。

```bash
service php7.2-fpm restart
service nginx restart
```

#### 官网推荐的 `php.ini`的 OPCache 配置

```ini
;允许在 web 环境使用
opcache.enable=1
;允许在 cli 环境使用
opcache.enable_cli=1
;OPcache 的共享内存大小，以兆字节为单位。
opcache.memory_consumption=128
;用来存储预留字符串的内存大小，以兆字节为单位
opcache.interned_strings_buffer=8	
;OPcache 哈希表中可存储的脚本文件数量上限
opcache.max_accelerated_files=4000 
;检查脚本时间戳是否有更新的周期，以秒为单位。 设置为 0 会导致针对每个请求， OPcache 都会检查脚本更新。如果 opcache.validate_timestamps 配置指令设置为禁用，那么此设置项将会被忽略。
opcache.revalidate_freq=60
```

[其他参数查看官网文档](https://www.php.net/manual/zh/opcache.configuration.php)

### 参考

1. [PHP 官方 OPcache 扩展文档](https://www.php.net/manual/zh/book.opcache.php)

2. [Opcode 是啥以及如何使用好 Opcache](https://www.zybuluo.com/phper/note/1016714)

3. [PHP OPcache工作原理](https://zhuanlan.zhihu.com/p/75869838)

