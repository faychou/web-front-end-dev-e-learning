# web push notification
push notification 是 PWA 的重要组成部分，它使得 web 程序也具有 native app 类似的”离线”通知功能，即使程序的页面被关掉了，用户依然可以收到服务端发送的通知消息。

相关功能：

* web server：后端服务，将消息推送给 push service

* push service：和 browser 相关联的专门用来处理通知的服务，用来接受 web server 推送的消息。每个浏览器都有自己的 push service

* browser：浏览器，除了常规功能之外(毕竟离开浏览器什么都干不了)，它负责和 push service 通信，并在有消息时唤醒 service worker

* client：web 客户端代码，用来注册 service worker

* service worker：最终由它来接受消息并展示给用户

### 获得通知权限
要展示通知，首先我们需要获取系统通知权限：

``` js
// main.js
Notification.requestPermission(status => {
  console.log('Notification permission status:', status);
});
```

运行之后会有一个弹框，让用户选择。用户的选择结果会保存在 Notification.permission中，值为 granted 或 denied 。

### 注册 service worker
``` js
// main.js
window.addEventListener('load', () => {      
  navigator.serviceWorker.register('sw.js').then(swReg => {
    console.log('Service Worker is registered', swReg);
    swRegistration = swReg;
  }).catch(err => {
    console.error('Service Worker Error', err);
  });
});
```

### 向 push service 发起消息订阅请求
``` js
swRegistration.pushManager.subscribe({
  userVisibleOnly: true,
}).then(subscription => {
  console.log('User is subscribed:', subscription);
  // 将subscription保存起来发给web server
}).catch(err => {
  if (Notification.permission === 'denied') {
    console.warn('Permission for notifications was denied');
  } else {
    console.error('Failed to subscribe the user: ', err);
  }
});
```

### web server 往 push service 推送消息
在 nodejs 端我们使用 web-push 包给 push service 推送消息。

``` js
// node端 app.js
const webPush = require('web-push');

// 从client端拿到的subscription
const pushSubscription = { "endpoint": "https://updates.push.services.mozilla.com/wpush/v1/gAAAAABcAN9TNEnJkWUet1JCu0BFE4307sZ1e9RQaxs2x5ReXXcDxU-lRwb9t5B3TWKjSEEmgmJVPjqFDmfWSS2LFiTlynI1T4IVLVyVglR0uM6YYsUa07cUdwnkpwfFLQBCCWjsdgnS", "keys": { "auth": "a-2f-JnVWOWyeic1pJNYNA", "p256dh": "BBgpcZSWJc_VsjQZphdWhADpoDwziiiI3650nG4FrOKrhgWJdMDtWBHDi_BpurksrJc2r-6P2r5BsJBMFGUhuMY" } }

// 要发送的信息
const payload = 'Here is a payload!';

// 发送选项：使用默认值
const options = {}

webPush.sendNotification(
  pushSubscription,
  payload,
  options
).catch(err => {
  console.log('send failed: ', err);
})
```

### service worker 处理浏览器通知
push service 收到消息后，会通知浏览器(如果浏览器当前关闭了，下一次打开时会收到通知)，浏览器唤醒相应的 service worker，具体就是给 service worker 发送 push 事件，service worker 处理 push 事件，并弹个小框将消息展示出来：

``` js
// sw.js
self.addEventListener('push', event => {
  let body;

  if (event.data) {
    body = event.data.text();
  } else {
    body = 'Default body';
  }

  const options = {
    body: body,
    icon: 'images/notification-flat.png',
    vibrate: [100, 50, 100],
    data: {
      dateOfArrival: Date.now(),
      primaryKey: 1
    },
    actions: [
      {
        action: 'explore', title: 'Go to the site',
        icon: 'images/checkmark.png'
      },
      {
        action: 'close', title: 'Close the notification',
        icon: 'images/xmark.png'
      },
    ]
  };

  event.waitUntil(
    // Show notification
    self.registration.showNotification('Push Notification', options);
  );
});
```

通过 showNotification(title, options) 展示通知，第一个参数是标题，第二个参数是控制弹窗的选项：

* body：消息内容

* icon：展示在内容旁边的大图标

* vibrate：振动控制

* data：SW不但可以展示消息，还可以处理用户的点击事件，这个 data 可以存储一些传递给点击处理的信息

* actions：在消息弹框上设置一些点击按钮，这样我们可以根据不同的点击做不同的处理
