---
title: jQuery 基础
date: 2020-07-12 22:15:54
tags: JavaScript
---

# jQuery

## 基础语法

```
$(selector).action();
```

- 美元符号定义 jQuery
- 选择符（selector）”查询”和”查找” HTML 元素
- jQuery 的 action() 执行对元素的操作

例如：

```
$(this).hide();
```

<!-- more -->

## 文档就绪事件

```
$(document).ready(function(){
   // 开始写 jQuery 代码...
});

$(function(){
   // 开始写 jQuery 代码...
});
```

## 选择器

1. 元素选择器

   ```
   $('p');
   ```

   选择所有的 p 标签

2. id选择器

   ```
   $('#test');
   ```

   选择 id 为 test 的标签

3. class选择器

   ```
   $('.test');
   ```

   选择 class 属性为 test 的标签

4. 属性选择器

   ```
   $("input [type='checkbox']");
   ```

   选择所有的 checkbox

5. 组合选择器

   ```
   $('ul li');
   ```

   选择 ul 下的所有 li 标签

## 事件

- 单击事件

  ```
  $('p').click(function(){
      
  });
  ```

- 双击事件

  ```
  $('p').dblclick(function(){
      
  });
  ```

- 鼠标移入事件

  ```
  $("#p").mouseenter(function(){
      
  });
  ```

- 鼠标移出事件

  ```
  $("#p").mouseleave(function(){
      
  });
  ```

- 获得焦点事件

  ```
  $("input").focus(function(){
    
  });
  ```

- 失去焦点事件

  ```
  $("input").blur(function(){
    
  });
  ```

- 事件委托

  ```
  $(document).on('click','p',function(){
      
  });
  ```

## 链式编程

chaining技术，允许我们在相同的元素上运行多条 jQuery 命令，一条接着另一条。这样的话，浏览器就不必多次查找相同的元素。

```
$("#p1").css("color","red").slideUp(2000).slideDown(2000);
```

这行代码可以使 “p1” 元素首先会变为红色，然后向上滑动，再然后向下滑动

## 捕获和设置

- text()

  ```
  <p id="test">这是段落中的文本。</p>
  <script>
  $(document).ready(function(){
      alert("text为: " + $("#test").text());
  });
  </script>
  <script>
      $(function(){
          $("#test").text('js动态赋值文本');
      });
  </script>
  ```

  text()方法没有参数时，为获取html中的文本。

  text()方法有一个参数时，为设置html的文本。

- html()

  ```
  <p id="test">这是段落中的文本。</p>
  <script>
  $(document).ready(function(){
      alert("html为: " + $("#test").html());
  });
  </script>
  <script>
      $(function(){
          $("#test").html('js动态赋值html');
      });
  </script>
  ```

  html()方法没有参数时，为获取html中的文本及子级标签。

  html()方法有一个参数时，为设置html的文本及子级标签。

- val()

  ```
  <input type="text" id="test" value="kongweimin">
  <script>
  $(document).ready(function(){
      alert("值为: " + $("#test").val());
  });
  </script>
  <script>
      $(function(){
          $("#test").val('js设置值');
      });
  </script>
  ```

  val()方法没有参数时，为获取input中的value。

  val()方法有一个参数时，为设置input中的value。

- attr()

  ```
  <a href="https://www.baidu.com" id="test">百度</a>
  <script>
      $(function(){
          alert($("#test").attr("href"));
      });
  </script>
  <script>
      $(function(){
          $("#test").attr("href", "https://github.com/");
      });
  </script>
  ```

  attr()方法有一个参数时，为获取标签的属性

  attr()方法有两个参数时，为设置标签的属性

  **注：**prop()方法和attr()方法，用法基本相同，含义基本相同，不同点为固定行间属性建议使用prop()方法，自定义行间属性建议使用attr()方法

## css

- addclass()

  ```
  $("div").addClass("important");
  ```

- removeclass()

  ```
  $("h1,h2,p").removeClass("important");
  ```

- css()

  ```
  $("p").css("background-color","yellow");
  $("p").css({"background-color":"yellow","font-size":"200%"});
  ```

## 祖先、后代、同胞

- parent()

  返回被选元素的直接父元素

- parents()

  返回被选元素的所有祖先元素，它一路向上直到文档的根元素 ()

- children()

  返回被选元素的所有直接子元素

- find()

  返回被选元素的后代元素，一路向下直到最后一个后代

  ```
  <div>
      <p>
          <span>span</span>
      </p>
  </div>
  <script>
      $(function(){
          $('div').find('span');
      });
  </script>
  ```

- siblings()

  返回被选元素的所有同胞元素

- next()

  返回被选元素的下一个同胞元素

- eq()

  返回被选元素中带有指定索引号的元素

  索引号从 0 开始，因此首个元素的索引号是 0 而不是 1

  ```html
  <p>第0个</p>
  <p>第1个</p>
  <p>第2个</p>
  <p>第3个</p>
  <script>
      $(function(){
          $("p").eq(1); //返回 <p>第1个</p>
      });
  </script>
  ```

## ajax

```
var form = $('form').serialize();
$.ajax({
    //后台地址
    url: '{{route("project.get")}}',
    //传输方式 get/post/put/patch/delete
    type: 'post',
    //传到后台的数据
    data: form,
    //返回的数据格式 json/xml/text
    dataType: 'json',
    //是否异步 true/false
    async: true
}).done(function (data) {
    //后台处理成功回调
    window.parent.location.reload();
}).fail(function (e) {
    //后台处理异常回调
    console.log(e.responseText);
    alert('系统繁忙，请稍后重试！');
}).always(function(){
    //无论成功异常都会回调
});
```

## ajax 上传文件

```html
<!DOCTYPE html>
<html>
<head>
	<title>上传文件</title>
	<meta charset="utf-8" />
</head>
<body>
	<form enctype="multipart/form-data">
		<input type="file" name="file">
		<input type="submit" name="submit" id="submit" value="提交">
	</form>
	<script type="text/javascript" src="jquery-3.3.1.min.js"></script>
	<script type="text/javascript">
		$(function () {
			$('#submit').click(function () {
				let files = $(':file').prop('files');
				let data = new FormData();
            	data.append('file', files[0]);
            	data.append('other', 1);

				$.ajax({
					url: 'proxy.php',
					type: 'post',
					data: data,
					dataType: "json",
					processData: false,
                	contentType: false,
				});

				return false;
			});
		});
	</script>
</body>
</html>
```

