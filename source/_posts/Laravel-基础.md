---
title: Laravel 基础
date: 2020-07-12 22:16:15
tags: Laravel
---

# Laravel

## 路由

- 文件位置

  routes/web.php

- 基本路由

  ```php
  //get路由
  Route::get('basic1', function(){
      return 'basic1';
  });
  
  //post路由
  Route::post('basic2', function(){
      return 'basic2';
  });
  ```

  <!-- more -->

- 多请求路由

  ```php
  //指定请求
  Route::match(['get','post'], 'multy1', function(){
      return 'multy1';
  });
  
  //所有请求
  Route::any('multy2', function(){
      return 'multy2';
  });
  ```

- 路由参数

  ```php
  //地址栏输入user/sean 页面输出sean
  Route::get('user/{name}', function($name){
      return $name;
  });
  
  //参数默认值
  Route::get('user/{name?}', function($name = 'sean'){
      return $name;
  });
  
  //正则限制参数格式
  Route::get('user/{name?}', function($name = 'sean'){
      return $name;
  })->where('name', '[A-Za-z]+');
  ```

- 路由别名

  ```php
  Route::get('user/member', ['as' => 'center', function(){
      return route('center');
  }]);
  ```

- 路由群组

  ```php
  Route::group(['prefix' => 'member'], function(){
      //地址栏 member/user/id/1
      Route::get('user/id/{id}', function($id){
          return $id;
      });
  });
  ```

- 路由输出视图

  ```php
  Route::get('/', function(){
      return view('welcome');
  });
  ```

## 控制器

- 新建控制器

  1. 文件位置：app/Http/Controllers/
  2. 命名规范：MemberController.php
  3. 类继承自：App\Http\Controllers\Controller

  ```php
  namespace App\Http\Controllers;
  
  class MemberController extends Controller
  {
      public function info()
      {
          return 'info';
      }
  }
  ```

- 控制器关联路由

  ```php
  //关联方式1
  Route::get('member/info', 'MemberController@info');
  //关联方式2
  Route::any('member/info', [
      'uses' => 'MemberController@info'
  ]);
  ```

- 关联后，路由的特性如何用

  ```php
  //route
  Route::any('member/info/{id}', [
      'uses' => 'MemberController@info'
  ]);
  
  //controller
  public function info($id)
  {
      return 'id='.$id;
  }
  ```

## 视图

- 新建视图

  1. 文件位置：resources/views
  2. 命名规则：info.blade.php
  3. 一个控制器对应一个同名文件夹：resources/views/member/info.blade.php

- 控制器和视图关联

  ```php
  //route
  Route::any('member/info', [
      'uses' => 'MemberController@info'
  ]);
  
  //controller
  public function info()
  {
      return view('info', [
          'name' => '孔维民'
      ]);
  }
  
  //view
  {{$name}}
  ```

## 模型

- 新建模型

  1. 文件位置：app/
  2. 数据库一张表对应一个模型
  3. 数据库表中添加 created_at 创建事件、updated_at 更新时间字段，可自动维护
  4. 类继承自：Illuminate\Database\Eloquent\Model
  5. 模型中方法使用静态方法

  ```php
  namespace App;
  
  use Illuminate\Database\Eloquent\Model;
  
  class User extends Model
  {
      public function getMember()
      {
          return 'sean';
      }
  }
  ```

## DB facade 操作数据库

- 数据库配置

  config/database.php

  /.env

- 命名空间

  ```php
  Illuminate\Support\Facades\DB
  ```

- 查询数据

  ```php
  //返回二维数组
  DB::select('select * from laravel_user where id=?', ['1']);
  ```

  **注：**问号占位符，第二个参数使用数组形式补充占位，这样可以防止sql注入

- 新增数据

  ```php
  //返回新增是否成功
  DB::insert('insert into laravel_user (name) values (?)', ['kwm'])
  ```

- 修改数据

  ```php
  //返回受影响行数
  DB::update('update laravel_user set age= ? where id= ?', [18, 5])
  ```

- 删除数据

  ```php
  //返回受影响行数
  DB::delete('delete from laravel_user where id = ?', [5]);
  ```

## 查询构造器操作数据库

