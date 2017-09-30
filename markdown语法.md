# markdown
## 超链接
Markdown中有两种方式，实现链接，分别为内联方式和引用方式。

### 内联方式
```
[飞舟博客](http://www.faychou.com)
```
显示效果：

[飞舟博客](http://www.faychou.com)

### 引用方式

``` bash
# 这里注意，实际写法是不包含以下的单引号的
[Google][1] and [Yahoo][2] and [MSN][3]

'[1]: http://google.com/        "Google" '

'[2]: http://search.yahoo.com/  "Yahoo Search" '

'[3]: http://search.msn.com/    "MSN Search"'
```

显示效果：

[Google][1] and [Yahoo][2] and [MSN][3]

[1]: http://google.com/        "Google" 
[2]: http://search.yahoo.com/  "Yahoo Search" 
[3]: http://search.msn.com/    "MSN Search"

## 脚注
这是一个链接到谷歌的[^脚注]。
[^脚注]: http://www.google.com
