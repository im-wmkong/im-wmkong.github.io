---
title: Laravel Eloquent 提示和技巧
date: 2020-07-12 22:21:49
tags: Laravel
---

# Laravel Eloquent 提示和技巧

Eloquent ORM 看起来像一个简单的机制，但在幕后，有很多半隐藏的功能和不太知名的方法来实现更多。在本文中，我将向您展示一些技巧。

<!-- more -->

### 1. Increments and Decrements

文章阅读量增加 1：

```php
$article = Article::find($articleid);
$article->readcount++;
$article->save();
```

你可以这样做：

```php
$article = Article::find($article_id);
$article->increment('read_count');
```

也可以这些做：

```php
Article::find($article_id)->increment('read_count');
Article::find($article_id)->increment('read_count', 10);      // +10
Product::find($produce_id)->decrement('stock');               // -1
```

### 2. XorY methods

Eloquent有很多功能，结合了两种方法，比如“请做X，否则做Y”。

1. findOrFail() :

   ```php
   $user = User::findOrFail($id);
   ```

   等价于：

   ```php
   $user = User::find($id);
   if (!$user)  {
       abort (404); 
   }
   ```

2. firstOrCreate() :

   ```php
   $user = User::firstOrCreate(['email' => $email]);
   ```

   等价于：

   ```php
   $user = User::where('email', $email)->first();
   if (!$user) {
           User::create(['email' => $email]);
   }
   ```

### 3. 模型 boot() 方法

在Eloquent模型中有一个名为boot（）的方法，您可以在其中覆盖默认行为：

```php
class User extends Model
{
    public static function boot()
    {
        parent::boot();
        static::updating(function ($model) 
        {
            // do some logging
        });
    }
}
```

可能最常见的例子之一是在创建模型对象时设置一些字段值。假设你想在那一刻生成UUID字段。

```php
public static function boot()
{
    parent::boot();
    static::creating(function ($model) 
    {
        $model->uuid = (string)Uuid::generate();
    });
}
```

### 4. Relationship with conditions and ordering

这是定义关系的典型方法：

```php
public function users()
{
   retrun $this->hasMany('App\User');
}
```

但是你知道吗，此时我们已经可以添加 **where** 或 **orderBy** 了！
例如，如果您想要某种类型的用户（也是通过电子邮件订购）的特定关系，您可以这样做：

```php
public function approvedUsers()
{
   retrun $this->hasMany('App\User')->where('approved', 1)->orderBy('email');
}
```

### 5. 模型属性：timestamps, appends等。

Eloquent模型有一些“参数”，以该类的属性形式出现。最受欢迎的可能是这些：

```php
class User extends Model {
    protected $table = 'users';
    protected $fillable = ['email', 'password']; // which fields can be filled with User::create()
    protected $dates = ['created_at', 'deleted_at']; // which fields will be Carbon-ized
    protected $appends = ['field1', 'field2']; // additional values returned in JSON
}
```

但等等，还有更多：

```php
protected $primaryKey = 'uuid'; // it doesn't have to be "id"
public $incrementing = false; // and it doesn't even have to be auto-incrementing!
protected $perPage = 25; // Yes, you can override pagination count PER MODEL (default 15)
const CREATED_AT = 'created_at';
const UPDATED_AT = 'updated_at'; // Yes, even those names can be overridden
public $timestamps = false; // or even not used at all
```

