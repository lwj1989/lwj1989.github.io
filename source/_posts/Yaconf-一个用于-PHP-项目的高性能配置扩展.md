---
layout: post
title: Yaconf 一个用于 PHP 项目的高性能配置扩展
date: 2020-09-29 19:34:43
tags: 
- yaconf
- php
---

[Yaconf 仓库](https://github.com/laruence/yaconf)

在开发 PHP 的时候，如果需要高性能的配置文件管理扩展，可以使用 `yaconf`这个扩展。

使用方法：

### 1. 安装 yaconf 扩展

```bash
pecl install yaconf
```

### 2. 怎么添加 `yaconf` 配置文件

- yaconf 的配置文件是一个`.ini`结尾的文件，在` yaconf.directory`中配置。

- 这个配置文件一般不和项目放在一起。

- 在 PHP 启动的时候，处理所有要处理的配置，然后这些配置信息就常驻内存了，随着 PHP 的生命周期存亡，避免每次请求的时候解析配置文件。

比如我把它和 **FPM** 的 `php.ini` 文件放在一起，`php-fpm.conf`也在这个位置。

```
//在 fpm 下创建了一个 yaconf 目录，然后把具体的.ini 文件放到这个文件夹下。
/etc/php/7.2/fpm/yaconf
//比如我有一个 foo.ini 在这个下面就是
/etc/php/7.2/fpm/yaconf/foo.ini
```

### 3. yaconf 怎么启用

我们需要在 `php.ini` 文件内启用这个扩展，在文件结尾添加下面的文本。

```ini
[Yaconf]
extension=yaconf.so
yaconf.directory=/etc/php/7.2/fpm/yaconf
yaconf.check_delay=100
```

- `yaconf.directory`：配置 yaconf 的文件放在哪里
- `yaconf.check_delay`：多久 (秒) 检测一次文件变动，如果是 0 就是不检测，也就是说如果是 0 的时候，文件变更只能通过重启 PHP 重新加载

### 4. 项目中使用 yaconf 配置

```php
Yaconf::get("foo.name"); //获取配置信息，比如我们需要上面 foo.ini 文件中配置的 name 参数，foo 就是文件名，name 就是参数名

Yaconf::has(string $name)；//检查某个配置文件是否存在，返回 boolean
```

