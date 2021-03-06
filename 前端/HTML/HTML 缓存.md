# 缓存
缓存的作用：重用已获取的资源，减少延迟与网络阻塞，进而减少显示某个资源所用的时间，借助 HTTP 缓存，Web 站点变得更具有响应性。

缓存分为以下几类：

![缓存分类](mdImgs/cache.png)

## HTTP header 缓存机制
分为强缓存和协商缓存。缓存的作用是提高客户端速度、节省网络流量、降低服务器压力。

### 强缓存
浏览器请求资源，如果 header 中的 Cache-Control 和 Expires 没有过期，直接使用强缓存（本地读取资源），不需要再向服务器请求资源。Cache-Control 的 max-age 优先级高于 Expires。

当缓存已经过期时，使用协商缓存。

### 协商缓存
浏览器请求的资源如果是过期的，那么会向服务器发送请求，header 中带有 Etag 字段。服务器再进行判断，如果 ETag 匹配，则返回给客户端 300 系列状态码，客户端继续使用本地缓存；否则，客户端会重新获取数据资源。

* 唯一标识方案: Etag(response 携带) & If-None-Match(request携带，上一次返回的 Etag): 服务器判断资源是否被修改，

* 最后一次修改时间: Last-Modified(response) & If-Modified-Since (request，上一次返回的 Last-Modified)
  * 如果一致，则直接返回 304 通知浏览器使用缓存
  * 如不一致，则服务端返回新的资源。

* Last-Modified 缺点：
  * 周期性修改，但内容未变时，会导致缓存失效
  * 最小粒度只到 s， s 以内的改动无法检测到

* Etag 的优先级高于 Last-Modified。

## cookie
一个用户的所有请求操作都应该属于同一个会话，而另一个用户的所有请求操作则应该属于另一个会话。

HTTP 协议是无状态的协议。一旦数据交换完毕，客户端与服务器端的连接就会关闭，再次交换数据需要建立新的连接。这就意味着服务器无法从连接上跟踪会话。

Cookie 实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用 response 向客户端浏览器颁发一个 Cookie。客户端浏览器会把 Cookie 保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该 Cookie 一同提交给服务器。服务器检查该 Cookie，以此来辨认用户状态。服务器还可以根据需要修改 Cookie 的内容。

cookie 的内容主要包括：名字，值，过期时间，路径和域。路径与域一起构成 cookie 的作用范围。

优点：对于传输部分少量不敏感数据，非常简明有效，可以手动设置失效期。

缺点：容量小（4K），不安全（cookie 被拦截，很可能暴露 session）；原生接口不够友好，需要自己封装；需要指定作用域，不可以跨域调用。

## Web Storage
优点：容量稍大一点（5M），localStorage可做持久化数据存储，支持事件通知机制，可以将数据更新的通知发送给监听者。

缺点：本地储存数据都容易被篡改，容易受到 XSS 攻击。

### 保存数据
``` js
localStorage.setItem( key, value );
sessionStorage.setItem( key, value );
```

### 读取数据
``` js
localStorage.getItem( key );
sessionStorage.getItem( key );
```

### 删除单个数据
``` js
localStorage.removeItem( key );
sessionStorage.removeItem( key );
```

### 删除全部数据
``` js
localStorage.clear( );
sessionStorage.clear( );
```


### 获取索引的key
``` js
localStorage.key( index );
sessionStorage.key( index );
```

### 监听 storage 事件
当页面中对 localStorage 或 sessionStorage 进行修改时，则会触发对应的处理函数:

``` js
window.addEventListener('storage',function(e) {
  console.log('key='+e.key+',oldValue='+e.oldValue+',newValue='+e.newValue);
})
```

* key : 键值。
* oldValue : 被修改前的值。
* newValue : 被修改后的值。
* url : 页面url。
* storageArea : 被修改的 storage 对象。

## indexDB
IndexDb 提供了一个结构化的、事务型的、高性能的 NoSQL 类型的浏览器端数据库，包含了一组同步/异步 API，无限容量，除非手动清除，否则一直存在。

### 打开数据库
``` js
// 打开数据库，参数一：数据库名称，参数二：数据库版本
var openRequest = indexedDB.open("test",1);

openRequest.onupgradeneeded = function(e) { // 第一次打开该数据库，或者数据库版本发生变化
  console.log("Upgrading...");
}
 
openRequest.onsuccess = function(e) { // 打开成功
  console.log("Success!");
  console.log(e.target.result);
}
 
openRequest.onerror = function(e) { // 打开失败
  console.log("Error");
  console.dir(e);
}

openRequest.onblocked = function(e) { // 上一次的数据库连接还未关闭
  //...
}
```

### 创建一个数据库存储对象
``` js
var objectStore = db.createObjectStore(dbName, { 
  keyPath: 'id',
  autoIncrement: true
});
```

### 向数据库添加数据
``` js
// 新建一个事务
var transaction = db.transaction('project', "readwrite");
// 打开存储对象
var objectStore = transaction.objectStore('project');
// 添加到数据对象中
objectStore.add(newItem);
```

## Web SQL（已经被抛弃）
关系数据库，通过SQL语句访问。

## Manifest（已经被web标准废除）
manifest 是一个后缀名为 minifest 的文件，在文件中定义那些需要缓存的文件，支持 manifest 的浏览器将会按照 manifest 文件的规则，像文件保存在本地，从而在没有网络链接的情况下，也能访问页面。

当第一次正确配置 app cache 后，再次访问该应用时，浏览器会首先检查 manifest 文件是否有变动，如果有变动就会把相应的变得更新下来，同时改变浏览器里面的 app cache，如果没有变动，就会直接把 app cache 的资源返回。

### 步骤一：在 html 文件指定 manifest 文件
``` html
<!DOCTYPE HTML>
<html manifest="demo.manifest">
...
</html>
```

### 步骤二：设置 manifest 缓存清单
``` js
// 需要缓存的文件，无论是否有网络连接，都从缓存读取
CACHE MANIFEST
/theme.css
/logo.gif
/main.js

// 文件 "login.asp" 永远不会被缓存，且离线时是不可用的
NETWORK:
login.asp

// 如果无法建立因特网连接，则用 "offline.html" 替代 /html5/ 目录中的所有文件
FALLBACK:
/html5/ /404.html
```

### 更新缓存
首先更新 manifest 文件，然后通过 javascript 来操作：

``` js
window.applicationCache.update();
```

优点：

* 可以离线运行
* 可以减少资源请求
* 可以更新资源

缺点：

* 更新的资源，需要二次刷新才会被页面采用
* 不支持增量更新，只有manifest发生变化，所有资源全部重新下载一次
* 缺乏足够容错机制，当清单中任意资源文件出现加载异常，都会导致整个manifest策略运行异常

## Service Worker
官方建议替代 Application Cache（Manifest）的方案 作为一个独立的线程，是一段在后台运行的脚本，可使 web app 也具有类似原生 App 的离线使用、消息推送、后台自动更新等能力。