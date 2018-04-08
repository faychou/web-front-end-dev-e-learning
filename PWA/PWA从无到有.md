# PWA
PWA 全称 Progressive Web Apps，中文名叫渐进式网络应用。是 Google 推出的一个令人兴奋的前端技术。PWA 综合了一系列技术使你的 web app 表现得就像是 native mobile app，让你能够构建一个完全离线的网站，为用户提供几乎是瞬间加载的体验。

一个 PWA 应用首先是一个网页, 可以通过 Web 技术编写出一个网页应用. 随后添加上 App Manifest 和 Service Worker 来实现 PWA 的安装和离线等功能。

### 特点：
* 响应式
* 独立于网络连接
* 类似原生应用的交互体验
* 始终保持更新
* 安全
* 可发现
* 可重连
* 可安装
* 可链接

由于 PWA 是一个全新的内容，所以在学习 PWA 之前，你需要保证你已经熟练使用以下的内容：

* ES6 基础；
* Promise ，当然这也是 ES6 里的知识，单独拿出来说明这很重要；
* fetch，全新的可替代 AJAX 的 API；
* WebWorker，JavaScript 解决单线程的方案；
* Cache API。

## manifest.json
这是 Web 应用清单文件，该文件包含网站相关的信息，包括图标，背景屏幕，颜色和默认方向等。能够使浏览器将 Web 应用安装到设备的主屏幕，以便为用户提供更快捷的访问和更丰富的体验。其次，还可以自定义浏览器的启动画面，并允许你自定义浏览器的地址栏和模板颜色等。

需要为 Web 应用的所有页面都添加 link 标签：

``` html
<link rel="manifest" href="/manifest.json">
```

这是一个 基础的 Web 应用清单：

``` js
{
  "name":"FayChou",
  "short_name":"fc",
  "display":"standalone",
  "start_url":"/index.html",
  "theme_color":"#8888ff",
  "background_color":"#aaaaff",
  "icons":[{
    "src":"static/images/head.png",
    "size":"256*256",
    "type":"image/png"
  }]
}
```

### name
用作当用户被提示安装应用时出现的文本。

### short_name
用作当应用安装后出现在用户主屏幕上的文本。

### start_url
start_url 用来指定当用户从设备启动应用时加载的第一个页面。如果给定的是相对路径，那么基础路径就是清单的路径。如果你想追踪有多少人是通过主屏幕图标访问网站的，你可能想要在查询字符串中追加追踪代码，比如 /index.html?homescreen=1 这样的 URL 。这样一来，你的 Web 分析软件包就可以分辨出通过主屏幕图标到达的用户。

### display
Web 应用显示模式。

* Fullscreen - 打开 Web 应用并占用整个可用的显示区域。
* Standalone - 打开 Web 应用以看起来像一个独立的原生应用。此模式下，用户代理将排除诸如 URL 栏等标准浏览器 UI 元素，但可以包括诸如状态栏和系统返回按钮的其他系统 UI 元素。
* Minimal-ui - 此模式类似于 fullscreen，但为终端用户提供了可访问的最小 UI 元素集合，例如，后退按钮、前进按钮、重载按钮以及查看网页地址的一些方式。
* Browser - 使用操作系统内置的标准浏览器来打开 Web 应用。

### theme_color
可以对浏览器的地址栏进行着色，以符合网站的主色调。

### icons
字段决定了当 Web 应用被添加到设备主屏幕时所显示的图标。

## Service Worker
Service Worker 能够在网页已经关闭的情况下运行, 用来实现页面的缓存和离线, 后台通知等功能，是 PWA 的最关键组成部分。

Service Worker 是用 JavaScript 编写的，但是又与标准 JavaScript 文件略有不同:

* 运行在它自己的全局脚本上下文中；

* 不绑定到具体的网页；

* 无法修改网页中的元素，因为它无法访问 DOM；

* 只能使用 HTTPS。

Service Worker 能够拦截进出的 HTTP 请求，从而完全控制你的网站。Service Worker 运行在 worker 上下文中，这意味着它无法访问 DOM，它与应用的主要 JavaScript 运行在不同的线程上，所以它不会被阻塞。它们被设计成是完全异步的，因此你无法使用诸如同步 XHR 和 localStorage 之类的功能。所以我们只需记住 Service Worker 可以让你全权控制网站中所有进出的网络请求。

* self: 表示 Service Worker 作用域, 也是全局变量；

* caches: 表示缓存；

