# WebSocket
WebSocket 是 HTML5 开始提供的一种浏览器与服务器进行全双工通讯的网络技术，属于应用层协议。它基于 TCP 传输协议，并复用 HTTP 的握手通道。WebSocket 的出现，使得浏览器具备了持续的实时双向通信的能力。

那么在 WebSocket 出现之前，主要有以下的解决方案：

#### 传统轮询
采取 setInterval 或者 setTimeout 来实现，这是一种比较常见的持续通信方案。

``` js
setInterval(function() {
  $.get("/path/to/server", function(data, status) {
    console.log(data);
  });
}, 10000);
```

但是该方法存在着很大的缺陷：在网络情况不稳定的情况下，服务器从接收请求、发送请求到客户端接收请求的总时间有可能超过 10 秒，而请求是以 10 秒间隔发送的，这样会导致接收的数据到达先后顺序与发送顺序不一致。于是出现了采用 setTimeout 的轮询方式：

``` js
function poll() {
  setTimeout(function() {
    $.get("/path/to/server", function(data, status) {
      console.log(data);
      // 发起下一次请求
      poll();
    });
  }, 10000);
}
```

#### 长轮询
传统的轮询方式都存在一个严重缺陷：在每次请求时都会新建一个 HTTP 请求，然而并不是每次都能返回所需的新数据。当同时发起的请求达到一定数目时，会对服务器造成较大负担。这时我们可以采用长轮询方式解决这个问题。

长轮询的基本思想是在每次客户端发出请求后，服务器检查上次返回的数据与此次请求时的数据之间是否有更新，如果有更新则返回新数据并结束此次连接，否则服务器 hold 住此次连接，直到有新数据时再返回相应。而这种长时间的保持连接可以通过设置一个较大的 HTTP timeout 实现。

``` php
/* 服务器（PHP）*/
<?php
    // 示例数据为data.txt
    $filename= dirname(__FILE__)."/data.txt";
    // 从请求参数中获取上次请求到的数据的时间戳
    $lastmodif = isset( $_GET["timestamp"])? $_GET["timestamp"]: 0 ;
    // 将文件的最后一次修改时间作为当前数据的时间戳
    $currentmodif = filemtime($filename);

    // 当上次请求到的数据的时间戳*不旧于*当前文件的时间戳，使用循环"hold"住当前连接，并不断获取文件的修改时间
    while ($currentmodif <= $lastmodif) {
        // 每次刷新文件信息的时间间隔为10秒
        usleep(10000);
        // 清除文件信息缓存，保证每次获取的修改时间都是最新的修改时间
        clearstatcache();
        $currentmodif = filemtime($filename);
    }

    // 返回数据和最新的时间戳，结束此次连接
    $response = array();
    $response["msg"] =Date("h:i:s")." ".file_get_contents($filename);
    $response["timestamp"]= $currentmodif;
    echo json_encode($response);
?>
```
``` js
//客户端
function longPoll (timestamp) {
    var _timestamp;
    $.get("/path/to/server?timestamp=" + timestamp)
    .done(function(res) {
        try {
            var data = JSON.parse(res);
            console.log(data.msg);
            _timestamp = data.timestamp;
        } catch (e) {}
    })
    .always(function() {
        setTimeout(function() {
            longPoll(_timestamp || Date.now()/1000);
        }, 10000);
    });
}
```

#### 服务器发送事件
服务器发送事件（以下简称 SSE）是 HTML 5 规范的一个组成部分，可以实现服务器到客户端的单向数据通信。通过 SSE ，客户端可以自动获取数据更新，而不用重复发送 HTTP 请求。一旦连接建立，“事件”便会自动被推送到客户端。服务器端 SSE 通过 事件流(Event Stream) 的格式产生并推送事件。

事件流对应的 MIME类型 为 text/event-stream ，包含四个字段：

* event：表示事件类型；
* data：表示消息内容；
* id：用于设置客户端 EventSource 对象的 last event ID string 内部属性；
* retry：指定了重新连接的时间。

