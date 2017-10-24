# HTML查漏补缺
## head
一张关于 head 标签的设置清单。

``` html
<!-- Set character encoding for the document -->
<meta charset="utf-8">

<!-- Instruct Internet Explorer to use its latest rendering engine -->
<meta http-equiv="x-ua-compatible" content="ie=edge">

<!-- Viewport for responsive web design -->
<meta name="viewport" content="width=device-width, initial-scale=1">

<!-- Meta Description.A meta description is provided, it is unique and doesn't possess more than 150 characters. -->
<meta name="description" content="Description of the page less than 150 characters">

<!-- Each favicon has been created and displays correctly. If you have only a favicon.ico, put it at the root of your site. Normally you won't need to use any markup. However, it's still good practice to link to it using the example below. Today, PNG format is recommended over .ico format (dimensions: 32x32px) -->
<!-- Standard favicon -->
<link rel="icon" type="image/x-icon" href="https://example.com/favicon.ico" />
<!-- Recommended favicon format -->
<link rel="icon" type="image/png" href="https://example.com/favicon.png" />

<!-- Apple Touch Icon.Create your Apple Icon file with at least 200x200px dimension to support all dimensions that you may need -->
<link rel="apple-touch-icon" href="/custom-icon.png">

<!-- Helps prevent duplicate content issues -->
<link rel="canonical" href="http://example.com/2017/09/a-new-article-to-red.html">
```

### Social meta
Facebook OG and Twitter Cards are, for any website, highly recommended. The other social media tags can be considered if you target a particular presence on those and want to ensure the display.

#### Facebook Open Graph
All Facebook Open Graph (OG) are tested and no one is missing or with a false information. Images need to be at least 600 x 315 pixels, 1200 x 630 pixels recommended.

``` html
<meta property="og:type" content="website">
<meta property="og:url" content="https://example.com/page.html">
<meta property="og:title" content="Content Title">
<meta property="og:image" content="https://example.com/image.jpg">
<meta property="og:description" content="Description Here">
<meta property="og:site_name" content="Site Name">
<meta property="og:locale" content="en_US">
```

#### Twitter Card
``` html
<meta name="twitter:card" content="summary">
<meta name="twitter:site" content="@site_account">
<meta name="twitter:creator" content="@individual_account">
<meta name="twitter:url" content="https://example.com/page.html">
<meta name="twitter:title" content="Content Title">
<meta name="twitter:description" content="Content description less than 200 characters">
<meta name="twitter:image" content="https://example.com/image.jpg">
```
