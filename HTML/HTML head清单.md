# HTML-head清单
一些关于 head 标签的设置清单。

### 添加到主屏后的标题（IOS）

``` html
<meta name="apple-mobile-web-app-title" content="标题"> 
```

### 添加到主屏后的APP图标

``` html
<link href="short_cut_114x114.png" rel="apple-touch-icon-precomposed">
```

### 启用 WebApp 全屏模式（IOS）

当网站添加到主屏幕后再点击进行启动时，可隐藏地址栏（从浏览器跳转或输入链接进入并没有此效果）

``` html
<meta name="apple-mobile-web-app-capable" content="yes" /> 
<meta name="apple-touch-fullscreen" content="yes" /> 
```

### 语言
在 html 标签中通过 lang 属性进行明确的语言声明，将会有助于翻译。

``` html
<html lang="en"><!-- 英文 -->
<html lang="zh-Hans"><!-- 简体中文 -->
<html lang="zh-Hant"><!-- 繁体中文 -->
```

### 编码
为文档设置字符编码。

``` html
<meta charset="utf-8">
```

### 标题
``` html
<title>hello world</title>
```

### viewport
``` html
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
```

设置 viewport-fit=cover 以兼容 iPhone X 全面屏“刘海”的显示。

### IE
针对 IE 浏览器，通过设置 IE=edge 要求它使用最新的版本。 

``` html
<meta http-equiv="X-UA-Compatible" content="IE=edge">
```

### 使用 webkit 内核
对于国内各种双核浏览器，通过设置 renderer 头要求它们使用 webkit 内核。

``` html
<meta name="renderer" content="webkit"> <!-- 用在360中 -->
<meta name="force-rendering" content="webkit"> <!-- 其他 -->
```

### 禁止百度转码
通过百度手机打开网页时，百度可能会对你的网页进行转码，往你页面贴上它的广告，非常之恶心。不过我们可以通过这个 meta 标签来禁止它：

``` html
<meta http-equiv="Cache-Control" content="no-siteapp" />
```

对于百度搜索强制转码的流氓做法，通过 no-transform 禁止其自动转码。

``` html
<meta http-equiv="Cache-Control" content="no-transform">
```

### 禁止自动识别
移动端浏览器对类似电话、邮箱或地址的内容不要自动识别。

``` html
<meta name="format-detection" content="telephone=no,email=no,adress=no">
```

### 文档信息
``` html
<meta name="author" content="飞舟"><!-- 作者 -->
<meta name="description" content="飞舟的博客"><!-- 描述 -->
<meta name="keywords" content="飞舟,WEB,js"><!-- 关键词 -->
<meta name="generator" content="Hugo 0.32"><!-- 生成工具 -->
```

### Icons
虽说所有浏览器都还支持过时的 favicon.ico 格式，但在 HTML5 时代，我们应该使用更好的 PNG icon with sizes 方案。同时为了兼容老旧浏览器，我们可以将生成好的 favicon.ico 文件放在网站的根目录下面，通常浏览器会自动请求并加载它，并不需要额外通过 link 标签引入。

``` html
<meta name="theme-color" content="#db5945"> <!-- 主题颜色 -->
<meta name="application-name" content="飞舟的博客"> <!-- 应用名称 -->
<meta name="apple-mobile-web-app-capable" content="yes"> <!-- 隐藏状态栏 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black"> <!-- 状态栏颜色 -->
<meta name="apple-mobile-web-app-title" content="飞舟的博客"> <!-- iOS 下的应用名称 -->
<meta name="msapplication-tooltip" content="飞舟的博客"> <!-- Hover 的提示信息 -->
<meta name="msapplication-TileColor" content="#db5945"> <!-- 磁贴背景颜色 -->
<meta name="msapplication-TileImage" content="img/logo.png"> <!-- 磁贴图标 -->
<meta name="msapplication-config" content="browserconfig.xml"> <!-- 磁贴配置文件 -->
<link rel="icon" type="image/x-icon" href="img/favicon.ico">
<link rel="icon" type="image/png" href="img/logo-16.png" sizes="16x16"> <!-- Browser Favicon -->
<link rel="icon" type="image/png" href="img/logo-32.png" sizes="32x32"> <!-- Taskbar Shortcut -->
<link rel="icon" type="image/png" href="img/logo-96.png" sizes="96x96"> <!-- Desktop Shortcut -->
<link rel="icon" type="image/png" href="img/logo-128.png" sizes="128x128"> <!-- Chrome Web Store -->
<link rel="icon" type="image/png" href="img/logo-196.png" sizes="196x196"> <!-- Chrome for Android Home Screen -->
<link rel="icon" type="image/png" href="img/logo-228.png" sizes="228x228"> <!-- Opera Coast Icon -->
<link rel="apple-touch-icon" href="img/logo-120.png"> <!-- iPhone -->
<link rel="apple-touch-icon" href="img/logo-152.png" sizes="152x152"> <!-- iPad -->
<link rel="apple-touch-icon" href="img/logo-180.png" sizes="180x180"> <!-- iPhone Plus -->
<link rel="apple-touch-icon" href="img/logo-167.png" sizes="167x167"> <!-- iPad Pro -->
<link rel="mask-icon" href="img/logo.svg" color="green"> <!-- Safari Pinned Tab Icon -->
<!-- Helps prevent duplicate content issues -->
<link rel="canonical" href="http://example.com/a-new-article-to-red.html">
```