``` php
/* 服务器（PHP）*/
<?php
  header("Content-Type: text/event-stream");
  header("Cache-Control: no-cache");
  
  // 每隔1秒发送一次服务器的当前时间
  while (1) {
    $time = date("r");
    echo "event: ping\n";
    echo "data: The server time is: {$time}\n\n";
    ob_flush();
    flush();
    sleep(1);
  }
?>
```

客户端中，SSE 借由 EventSource 对象实现。EventSource 包含五个外部属性：onerror, onmessage, onopen, readyState、url，以及两个内部属性：reconnection time 与 last event ID string。在 onerror 属性中我们可以对错误捕获和处理，而 onmessage 则对应着服务器事件的接收和处理。另外也可以使用 addEventListener 方法来监听服务器发送事件，根据 event 字段区分处理。

``` js
/* 客户端 */
var eventSource = new EventSource("/path/to/server");
eventSource.onmessage = function (e) {
  console.log(e.event, e.data);
}

// 或者
eventSource.addEventListener("ping", function(e) {
  console.log(e.event, e.data);
}, false);
```

SSE 相较于轮询具有较好的实时性，使用方法也非常简便。然而 SSE 只支持服务器到客户端单向的事件推送，而且所有版本的 IE（包括到目前为止的 Edge）都不支持 SSE。如果需要强行支持 IE 和部分移动端浏览器，可以尝试 EventSource Polyfill（本质上仍然是轮询）。

> 注意：以上方法都是非实时，多少都有点延迟。

### WebSocket 优点
* 支持双向通信，实时性更强。
* 更好的二进制支持。
* 较少的控制开销。连接创建后，ws客户端、服务端进行数据交换时，协议控制的数据包头部较小。在不包含头部的情况下，服务端到客户端的包头只有2~10字节（取决于数据包长度），客户端到服务端的的话，需要加上额外的4字节的掩码。而HTTP协议每次通信都需要携带完整的头部。
* 支持扩展。ws协议定义了扩展，用户可以扩展协议，或者实现自定义的子协议。（比如支持自定义压缩算法等）

### WebSocket 特点
最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。


* 建立在 TCP 协议之上，服务器端的实现比较容易。
* 与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443 ，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
* 数据格式比较轻量，性能开销小，通信高效。
* 可以发送文本，也可以发送二进制数据。
* 没有同源限制，客户端可以与任意服务器通信。
* 协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL。

### 简单例子
#### 服务端 
监听 8080 端口。当有新的连接请求到达时，打印日志，同时向客户端发送消息。当收到到来自客户端的消息时，同样打印日志。


``` js
//server.js
var app = require('express')();
var server = require('http').Server(app);
var WebSocket = require('ws');

var wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', function connection(ws) {
    console.log('server: receive connection.');
    
    ws.on('message', function incoming(message) {
        console.log('server: received %s', message);
        ws.send('server: reply');
    });

    ws.on('pong', () => {
        console.log('server: received pong from client');
    });

    ws.send('world');
    
    // setInterval(() => {
    //     ws.ping('', false, true);
    // }, 2000);
});

app.get('/', function (req, res) {
  res.sendfile(__dirname + '/index.html');
});

app.listen(3000);
```

#### 客户端
向 8080 端口发起 WebSocket 连接。连接建立后，打印日志，同时向服务端发送消息。接收到来自服务端的消息后，同样打印日志。

``` html
<!-- index.html -->
<html>
<head>
  <title>WebSocket demo</title>
  <meta charset="utf8">
</head>
<body>

<script>
  var ws = new WebSocket('ws://localhost:8080');
  ws.onopen = function () {
    console.log('client: ws connection is open');
    ws.send('hello');
  };
  ws.onmessage = function (e) {
    console.log('client: received %s', e.data);
  };
</script>
</body>
</html>
```

### 数据帧
WebSocket 客户端、服务端通信的最小单位是帧（frame），由 1 个或多个帧组成一条完整的消息（message）。

发送端：将消息切割成多个帧，并发送给服务端；

接收端：接收消息帧，并将关联的帧重新组装成完整的消息；

### 数据传递
一旦 WebSocket 客户端、服务端建立连接后，后续的操作都是基于数据帧的传递。WebSocket 根据 opcode 来区分操作的类型。比如 0x8 表示断开连接，0x0 - 0x2 表示数据交互。

