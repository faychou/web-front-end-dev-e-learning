# CSS 变量
它是 CSS 的新特性，让你能够在样式表中使用变量的能力，并且无需任何配置。

``` css
/* 老办法 */
h1{
    font-size:30px;
}
navbar > a {
    font-size:30px;
}

/* 使用变量 */
:root {
  --base-font-size: 30px;
}
h1 {
  font-size: var(--base-font-size);
}
navbar > a {
  font-size: var(--base-font-size);
}
```

过仅改变 --base-font-size 的值来改变 app 中所有原生的字体大小。