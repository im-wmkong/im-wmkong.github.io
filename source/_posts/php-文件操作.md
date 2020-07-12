---
title: php 文件操作
date: 2020-07-12 22:13:34
tags: PHP
---

# 文件操作

### 读取和写入

#### 读取文件内容，输出到浏览器

```php
readfile(path);
```

#### 把文件内容取出来做成数组，内容行数为元素个数

```php
file(path);
```

<!-- more -->

#### 将整个文件读入一个字符串

```php
file_get_contents(path);
```

#### 将一个字符串写入文件

```php
file_put_contents(path, $str);
```

### 打开，写入/读取，关闭

#### 打开一个文件

```php
php$res = fopen(path);
```

#### 把内容写入文件

```php
fwrite($res, $str);
```

#### 移动文件指针

```php
fseek($res, $offset);
```

#### 读取文件

```php
fread($res, $length);
```

#### 关闭一个已打开的文件指针

```php
fclose($res);
```

#### 测试文件指针是否到了文件结束的位置

```php
feof($res);
```

#### 返回文件指针读/写的位置

```php
ftell($res);
```

#### 文件指针的位置倒回至开头

```php
rewind($res);
```

#### 打开方式

r 以只读方式打开 如果文件不存在报错
r+ 以读写方式打开 覆盖写 如果文件不存在报错

w 以只写方式打开 如果文件不存在 则创建
w+ 以读写方式打开 如果文件不存在 则创建

a 以只写方式打开 如果文件不存在 则创建 累加写入
a+ 以读写方式打开 如果文件不存在 则创建 累加写入

x 以只写方式打开 文件存在报错 如果文件不存在创建
x+ 以读写方式打开 文件存在报错 如果文件不存在创建

### 文件路径

#### 以数组形式返回文件路径的信息

```php
pathinfo(path);
```

#### 返回路径中的文件名部分

```php
basename(path);
```

#### 返回路径中的目录部分

```php
dirname(path);
```

#### 讲数组转换成为url地址的参数信息

```php
http_build_query($arr);
```

#### 解析URL，返回其组成部分

```php
parse_url($url);
```

#### 将字符串解析成多个变量

```php
parse_str($str, $output);
echo $output['arr'][0];
echo $output['arr'][1];
```

### 文件的判断

#### 判断文件是否存在

```php
file_exists($filename);
```

#### 判断是否是一个文件

```php
is_file($filename);
```

#### 判断是否是一个目录

```php
is_dir($filename);
```

#### 判断是否可写

```php
is_writable($filename);
```

#### 判断是否可读

```php
is_readable($filename);
```

#### 判断是否可执行

```php
is_executable($filename);
```

### 文件夹操作

#### 创建文件夹

```php
mkdir(path);
```

#### 删除文件夹

```php
rmdir(path);
```

#### 打开文件夹

```php
opendir(path);
```

#### 关闭文件夹

```php
closedir(path);
```

#### 读取文件夹

```php
readdir(path);
```

#### 删除文件

```php
unlink($filename);
```

#### 拷贝文件

```php
copy ($source, $dest);
```

#### 重命名一个文件或目录

```php
rename ($oldname,$newname)
```

### 文件时间和大小

#### 文件的创建时间

```php
filectime($filename);
```

#### 文件的修改时间

```php
filemtime($filename);
```

#### 文件的最后访问时间

```php
fileatime($filename);
```

**文件大小**

byte是最小单位

1024b = 1kb

1024kb = 1mb

1024mb = 1GB

1024GB = 1TB

#### 统计文件大小

```php
filesize($filename);
```

### 文件上传

#### 1、准备form表单

```php
<form method="post"  enctype = "multipart/form-data">
    
</form>
```

#### 2、判断错误号

```php
$file['error']
```

#### 3、判断文件是否超过了你指定的大小

```php
$file['size'] > $maxSize
```

#### 4、判断是否是你准许的mime类型

```php
in_array($file['type'], $allowMine)
```

#### 5、生成新的文件路径，或者是新的文件名

```php
$info = pathinfo($file['name']);
$subFix = $info['extension'];
in_array($subFix, $allowSub)
```

#### 6、判断是否是上传文件

```php
is_uploaded_file($file['tmp_name'])
```

#### 7、移动文件

```php
move_uploaded_file($file['tmp_name'], $path.$newName)
```

注：

要开启

```php
file_uploads = On;
```

限定你上传文件的大小

```php
upload_max_filesize = 1024;
```

上传的临时文件目录

```php
upload_tmp_dir = "D:/wamp64/tmp";
```

允许同时上传的个数

```php
max_file_uploads = 20;
```

### 文件下载

#### 1、告知你的浏览器文件类型

```php
header('Content-type:image/jpeg');
```

#### 2、告知附件

```php
header('Content-Disposition: attachment; filename="new.jpg"');
```

#### 3、告知大小

```php
header('Content-Length'.filesize('image.jpg'));
```

#### 4、输出内容

```php
readfile('image.jpg');
```