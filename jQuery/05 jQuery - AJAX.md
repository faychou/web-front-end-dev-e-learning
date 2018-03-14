# jQuery AJAX
简单例子：

``` js
$.ajax({
  type : 'post',
  url : 'http://localhost:8080/home',
  data : { age: 23 },
  success : function (data) {
    console.log(data);
  }
});
```

## API
### jQuery.get()
``` js
jQuery.get(...)
       所有参数：
              url: 待载入页面的URL地址
             data: 待发送 Key/value 参数。
          success: 载入成功时回调函数。
         dataType: 返回内容格式，xml, json,  script, text, html
```

### jQuery.post()
``` js
jQuery.post(...)
       所有参数：
              url: 待载入页面的URL地址
             data: 待发送 Key/value 参数
          success: 载入成功时回调函数
         dataType: 返回内容格式，xml, json,  script, text, html
```

### jQuery.ajax()
``` js
jQuery.ajax(...)
       部分参数：
　　　　　　　　type：请求方式，GET、POST（1.9.0之后用method）
              url：请求地址
　　　　　　　　data：要发送的数据
　　　　　　　　contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
            　async：是否异步


       beforeSend：发送请求前执行的函数(全局)
          success：成功之后执行的回调函数(全局)
            error：失败之后执行的回调函数(全局)
　　　　　　complete：完成之后执行的回调函数(全局)

          
         dataType：将服务器端返回的数据转换成指定类型
            "xml": 将服务器端返回的内容转换成xml格式
           "text": 将服务器端返回的内容转换成普通文本格式
           "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
         "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
           "json": 将服务器端返回的内容转换成相应的JavaScript对象
          "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数


　　　　　　headers：请求头
          timeout：设置请求超时时间（毫秒）
```

#### contentType
在 jquery 的 ajax 中， contentType 默认为：application/x-www-form-urlencoded，这种格式的特点就是，name/value 成为一组，每组之间用 & 联接，而 name与 value 则是使用 = 连接。如： www.faychou.cn/q?key=fdsa&lang=zh 这是 get , 而 post 请求则是使用请求体，参数不在 url 中，在请求体中的参数表现形式也是: key=fdsa&lang=zh 的形式。

键值对这样组织在一般的情况下是没有什么问题的，这里说的一般是，不带嵌套类型 JSON，也就是简单的 JSON ，形如这样：

``` json
{
  a: 1,
  b: 2,
  c: 3
}
```

但是在一些复杂的情况下就有问题了。 例如在 ajax 中你要传一个复杂的 json 对像，也就说是对象嵌数组，数组中包括对象：

``` json
{
  data: {
    a: [{
      x: 2
    }]
  }
}
```

如果是这种情况，application/x-www-form-urlencoded 是没有办法将复杂的 JSON 组织成键值对形式 ,该请求可以发送，但是服务端收到的数据为空，因为 ajax 没有办法知道怎样处理这个数据。

此时就需要自定义数据类型为 application/json 。这种类型是 text ， ajax 将会把复杂的 JSON 数据用 JSON.stringify 序列化，然后发送，服务器端接到后用 JSON.parse 进行还原就行了，这样就能处理复杂的对象了。

``` js
$.ajax({
  dataType: 'json',
  contentType: 'application/json',
  data: JSON.stringify({a: [{b:1, a:1}]})
});
```

#### dataType
预期服务器返回的数据类型。如果不指定，jQuery 将自动根据 HTTP 包 MIME 信息来智能判断，比如 XML MIME 类型就被识别为 XML。

> 注意：我们必须确保网页服务器报告的 MIME 类型与我们选择的 dataType 所匹配。比如 XML，服务器端就必须声明 text/xml 或者 application/xml 来获得一致的结果。

## jQuery 跨域