更多请查看默认 [abstract Model class](https://github.com/laravel/framework/blob/5.6/src/Illuminate/Database/Eloquent/Model.php) 的代码，并查看所有使用的特征。

### 6. find()

大家都知道 **find()** 方法可以这样用：

```php
$user = User::find(1);
```

其实 **find()** 还可以传递一个数组作为参数：

```php
$users = User::find([1,2,3]);
```

### 7. whereX

有一种优雅的方式可以解决这个问题：

```php
$users = User::where('approved', 1)->get();
```

等价于：

```php
$users = User::whereApproved(1)->get();
```

### 8. Order by relationship

一个更复杂的“技巧”。如果您有论坛主题但想通过最新帖子订购，该怎么办？顶部有最新更新主题的论坛中非常常见的要求，对吧？

首先，描述关于该主题的最新帖子的单独关系：

```php
public function latestPost()
{
    return $this->hasOne(\App\Post::class)->latest();
}
```

然后，在我们的控制器中，我们可以这样做：

```php
$users = Topic::with('latestPost')->get()->sortByDesc('latestPost.created_at');
```

### 9. Eloquent::when() – no more if-else’s

我们中的许多人用 “ if-else ” 编写条件查询，如下所示：

```php
if (request('filter_by') == 'likes') {
    $query->where('likes', '>', request('likes_amount', 0));
}

if (request('filter_by') == 'date') {
    $query->orderBy('created_at', request('ordering_rule', 'desc'));
}
```

但有更好的方法 - 使用 when（）：

```php
$query = Author::query();

$query->when(request('filter_by') == 'likes', function ($q) {
    return $q->where('likes', '>', request('likes_amount', 0));
});

$query->when(request('filter_by') == 'date', function ($q) {
    return $q->orderBy('created_at', request('ordering_rule', 'desc'));
});
```

它可能不会感觉更短或更优雅，但最强大的是传递参数：

```php
$query = User::query();

$query->when(request('role', false), function ($q) use ($role) { 
    return $q->where('role_id', $role);
});

$authors = $query->get();
```

### 10. BelongsTo Default Models

假设你有Post属于Author，然后是Blade代码：

```php
{{ $post->author->name }}
```

但是如果作者被删除，或者由于某种原因没有设置呢？您将收到错误，例如“property of non-object”。
当然，您可以像这样阻止它：

```php
{{ $post->author->name ?? '' }}
```

但你可以在Eloquent关系层面上做到这一点：

```php
public function author()
{
    return $this->belongsTo('App\Author')->withDefault();
}
```

在此示例中，如果没有作者附加到帖子，则 author（）关系将返回空的 App \ Author 模型。
此外，我们可以将默认属性值分配给该默认模型。

```php
public function author()
{
    return $this->belongsTo('App\Author')->withDefault([
            'name' => 'Guest Author'
    ]);
}
```

### 11. 赋值函数排序

假设有这么一段代码：

```php
public function getFullNameAttribute()
{
    return $this->attributes['first_name'].' '.$this->attributes['last_name'];
}
```

如果你想按照 full_name 进行排序，下面这句代码将不起作用:

```php
$clients = Client::orderBy('full_name')->get();   // doesn't work
```

解决办法很简单，我们只需要在获取集合之后利用 sortBy 对集合进行排序即可：

```php
$clients = Client::get()->sortBy('full_name');   // works
```

### 12. 全局范围内默认排序

如果你希望所有用户总是按照 name 字段排序，你可以在全局范围内做一个声明，让我们回到上面已经提到的boot（）方法。

```php
protected static function boot()
{
    parent::boot();
    // order by name ASC
    static::addGlobalScope('order', function (Builder $builder) {
        $builder->orderBy('name', 'asc);
    });
}
```

### 13. 原始查询方法

有时候我们需要在Eloquent查询语句中添加原始查询

```php
// whereRaw
$orders = DB::table('orders')
        ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
        ->get();
// havingRaw
Product::groupBy('category_id')->havingRaw('COUNT(*) > 1')->get();
// orderByRaw
User::where('created_at', '>', '2018-11-11')
    ->orderByRaw('(updated_at - created_at) desc')
    ->get();
```

### 14. Replicate: 制作一行的副本

制作数据库条目副本的最佳方法:

```php
$task = Task::find(1);
$newTask = $task->replicate();
$newTask->save();
```

### 15. chunk() 方法批量处理大数据量

不完全与Eloquent相关，它更多关于Collection，但仍然很强大 - 处理更大的数据集，你可以将它们分成几块。
一般情况下数据量不太大的情况下会像下面这样遍历

```php
$users = User::all();
foreach($users as $user) {
    // ...
}
```

数据太大就能显示 chunk() 的神威了

```php
User::chunk(100, function ($users) {
    foreach($users as $user) {
        //...
    }
});
```

### 16. 命令行创建模型的同时，创建迁移文件和控制器

laravel创建模型的命令大家都很熟悉：

```shell
php artisan make:model Company
```

不过你应该了解另外几个很常用的参数：

```shell
php artisan make:model Company -m
php artisan make:model Company -mc
php artisan make:model Company -mcr
php artisan make:model Company -mcrf
```

-m 表示创建模型对应的迁移文件
-c 表示创建模型对应的控制器
-r 表示创建的控制器属于资源控制器
-f 表示创建模型对应的工厂文件

实际上上述几个情况，也可以通过 -a 来实现

```php
php artisan make:model Company -a
```

### 17. 保存数据的同时 覆盖 updated_at 的默认更新时间

其实 ->save() 方法是可以接受额外参数的，因此，我们可以告诉它“忽略”updated_at默认功能以填充当前时间戳。

```php
$product = Product::find(1);
$product->updated_at = '2018-11-11 11:11:11';
$product->save(['timestamps' => false]);
```

可以看到，我们用我们预先定义的版本覆盖默认的updated_at。

### 18. update() 方法的执行结果

你有没有想过这段代码究竟返回了什么？

```php
$result = $product->whereNull('category_id')->update(['category_id' => 1]);
```

更新是在数据库中执行的，但$ result会包含什么？
答案是受影响的行。因此，如果您需要检查受影响的行数，则无需再调用任何其他内容 - update（）方法将为您返回此数字。

### 19. 将and 或者 or转换为Eloquent查询

在你的查询中肯定会遇到 and 或者 or 的情况，就像这样：

```php
... where (gender = 'Male' and age > 18) or (gender = 'Female' and age >= 65)
```

那么怎么转换成Eloquent查询呢？先来看一个错误的例子：

```php
$q->where('gender', 'Male');
$q->where('age', '>', 18);
$q->orWhere('gender', 'Female');
$q->where('age', '>=', 65);
```

正确的方法有点复杂，使用闭包函数作为子查询：

```php
$q->where(function ($query) {
    $query->where('gender', 'Male')->where('age', '>', 18);
})->orWhere(function ($query) {
    $query->where('gender', 'Female')->orWhere('age', '>=', 65);
})
```

### 20. orWhere() 有多个参数的情况

通常情况下遇到这种查询：

```php
$q->where('a', 1);
$q->orWhere('b', 2);
$q->orWhere('c', 65);
```

这种情况下可以传递一个数组作为 orWhere() 的参数：

```php
$q->where('a', 1);
$q->orWhere(['b' => 2, 'c' => 65]);
```

原文：https://laravel-news.com/eloquent-tips-tricks