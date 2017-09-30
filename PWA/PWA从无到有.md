# PWA
Progressive Web Apps (PWA) 是一个令人兴奋的前端技术的革新。PWA 综合了一系列技术使你的 web app 表现得就像是 native mobile app。

一个 PWA 应用首先是一个网页, 可以通过 Web 技术编写出一个网页应用. 随后添加上 App Manifest 和 Service Worker 来实现 PWA 的安装和离线等功能。

## manifest.json
让 PWA 应用被添加到主屏幕, 使用 manifest.json 定义应用的名称, 图标等等信息。

``` json
{
  "name":"FayChou",
  "short_name":"fc",
  "display":"standalone",
  "start_url":"/",
  "theme_color":"#8888ff",
  "background_color":"#aaaaff",
  "icons":[{
    "src":"static/images/head.png",
    "size":"256*256",
    "type":"image/png"
  }]
}
```

## Service Worker
Service Worker 在网页已经关闭的情况下还可以运行, 用来实现页面的缓存和离线, 后台通知等等功能。

* self: 表示 Service Worker 作用域, 也是全局变量
* caches: 表示缓存
* skipWaiting: 表示强制当前处在 waiting 状态的脚本进入 activate 状态
* clients: 表示 Service Worker 接管的页面

### 添加方法
为了让应用可以离线工作，要做的第一件事情就是注册一个service worker，这是一段在后台运行的脚本程序，并不要用户去打开它，也不需要任何的操作。

第一步，在跟目录下创建一个空文件叫做service-worker.js。这个文件必须放在根目录。因为service worker的作用域范围是跟它所在的位置来决定的。

第二步，在app.js中添加以下代码来检查浏览器是不是支持service worker，如果支持会注册service worker：

``` js
if('serviceWorker' in navigator) {  
navigator.serviceWorker  
  .register('/service-worker.js')  
  .then(function() { 
    console.log('Service Worker Registered'); 
  });  
}

// or

if(navigator.serviceWorker != null) {
  navigator.serviceWorker
  .register('./service-worker.js')
  .then(function(registration){
    console.log("Service Worker Registered:" + registration.scope);
  });
}
```
### 处理静态缓存
当 service worker 被注册以后，用户首次访问页面的时候，一个 install 事件函数就会被触发。在这个事件的回调函数中，我们能够缓存所有应用需要用到的资源。

在 service-worker.js 中添加以下代码:

``` js
// 定义需要缓存的路径，以及需要缓存的静态文件的列表
var cacheStorageKey = 'minimal-pwa-1';
var cacheList = [
  '/',
  'index.html',
  'static/css/index.css',
  'static/images/head.png'
];

// 在注册安装完 Service Worker 时, 抓取资源写入缓存
self.addEventListener('install',function(e) {
  e.waitUntil(
    //打开cache对象
    caches.open(cacheStorageKey)
    .then(function(cache) {
      //当cache数据被打开后，加载资源
      return cache.addAll(cacheList);
    })
    .then(function() {
      return self.skipWaiting()
    })
  )
})
```

调用 `self.skipWaiting()` 方法是为了在页面更新的过程当中, 新的 Service Worker 脚本能立即激活和生效。

### 从缓存中加载资源
网页抓取资源的过程中, Service Worker 可以收到从 PWA 中发起的请求，并且响应, 可以编写代码决定如何响应资源的请求:

``` js
self.addEventListener('fetch',function(e) {
  console.log('[ServiceWorker] Fetch', e.request.url);
  e.respondWith(
    caches.match(e.request)
    .then(function(response) {
      // return response || fetch(e.request);
      if(response != null) {
        return response;
      }
      return fetch(e.request);
    })
  )
});
```

真实的项目当中, 可以根据资源的类型, 站点的特点, 可以专门设计复杂的策略。fetch 事件当中甚至可以手动生成 Response 返回给页面。

### 更新缓存
在service-worker开始运行的时候，activate监听事件就会被激活。

缓存的资源随着版本的更新会过期, 所以会根据缓存的字符串名称清除旧缓存, 采用遍历所有的缓存名称逐一判断决定是否清除:

``` js
self.addEventListener('activate', function(e) {
  console.log('[ServiceWorker] Activate');
  e.waitUntil(
    caches.keys()
    .then(function(cacheNames) {
      return Promise.all(cacheNames.map(function(name) {
        if (name !== cacheStorgeKey) {
          console.log('[ServiceWorker] Removing old cache', name);
          return caches.delete(name);
        }
      }));
    })
  );
  return self.clients.claim();
});
```

在新安装的 Service Worker 中通过调用 self.clients.claim() 取得页面的控制权, 这样之后打开页面都会使用版本更新的缓存。旧的 Service Worker 脚本不再控制着页面之后会被停止。

## Web server for chrome
需要谷歌浏览器版本52或更高，然后下载安装该扩展程序。也可以使用其他的网络服务器。