# 单位
# 像素（px）

# 百分数（%）

# em
em 作为 font-size 的单位时，其代表父元素的字体大小，em 作为其他属性单位时，代表自身字体大小。

# rem
rem 作用于非根元素时，相对于根元素字体大小；rem 作用于根元素字体大小时，相对于其出初始字体大小。

``` css
/* 作用于根元素，相对于原始大小（16px），所以html的font-size为32px*/
html {font-size: 2rem;}

/* 作用于非根元素，相对于根元素字体大小，所以为64px */
p {font-size: 2rem;}
```

# vw & vh