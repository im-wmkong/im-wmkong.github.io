---
title: php 时间函数
date: 2020-07-12 22:12:58
tags: PHP
---



## 时间函数

返回时间戳

```php
time();
```

格式化时间戳

```php
date('Y-m-d H:i:s', time());
```

根据日期返回时间戳

```php
mktime(12, 59, 59, 12, 31, 1997); //H,i,s,m,d,Y
```

验证一个日期

```php
checkdate(12, 31, 1997); //m,d,Y
```

返回日期，以数组形式

```php
getdate(time());
```

根据格式化后的日期字符串返回时间戳

```php
strtotime('2018-4-9 9:20:50');
```

根据格式化后的日期字符串返回时间数组

```php
date_parse("2006-12-12 10:00:00.5");
```

返回当前时间戳和微秒数

```php
microtime();
```