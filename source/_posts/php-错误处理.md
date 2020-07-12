---
title: php 错误处理
date: 2020-07-12 22:12:44
tags: PHP
---

# 错误处理

## 配置文件抑制错误

生产坏境需要抑制错误，测试环境需要放开错误

php.ini

```php
display_errors = On
```

改为

```php
display_errors = Off
```

php代码修改php.ini

```php
ini_set('display_errors') = 'Off';
```

获取php.ini

```php
ini_get('display_errors');
```

另一种方式

```php
error_reporting(0); //屏蔽错误

error_reporting(E_ALL);  //显示错误
```

错误日志路径

```php
error_log = 'wamp64/logs/php_error.log';
```