* skipWaiting: 表示强制当前处在 waiting 状态的脚本进入 activate 状态；

* clients: 表示 Service Worker 接管的页面。

### 安全
为了让 Service Worker 能在网站上运行，需要通过 HTTPS 来提供服务。

### 生命周期
#### register
当用户首次导航至 URL 时，服务器会返回响应的网页。在注册过程中，浏览器会下载、解析并执行 Service Worker 。如果在此步骤中出现任何错误，register() 返回的 promise 都会执行 reject 操作，并且 Service Worker 会被废弃。

### install
发生在浏览器安装并成功注册 Service Worker 时，这是把资源添加到缓存中的绝佳时间。

### activated
一旦安装这步完成，Service Worker 便会激活并控制在其范围内的一切。如果生命周期中的所有事件都成功了，Service Worker 便已准备就绪，随时可以使用了！

### 添加方法
为了让应用可以离线工作，要做的第一件事情就是注册一个 service worker，这是一段在后台运行的脚本程序，并不要用户去打开它，也不需要任何的操作。

第一步，在跟目录下创建一个空文件叫做 service-worker.js。这个文件必须放在根目录。因为 service worker 的作用域范围是跟它所在的位置来决定的。

第二步，在 app.js 中添加以下代码来检查浏览器是不是支持 service worker，如果支持会注册 service worker：

``` js
if('serviceWorker' in navigator) { // 检查当前浏览器是否支持 Service Workers
navigator.serviceWorker  
  .register('/service-worker.js')  // 如果支持就注册一个 'service-worker.js' 的 Service Worker 文件
  .then((registration) => { 
    console.log('ServiceWorker Registered successful:',registration.scope); 
  }).catch((err) => { // 注册失败
    console.log('ServiceWorker Registered failed: ', err);
  });  
}

// or
if(navigator.serviceWorker != null) {
  navigator.serviceWorker
  .register('./service-worker.js')
  .then((registration) => {
    console.log("ServiceWorker Registered:",registration.scope);
  });
}
```

使用 `navigator.serviceWorker.register('/service-worker.js')` 函数注册，该函数返回 promise，然后通知浏览器下载 Service Worker 文件，如果注册成功，它会开始 Service Worker 生命周期的剩余阶段。

### 处理静态缓存
当 service worker 被注册以后，用户首次访问页面的时候，一个 install 事件函数就会被触发。在这个事件的回调函数中，我们能够缓存所有应用需要用到的资源。

在 service-worker.js 中添加以下代码:

``` js
// 定义缓存的名称
var cacheStorageKey = 'minimal-pwa-1';

// 定义需要缓存的静态文件的列表
var cacheList = [
  '/',
  'index.html',
  'static/css/index.css',
  'static/images/head.png'
];

// 在注册安装完 Service Worker 时, 抓取资源写入缓存
// self 在 Worker 里面相当于 Global
self.addEventListener('install',(e) => { // 进入 Service Worker 的安装事件
  e.waitUntil(
    // 打开 cache 对象，这是一个全局变量
    caches.open(cacheStorageKey)
    .then((cache) => {
      
      // 添加到缓存中
      return cache.addAll(cacheList);
    })
    .then(() => self.skipWaiting())
  )
})
```

调用 `self.skipWaiting()` 方法是为了在页面更新的过程当中, 新的 Service Worker 脚本能立即激活和生效。

如果所有的文件都成功缓存了，那么 Service Worker 便会安装完成。如果任何文件下载失败了，那么安装过程也会随之失败。

### 从缓存中加载资源
网页抓取资源的过程中, Service Worker 可以收到从 PWA 中发起的请求，并且响应, 可以编写代码决定如何响应资源的请求:

``` js
self.addEventListener('fetch',(e) => { // 添加 fetch 事件的事件监听器
  console.log('[ServiceWorker] Fetch', e.request.url);
  e.respondWith(
    caches.match(e.request) //检查传入的请求 URL 是否匹配当前缓存中存在的任何内容
    .then((response) => {
      // return response || fetch(e.request);
      if(response) {
        return response;
      }
      return fetch(e.request);
    })
  )
});
```

使用 caches.match() 函数来检查传入的请求 URL 是否匹配当前缓存中存在的任何内容。如果存在的话，我们就简单地返回缓存的资源。但是，如果资源并不存在于缓存当中，我们就如往常一样继续，通过网络来获取资源。