WebSocket 的每条消息可能被切分成多个数据帧。当 WebSocket 的接收方收到一个数据帧时，会根据 FIN 的值来判断，是否已经收到消息的最后一个数据帧。

FIN=1 表示当前数据帧为消息的最后一个数据帧，此时接收方已经收到完整的消息，可以对消息进行处理。FIN=0，则接收方还需要继续监听接收其余的数据帧。

此外，opcode 在数据交换的场景下，表示的是数据的类型。0x01 表示文本，0x02 表示二进制。而 0x00 比较特殊，表示延续帧（continuation frame），顾名思义，就是完整消息对应的数据帧还没接收完。

客户端向服务端两次发送消息，服务端收到消息后回应客户端，这里主要看客户端往服务端发送的消息。

##### 第一条消息：
FIN=1, 表示是当前消息的最后一个数据帧。服务端收到当前数据帧后，可以处理消息。opcode=0x1，表示客户端发送的是文本类型。

##### 第二条消息：
FIN=0，opcode=0x1，表示发送的是文本类型，且消息还没发送完成，还有后续的数据帧。

FIN=0，opcode=0x0，表示消息还没发送完成，还有后续的数据帧，当前的数据帧需要接在上一条数据帧之后。

FIN=1，opcode=0x0，表示消息已经发送完成，没有后续的数据帧，当前的数据帧需要接在上一条数据帧之后。服务端可以将关联的数据帧组装成完整的消息。

### 连接保持+心跳
为了保持客户端、服务端的实时双向通信，需要确保客户端、服务端之间的 TCP 通道保持连接没有断开。然而，对于长时间没有数据往来的连接，如果依旧长时间保持着，可能会浪费连接资源。但是也有某些场景需要客户端、服务端即使长时间没有数据往来，也要保持连接。这个时候，可以采用心跳来实现。

著作权归作者所有。
商业转载请联系作者获得授权,非商业转载请注明出处。
链接:http://caibaojian.com/websocket.html
来源:http://caibaojian.com

* 发送方->接收方：ping
* 接收方->发送方：pong

ping、pong 的操作，对应的是 WebSocket 的两个控制帧，opcode 分别是 0x9、0xA。

``` js
//服务端向客户端发送 ping (采用 ws 模块)
ws.ping('', false, true);
```

### Sec-WebSocket-Key/Accept的作用
主要作用在于提供基础的防护，减少恶意连接、意外连接。

* 避免服务端收到非法的websocket连接（比如http客户端不小心请求连接websocket服务，此时服务端可以直接拒绝连接）
* 确保服务端理解websocket连接。因为ws握手阶段采用的是http协议，因此可能ws连接是被一个http服务器处理并返回的，此时客户端可以通过Sec-WebSocket-Key来确保服务端认识ws协议。（并非百分百保险，比如总是存在那么些无聊的http服务器，光处理Sec-WebSocket-Key，但并没有实现ws协议。。。）
* 用浏览器里发起ajax请求，设置header时，Sec-WebSocket-Key以及其他相关的header是被禁止的。这样可以避免客户端发送ajax请求时，意外请求协议升级（websocket upgrade）
* 可以防止反向代理（不理解ws协议）返回错误的数据。比如反向代理前后收到两次ws连接的升级请求，反向代理把第一次请求的返回给cache住，然后第二次请求到来时直接把cache住的请求给返回（无意义的返回）。
* Sec-WebSocket-Key主要目的并不是确保数据的安全性，因为Sec-WebSocket-Key、Sec-WebSocket-Accept的转换计算公式是公开的，而且非常简单，最主要的作用是预防一些常见的意外情况（非故意的）。

``` js
const crypto = require('crypto');
const magic = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11';
const secWebSocketKey = 'w4v7O6xFTi36lq3RNcgctw==';

let secWebSocketAccept = crypto.createHash('sha1')
	.update(secWebSocketKey + magic)
	.digest('base64');

console.log(secWebSocketAccept);
// Oy4NRAQ13jhfONC7bP8dTKb4PTU=
```