- 命名空间

  ```php
  Illuminate\Support\Facades\DB
  ```

- 查询数据

  1. get()

     ```php
     //获取全部数据，返回对象
     $user = DB::table('user')->get();
     //将数据转为二维索引数组
     //每个一维数组中的值为对象
     collect($user)->toArray();
     //将数据转为json
     collect($user)->toJson();
     ```

  2. first()

     ```php
     //获取结果集的第一条数据，通常配合where或orderBy使用
     DB::table('user')->first();
     //将数据转为一维关联数组
     collect($user)->toArray();
     ```

  3. where()

     ```php
     //获取id为1的数据
     DB::table('user')->where('id', 1)->get();
     //获取id大于1的数据
     DB::table('user')->where('id', '>', 1)->get();
     //获取id不等于1，age不等于18的数据
     //字符串占位符的方式要用whereRaw
     DB::table('user')->whereRaw('id != ? and age != ?', [1, 18])->get()
     ```

  4. pluck()

     ```php
     //获取表中所有的name字段的值
     $user = DB::table('user')->pluck('name');
     //获取表中所有的name字段的值，并把id设置为数组的键
     $user = DB::table('user')->pluck('name', 'id');
     //将数据转为一维关联数组
     collect($user)->toArray();
     ```

  5. select()

     ```php
     //获取id，name，age三个字段表中所有的值
     DB::table('user')->select('id', 'name', 'age')->get();
     ```

  6. chunk()

     ```php
     //将数据分段处理，每次处理2条
     DB::table('user')->chunk(2, function ($user) {
         var_dump($user);
     });
     ```

- 新增数据

  ```php
  //返回是否成功
  DB::table('user')->insert(['name' => 'kwm']);
  //返回自增id
  DB::table('user')->insertGetId(['name' => 'kwm']);
  ```

  **注：**如果在配置文件中添加了表前缀，这里就不用写前缀了

- 修改数据

  ```php
  //返回受影响行数
  DB::table('user')->where('id', 3)->update(['age' => 20]);
  //自增1，返回受影响行数
  DB::table('user')->where('id', 3)->increment('age'); 
  //增加5
  DB::table('user')->where('id', 3)->increment('age', 3); 
  //自减1，返回受影响行数
  DB::table('user')->where('id', 3)->decrement('age'); 
  //减少5，并将name改为kongweimin
  DB::table('user')->where('id', 3)->decrement('age', 3, ['name' => 'kongweimin']);
  ```

- 删除数据

  ```php
  //返回受影响行数
  DB::table('user')->where('id', 7)->delete();
  //清空表，并将自增id置0
  DB::table('user')->truncate();
  ```

- 聚合函数

  1. count()

     ```php
     //返回数字，总行数
     DB::table('user')->count()
     ```

  2. max()

     ```php
     //返回数字，该列的最大值
     DB::table('user')->max('age');
     ```

  3. min()

     ```php
     //返回数字，该列的最小值
     DB::table('user')->min('age');
     ```

  4. avg()

     ```php
     //返回数字字符串，该列的平均值
     DB::table('user')->avg('age');
     ```

  5. sum()

     ```php
     //返回数字字符串，该列的和
     DB::table('user')->sum('age');
     ```

## Eloquent ORM操作数据库

**利用模型类操作数据库，查询构造器的所有方法在这里都可以使用**

```php
namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    //指定表名
    protected $table = 'user';
    
	//指定主键
    protected $primaryKey = 'id';
    
    //create()方法允许添加的字段
    protected $fillable = ['name', 'age'];
    
    //不需要自动维护created_at、updated_at
    protected $timestamps = false;
    
    //将created_at、updated_at字段的值改为时间戳
    //不写此方法，值为年份
    public function getDateFormat()
    {
        return time();
    }
    
    //获取created_at、updated_at字段时不做处理
    //不写此方法，会自动格式化
    public function asDateTime($value)
    {
        return $value;
    }
}
```

- 查询数据

  1. all()

     ```php
     //获取全部数据，返回对象
     $user = User::all();
     //将数据转为二维索引数组
     //每个一维数组中的值为数组
     collect($user)->toArray();
     ```

  2. find()

     ```php
     //根据主键获取数据，可直接使用toArray()将对象转为一维数组
     User::find(1)->toArray()
     ```