### 更新缓存
在 service-worker 开始运行的时候，activate 监听事件就会被激活。

缓存的资源随着版本的更新会过期, 所以会根据缓存的字符串名称清除旧缓存, 采用遍历所有的缓存名称逐一判断决定是否清除:

``` js
self.addEventListener('activate', (e) => {
  console.log('[ServiceWorker] Activate');
  e.waitUntil(
    caches.keys()
    .then((cacheNames) => {
      return Promise.all(cacheNames.map((name) => {
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

### 拦截并缓存
Service Workers 能够拦截 HTTP 请求，先请求资源，然后立即缓存起来。这样一来，对于同样资源的发起的下一次 HTTP 请求，我们可以立即将其从 Service Worker 缓存中取出。

``` js
var cacheName = 'helloWorld';
self.addEventListener('fetch', (event) => { 
  event.respondWith(
    caches.match(event.request) 
    .then((response) => {
      if (response) {  
        return response;
      }
      var requestToCache = event.request.clone(); // 克隆了请求。请求是一个流，只能消耗一次
      return fetch(requestToCache).then( // 尝试按预期一样发起原始的 HTTP 请求
        (response) => {
          if (!response || response.status !== 200) { 
            return response; // 请求失败或者服务器响应了错误代码，则返回错误信息
          }
          var responseToCache = response.clone(); // 再次克隆响应，因为需要将其添加到缓存中，而且它还将用于最终返回响应
          caches.open(cacheName) // 打开名称为 “helloWorld” 的缓存
            .then((cache) => {
              cache.put(requestToCache, responseToCache); // 将响应添加到缓存中
            });
          return response;
        }
      );
    })
  );
});
```

先检查请求的资源是否存在于缓存之中。如果存在，我们可以就此返回缓存并不再继续执行代码。

如果请求的资源没在缓存之中，就按原计划发起网络请求。在代码更进一步之前，克隆请求。这么做是因为请求是一个流，它只能消耗一次。因为我们已经通过缓存消耗了一次，然后发起 HTTP 请求还要再消耗一次，所以我们需要在此时克隆请求。

然后，检查 HTTP 响应，确保服务器返回的是成功响应并且没有任何问题，以避免缓存一个错误的结果。

最后，如果成功响应，再次克隆响应，使用这个响应并将其添加至缓存中，以便下次再使用它。如果用户刷新页面或访问网站另一个请求了这些资源的页面，它会立即从缓存中获取资源，而不再是通过网络。

## 添加到主屏幕
添加到主屏幕功能最棒的是几乎不用写任何代码便可实现功能，浏览器已经为你做好了这一切。

前提条件:

* 需要 manifest.json 文件
* 清单文件需要启动 URL
* 需要 144x144 的 PNG 图标
* 网站正在使用通过 HTTPS 运行的 Service Worker
* 用户需要至少浏览网站两次，并且两次访问间隔在五分钟之上

## 添加启动页面
当用户点击主屏幕上的 Web 应用，浏览器渲染页面的第一帧时，将显示一个临时的“启动页面”。

``` js
{
  "name":"FayChou",
  "background_color":"#aaaaff",
  "icons":[{
    "src":"static/images/head.png",
    "size":"256*256",
    "type":"image/png"
  }]
}
```

启动页面是根据清单文件中的信息动态生成的。它使用了 name 和 background_color 两个属性，以及浏览器根据在 icons 数组中选择尺寸最合适的图标。

## 推送通知
发送推送通知需要采取三个步骤：

首先，浏览器会显示一个提示以询问用户是否愿意接受通知。如果接受，我们便可以将用户的订阅详细信息保存在服务器上，稍后会使用它们来发送通知。请注意，这些订阅详细信息对于每个用户、设备和浏览器来说是唯一的，所以如果一个用户使用多个设备登录了你的网站，那么每台设备都会提醒该用户是否接受通知。

一旦用户接受了，我们便能够使用这些保存过的订阅详情来给用户发送消息，这可以通过计划任务来完成，该任务可以及时更新用户信息。

最后，推送通知。

### 订阅通知
在能开始向用户发送通知前，我们需要通过显示提示的方式来征求他们的允许。这个提示功能是浏览器默认自带的，但首先我们需要添加一些代码来确保提示已经启用。如果用户接受提示的话，你将获得一个包含该用户订阅信息的订阅对象。可如果用户拒绝的话，你将无法向其发送任何消息，并且他们不会再被提示。

``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Progressive Times</title>
    <link rel="manifest" href="/manifest.json"> 
  </head>
  <body>
    <script>
      var endpoint;
      var key;
      var authSecret;
      var vapidPublicKey = 'BAyb_WgaR0L0pODaR7wWkxJi__tWbM1MPBymyRDFEGjtDCWeRYS9EF7yGoCHLdHJi6hikYdg4MuYaK0XoD0qnoY'; <!-- 客户端和服务端都需要公钥，以确保消息是加密过的 -->

      function urlBase64ToUint8Array(base64String) { <!-- 需要将 VAPID 钥从 base64 字符串转换成 Uint8 数组，因为这是 VAPID 规范要求的 -->
        const padding = '='.repeat((4 - base64String.length % 4) % 4);
        const base64 = (base64String + padding)
          .replace(/\-/g, '+')
          .replace(/_/g, '/');
        const rawData = window.atob(base64);
        const outputArray = new Uint8Array(rawData.length);
        for (let i = 0; i < rawData.length; ++i) {
          outputArray[i] = rawData.charCodeAt(i);
        }
        return outputArray;
      }
      if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('sw.js').then(function (registration) {
          return registration.pushManager.getSubscription()                              <!-- 获取任何已存在的订阅 -->
            .then(function (subscription) {
              if (subscription) {                                                        <!-- 如果已经订阅过了，则无需再次注册 -->
                return;
              }
              return registration.pushManager.subscribe({  <!-- 还没有订阅过，则显示一个提示 -->
                  userVisibleOnly: true,
                  applicationServerKey: urlBase64ToUint8Array(vapidPublicKey)
                })
                .then(function (subscription) {
                  var rawKey = subscription.getKey ? subscription.getKey('p256dh') : ''; <!-- 需要从订阅对象中获取 key 和 authSecret -->
                  key = rawKey ? btoa(String.fromCharCode.apply(null, new Uint8Array(rawKey))) : '';
                  var rawAuthSecret = subscription.getKey ? subscription.getKey('auth') : '';
                  authSecret = rawAuthSecret ?
                    btoa(String.fromCharCode.apply(null, new Uint8Array(rawAuthSecret))) : '';
                  endpoint = subscription.endpoint;
                  return fetch('./register', { <!-- 将详细信息发送给服务器以注册用户 -->
                    method: 'post',
                    headers: new Headers({
                      'content-type': 'application/json'
                    }),
                    body: JSON.stringify({
                      endpoint: subscription.endpoint,
                      key: key,
                      authSecret: authSecret,
                    }),
                  });
                });
            });
        }).catch(function (err) {
          // 注册失败 :(
          console.log('ServiceWorker registration failed: ', err);
        });
      }
    </script>
  </body>
</html>
```

