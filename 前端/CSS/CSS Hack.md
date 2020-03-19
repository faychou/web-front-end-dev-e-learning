# CSS Hack
## IE 属性注释

## IE 条件注释
``` html
<!--[if lt IE 9]>
  <script src="html5.js"></script>
  <script src="respond.js"></script>
<![endif]-->
```

意思是：IE9 以下的浏览器，引入 html5.js 和 respond.js 文件。其他版本 IE 浏览器，则不引入。

这里的 `lt` 则是指小于，以下为其他类似的关键词：

* `lt` ：Less than 的简写，小于。

* `lte` ：Less than or equal to 的简写，小于或等于。

* `gt` ：Greater than 的简写，大于。

* `gte`：Greater than or equal to 的简写，大于或等于。

* `!`：不等于。

### 引导浏览器升级
``` html
<!--[if lt IE 9]>
  <script>
	location.href = "page/update-browser.html";
  </script>
<![endif]-->
```

``` html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge"/>
<title>update-browser</title>
</head>
<body>
  <div>
    <h2>推荐浏览器：</h2>
    <div>
      请点击这里下载
      <a href="https://www.baidu.com/s?wd=chrome" title="谷歌" target="_blank" >Google 浏览器</a>
      进行升级
    </div>
  </div>
</body>
</html>
```