- 新增数据

  1. save()

     ```php
     //新建模型对象
     $user = new User();
     //数据库字段赋值
     $user->name = 'yjy';
     $user->age = 18;
     //保存该对象
     //返回bool
     $user->save();
     ```

  2. create()

     ```php
     //可添加多条
     //要在model中添加$fillable属性
     $user = User::create(
                 ['name' => 'yjy1', 'age' => 18]
             );
     ```

- 修改数据

  1. save()

     ```php
     //获取要修改的数据
     $user = User::find(11);
     //修改字段
     $user->name = '尹娇阳';
     //保存，返回bool
     $user->save();
     ```

  2. update()

     ```php
     //根据条件批量修改
     //返回受影响行数
     User::where('id', '>', 8)->update(['age' => 20])
     ```

- 删除数据

  1. delete()

     ```php
     //获取要删除的数据对象
     $user = User::find(11);
     //执行删除，返回bool
     $user->delete();
     
     //根据条件批量删除
     //返回受影响行数
     User::where('id', '>', 5)->delete();
     ```

  2. destroy()

     ```php
     //根据主键删除，参数可以为单个id或id数组
     //返回受影响行数
     User::destroy(12);
     User::destroy([13,14]);
     ```

## Blade模板引擎

- 模版继承

  1. 主模版

     模版地址：/resources/views/layouts/app.blade.php

     ```html
     <html>
     <head>
         <title>App Name - @yield('title')</title>
     </head>
     <body>
     @section('sidebar')
         侧边栏
     @show
     
     <div class="container">
         @yield('content', '默认主内容区域')
     </div>
     </body>
     </html>
     ```

  2. 子模版

     ```html
     <!-- 使用extends()方法继承主模版 -->
     @extends('layouts.app')
     
     @section('title', '页面标题')
     
     <!-- 加parent为追加主模版渲染 -->
     @section('sidebar')
         @parent
         <p>这是在侧边栏追加的内容</p>
     @endsection
     
     <!-- 覆盖主模版渲染 -->
     @section('content')
         <p>主要内容显示区域</p>
     @endsection
     ```

  **注：**yield是不可扩展的，子模版中继承了就显示，否则就显示默认，不能使用parent

- 模版基本语法

  ```html
  @section('content')
      <p>主要内容显示区域</p>
      <p>{{$name}}</p>
      <p>{{time()}}</p>
      <p>{{date('Y-m-d H:i:s', time())}}</p>
      <!-- isset()的短标签语法 -->
      <p>{{$name1 or 'default'}}</p>
      {{--模版注释，生成的html中不显示--}}
  @endsection
  ```

- include

  ```html
  @include('layouts.common', ['msg' => '信息'])
  ```

  **注：**第二个参数是像子模版中传数据

- if

  ```html
  @if($name == 'admin')
      <p>I am admin</p>
  @else
      <p>who am I?</p>
  @endif
  ```

- foreach

  ```html
  @foreach($user as $value)
      <p>{{$value->name}}</p>
  @endforeach
  ```

- forelse

  ```html
  @forelse($user as $value)
      <p>{{$value->name}}</p>
  @empty
      <p>null</p>
  @endforelse
  ```

  **注：**自动加一层判断，数组不为空走empty上方区间，为空走empty下方区间

- 页面跳转

  ```html
  {{-- 路由名称跳转 --}}
  <a href="{{url('member/url')}}">url()</a>
  {{-- 路由别名跳转 --}}
  <a href="{{route('asUrl')}}">route()</a>
  {{-- 控制器+方法名跳转 --}}
  <a href="{{action('MemberController@url')}}">action()</a>
  ```

## 玩转Controller

### Request

- 取值

  ```php
  public function request(Request $request)
  {
      //获取地址栏name参数
      echo $request->input('name');
  
      //获取地址栏name参数，若无该参数，则显示默认值
      echo $request->input('name', '默认值');
  
      //判断是否有这个参数
      if ($request->has('name')) {
          echo $request->input('name');
      } else {
          echo '无该参数';
      }
  
      //获取所有参数
      $res = $request->all();
      var_dump($res);
  }
  ```