要想发送推送通知，需要使用 VAPID 协议。VAPID 是“自主应用服务器标识” ( Voluntary Application Server Identification ) 的简称。它是一个规范，它本质上定义了应用服务器和推送服务之间的握手，并允许推送服务确认哪个站点正在发送消息。

### 发送通知
``` js
const webpush = require('web-push'); 
const express = require('express');
var bodyParser = require('body-parser');
const app = express();
webpush.setVapidDetails( // 设置 VAPID 详情
  'mailto:contact@deanhume.com',
  'BAyb_WgaR0L0pODaR7wWkxJi__tWbM1MPBymyRDFEGjtDCWeRYS9EF7yGoCHLdHJi6hikYdg4MuYaK0XoD0qnoY',
  'p6YVD7t8HkABoez1CvVJ5bl7BnEdKUu5bSyVjyxMBh0'
);
app.post('/register', function (req, res) { // 监听指向 '/register' 的 POST 请求
  var endpoint = req.body.endpoint;
  saveRegistrationDetails(endpoint, key, authSecret); // 保存用户注册详情，这样我们可以在稍后阶段向他们发送消息
  const pushSubscription = { // 建立 pushSubscription 对象
    endpoint: req.body.endpoint,
    keys: {
      auth: req.body.authSecret,
      p256dh: req.body.key
    }
  };
  var body = 'Thank you for registering';
  var iconUrl = 'https://example.com/images/homescreen.png';
  webpush.sendNotification(pushSubscription, // 发送 Web 推送消息
      JSON.stringify({
        msg: body,
        url: 'http://localhost:3111/',
        icon: iconUrl
      }))
    .then(result => res.sendStatus(201))
    .catch(err => {
      console.log(err);
    });
});
app.listen(3111, function () {
  console.log('Web push app listening on port 3111!')
});
```