> 注意：apple-mobile-web-app-status-bar-style 值默认状态栏为白色，可设置为 black（黑色） 或者 black-translucent（灰色半透明）；mask-icon 引入的 svg 文件必须只有一个图层，并且 viewBox 属性应该为 “0 0 16 16”。

微软为了让 Metro UI 更好看，引入了 browserconfig.xml 文件，主要用于定制网站固定磁铁的图标和背景颜色：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<browserconfig>
   <msapplication>
     <tile>
        <square70x70logo src="/img/logo-70.png"/>
        <square150x150logo src="/img/logo-150.png"/>
        <wide310x150logo src="/img/logo-310x150.png"/>
        <square310x310logo src="/img/logo-310.png"/>
        <TileImage src="/img/logo-144.png"/>
        <TileColor>#db5945</TileColor>
     </tile>
   </msapplication>
</browserconfig>
```

### 索引
设置 robots 为 index,follow 指示搜索引擎爬虫该页面需要进入索引，并且页面内的所有链接都要继续跟踪。

``` html
<meta name="robots" content="index,follow">
```

### referrer
设置 referrer 为 origin-when-cross-origin 指示浏览器在进行跨域跳转时，其 referrer 值将统一为域名，而非具体的 URL 地址。

``` html
<meta name="referrer" content="origin-when-cross-origin">
```

### 预加载
浏览器预先请求当前页面所需要的资源。

``` html
<link rel="preload" href="/css/main.css" as="style">
<link rel="preload" href="/js/main.js" as="script">
```

### 预请求
浏览器预先请求用户即将浏览页面所需要的资源。

``` html
<link rel="prefetch" href="/img/css-sprite.png">
<link rel="prefetch" href="/fonts/icons.woff2">
```

### DNS 预读取
DNS 预读取是一项使浏览器主动去执行域名解析的功能，其范围包括当前文档内的所有链接，这能够减少用户点击链接时的延迟。

``` html
<meta http-equiv="x-dns-prefetch-control" content="on">
<link rel="dns-prefetch" href="//www.google-analytics.com">
<link rel="dns-prefetch" href="//fonts.googleapis.com">
```

### Base
设置网页的跟路径，但是不推荐使用 `<base>` 元素。

``` html
<!-- 一般 -->
<link href="/h5/app.css" rel="stylesheet">

<!-- 使用base后 -->
<base href="/h5/">
<link href="app.css" rel="stylesheet">
```

### Twitter Card
Twitter 卡片用于将网站内容以更加优雅漂亮的方式分享到 twitter.com 网站。

``` html
<meta name="twitter:card" content="summary">
<meta name="twitter:site" content="@site_account">
<meta name="twitter:creator" content="@individual_account">
<meta name="twitter:url" content="https://faychou.cn/page.html">
<meta name="twitter:title" content="此处为分享标题">
<meta name="twitter:description" content="此处为分享描述，控制在200字节内">
<meta name="twitter:image" content="https://faychou.cn/分享配图.jpg">
```

### Facebook Open Graph
Open Graph 是一套开放的网页标注协议，通过 meta 标签标注网页的类型，主要由 Facebook 推动，已经成为社交分享领域的事实标准。如果你希望明确告诉社交网络或搜索引擎你的网页类型，你应该添加这些 meta 标签。

``` html
<meta property="og:type" content="article">
<meta property="og:url" content="此处为分享的链接地址">
<meta property="og:title" content="此处为分享标题">
<meta property="og:image" content="此处为分享配图url">
<meta property="og:description" content="此处为分享描述">
<meta property="og:site_name" content="Site Name">
<meta property="og:locale" content="en_US">
```

### RSS
对于支持 RSS 订阅的页面，可针对 RSS 阅读器提供可订阅的源文件。

``` html
<link rel="alternative" href="index.xml" title="飞舟的博客" type="application/atom+xml">
```

### Polyfill
专门为老旧的 IE 浏览器引入 Polyfill 方案，为了让 IE6-IE8 浏览器能够使用 HTML5 标签和 Media Query 特性，我们需要引入 html5shiv 和 response.js 这两个库。

``` html
<!--[if lt IE 9]>
<script src="//cdn.bootcss.com/html5shiv/3.7.3/html5shiv.min.js"></script>
<script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
<![endif]-->
```

### 打印
对于可打印的页面（如文章页面），可提供针对打印机的样式表，使得网站用户可以将文章打印下来阅读。

``` html
<link rel="stylesheet" href="css/print.css" media="print">
```