- 判断请求类型

  ```php
  public function request(Request $request)
  {
      //获取请求类型
      echo $request->method();
  
      //判断当前请求是什么类型
      if ($request->isMethod('GET')) {
          echo 'is GET';
      } else {
          echo 'other';
      }
  
      //判断当前请求是不是ajax
      if ($request->ajax()) {
          echo 'is ajax';
      } else {
          echo 'other';
      }
  
      //判断当前请求路径
      if ($request->is('member/*')) {
          echo 'is member/*';
      } else {
          echo 'other';
      }
  
      //获取当前请求路径
      echo $request->url();
  }
  ```

## Session

Route 设置

```php
// 设置middleware中间件
Route::group(['middleware' => ['web']], function () {
    Route::any('session', ['uses' => 'MemberController@session']);
});
```

1. HTTP request session

   ```php
   //依赖注入 Request
   //存值
   $request->session()->put('key1', 'value1');
   //取值
   $request->session()->get('key1');
   ```

2. session 辅助函数

   ```php
   //存值
   session()->put('key2', 'value2');
   //取值
   session()->get('key2');
   ```

3. session 类

   ```php
   use Illuminate\Support\Facades\Session;
   ```

   ```php
   //存值
   Session::put('key3', 'value3');
   //取值
   Session::get('key3');
   //若有值，则取值，无值取default
   Session::get('key4', 'default');
   
   //存数组
   Session::push('key5', 1);
   Session::push('key5', 2);
   
   //获取不删除
   Session::get('key5');
   //获取并删除
   Session::pull('key5', 'default');
   
   //获取所有的session
   Session::all();
   
   //判断session是否存在
   Session::has('key1');
   
   //删除session
   Session::forget('key1');
   //清空session
   Session::flush();
   
   //暂存session（只能获取一次）
   Session::flash('k-f', 'v-f');
   ```

## Response

1. 视图

   ```php
   return view('welcome');
   ```

2. json

   ```php
   $data = [
       'errCode' => 1,
       'errMsg' => 'fatal'
   ];
   return response()->json($data);
   ```

3. 重定向

   ```php
   //重定向到指定路由
   //参数为路由名称
   return redirect('session2');
   
   //带有参数重定向
   return redirect('session2')->with('msg', 'success');
   //获取参数（与flash类似，只能获取一次）
   Session::get('msg');
   
   //重定向到控制器的方法
   return redirect()->action('MemberController@session');
   
   //重定向到路由的别名
   return redirect()->route('session');
   
   //重定向到上级来源页
   return redirect()->back();
   ```

## Middleware 中间件

**中间件作用**：Laravel中间件提供了一个方便的机制来过滤进入应用程序的http请求

情景：

> 现在有一个活动
>
> 当前日期未到活动开始日期，用户看到宣传页面（路由：member/activity0）
>
> 当前日期已到活动开始日期，用户看到活动页面（路由：member/activity1、member/activity2）

**定义一个中间件**

文件位置：/app/Http/Middleware/Activity.php

```php
namespace App\Http\Middleware;

use Closure;

class Activity
{
    //$request是一个请求
    //$next是继续执行$request的方法包
    public function handle($request, Closure $next)
    {
        //判断当前时间是否到达活动日期
        if (time() < strtotime('2018-7-12')) {
            //没有到达，跳转到宣传页面
            return redirect('member/activity0');
        }
		//已到达，继续执行
        return $next($request);
    }
}
```

**中间件在写好后需要注册才能使用**

注册文件位置：/app/Http/Kernel.php

```php
//在这个数组中添加一个键值
protected $routeMiddleware = [
    ...
    'activity' => \App\Http\Middleware\Activity::class,
];
```

**使用中间件**

```php
//配置路由的文件中使用中间件
Route::get('member/activity0', ['uses' => 'MemberController@activity0']);
//在需要中间件的路由中设置中间件 middleware
Route::group(['middleware' => ['activity']], function () {
    Route::get('member/activity1', ['uses' => 'MemberController@activity1']);
    Route::get('member/activity2', ['uses' => 'MemberController@activity2']);
});
```

**关于前置中间件和后置中间件**

区别方法：逻辑代码在请求前还是请求后

刚才写的中间件的代码就是前置中间件