### 接收通知并与之互动
现在我们存储了用户的唯一订阅详情，可以开始给他们发送推送消息了，并为他们提供相关重要通知的及时更新。

``` js
self.addEventListener('push', function (event) {
  var payload = event.data ? JSON.parse(event.data.text()) : 'no payload';  // 检查服务端是否发来了任何有效载荷数据
  var title = 'Progressive Times';
  event.waitUntil(
    self.registration.showNotification(title, { // 使用提供的信息来显示 Web 推送通知
      body: payload.msg,
      url: payload.url,
      icon: payload.icon
    })
  );
});
```

为了用户能够与推送通知进行互动，需要处理通知的点击事件：

``` js
self.addEventListener('notificationclick', function (event) {
  event.notification.close(); // 一旦我们点击了通知标题，它便会关闭
  event.waitUntil( // 检查当前窗口是否已经打开，如果已打开则切换至当前窗口
    clients.matchAll({
      type: "window"
    })
    .then(function (clientList) {
      for (var i = 0; i < clientList.length; i++) {
        var client = clientList[i];
        if (client.url == '/' && 'focus' in client)
          return client.focus();
      }
      if (clients.openWindow) {
        return clients.openWindow('http://localhost:3111'); // 点击后打开该 URL
      }
    })
  );
});
```

接收此通知的一些用户会使用具有振动功能的设备，例如移动电话。为了开启振动功能，我们可以为通知添加振动模式。振动模式可以是数字数组，也可以是单个数字，但它会被看作单个数字的数组。数组中的值表示以毫秒为单位的时间，索引为偶数的数字表示振动的时间，索引为奇数的数字表示在下一次振动之前暂停多久。

``` js
self.addEventListener('push', function (event) {
  var payload = event.data ? JSON.parse(event.data.text()) : 'no payload';
  var title = 'Progressive Times';
  event.waitUntil(
    self.registration.showNotification(title, {
      body: payload.msg,
      url: payload.url,
      icon: payload.icon,
      actions: [
        { action: 'voteup',   title: '𑠀 Vote Up'   }, // 出现在通知中的操作
        { action: 'votedown', title: '𑠀 Vote Down' }
      ],
      vibrate: [300, 100, 400] // 振动300毫秒，然后暂停100毫秒，然后再振动400毫秒
    })
  );
});
```

使用通知操作来增强推送通知。

``` js
self.addEventListener('notificationclick', function (event) {
  event.notification.close(); // 一旦用户点击了通知标题，它便会关闭
  if (event.action === 'voteup') {  // 确定用户选择了哪个操作
    clients.openWindow('http://localhost:/voteup');
  } else {
    clients.openWindow('http://localhost:/votedown'); // 根据用户的选择，将他们引导至正确的 URL
  }
}, false);
```

### 取消订阅
用户能够通过改变浏览器中的一些设置来取消订阅，但是你可能需要以编程方式来取消某个用户的订阅。例如，你可以在页面中添加一个简单的按钮，通过点击按钮来允许用户取消订阅，而不是让用户在浏览器设置中到处找寻。

``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Progressive Times</title>
    <link rel="manifest" href="/manifest.json">
    <button type="button" id="unsubscribe">Unsubscribe</button> <!-- 取消订阅按钮 -->
  </head>
  <body>
    <script>
      function unsubscribe() {
        if ('serviceWorker' in navigator) {
          navigator.serviceWorker.ready
            .then((serviceWorkerRegistration) => {
              serviceWorkerRegistration.pushManager.getSubscription() <!-- 检查用户是否已经订阅 -->
                .then((subscription) => {
                  if (!subscription) {
                    console.log("Not subscribed, nothing to do.");
                    return;
                  }

                  subscription.unsubscribe() <!-- 如果用户已经订阅，就取消订阅 -->
                    .then(function () {
                      console.log("Successfully unsubscribed!.");
                    })
                    .catch((e) => {
                      logger.error('Error thrown while unsubscribing from push messaging', e);
                    });
                });
            });
        }
      }
      document.getElementById("unsubscribe").addEventListener("click", unsubscribe); <!-- 为取消订阅按钮添加点击事件的事件监听器 -->
    </script>
  </body>
</html>
```

## Web server for chrome
需要谷歌浏览器版本52或更高，然后下载安装该扩展程序。也可以使用其他的网络服务器。