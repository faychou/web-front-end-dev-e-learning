# WebSocket
WebSocket 是 HTML5 开始提供的一种浏览器与服务器进行全双工通讯的网络技术，属于应用层协议。它基于 TCP 传输协议，并复用 HTTP 的握手通道。WebSocket 的出现，使得浏览器具备了持续的实时双向通信的能力。

那么在 WebSocket 出现之前，为了保持客户端和服务端持续的通讯以保证双方信息的同步，主要有以下的解决方案：

## 传统通信方案
### 传统轮询
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

### 长轮询
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

### 服务器发送事件
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
* 协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL，如：`ws://example.com:80/path`。

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

## WebSocket 协议
一旦客户端和服务器都发送了它们的握手，且如果握手成功，接着开始数据传输部分。 这是一个每一端都可以的双向通信信道，彼此独立，随意发生数据。本协议有两部分：握手和数据传输。

### 握手
首先，客户端发起协议升级请求。可以看到，采用的是标准的 HTTP 报文格式，且只支持GET方法。

``` http
GET / HTTP/1.1
Host: localhost:8080
Origin: http://127.0.0.1:3000
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: w4v7O6xFTi36lq3RNcgctw==
Sec-WebSocket-Protocol: chat, superchat
```

* Connection: Upgrade 表示要升级协议
* Upgrade: websocke 表示要升级到 websocket 协议。
* Sec-WebSocket-Version: 13 表示 websocket 的版本。如果服务端不支持该版本，需要返回一个 Sec-WebSocket-Versionheader ，里面包含服务端支持的版本号。
* Sec-WebSocket-Key 与后面服务端响应首部的 Sec-WebSocket-Accept 是配套的，提供基本的防护，比如恶意的连接，或者无意的连接。

然后，服务端返回以下内容，响应协议升级，状态代码 101 表示协议切换。

``` http
HTTP/1.1 101 Switching Protocols
Connection:Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: Oy4NRAQ13jhfONC7bP8dTKb4PTU=
Sec-WebSocket-Protocol: chat
```

#### Sec-WebSocket-Accept
Sec-WebSocket-Accept 根据客户端请求首部的 Sec-WebSocket-Key 计算出来。

计算公式为：

第一步：将 Sec-WebSocket-Key 跟 258EAFA5-E914-47DA-95CA-C5AB0DC85B11 拼接。

第二步：通过 SHA1 计算出摘要，并转成 base64 字符串。

### 数据帧
WebSocket 客户端、服务端通信的最小单位是帧（frame），由 1 个或多个帧组成一条完整的消息（message）。

* 发送端：将消息切割成多个帧，并发送给服务端；
* 接收端：接收消息帧，并将关联的帧重新组装成完整的消息；

#### 数据帧格式详解
#### FIN
占 1 个比特。如果是 1，表示这是 消息（message）的最后一个数据帧，，此时接收方已经收到完整的消息，可以对消息进行处理。FIN=0，则接收方还需要继续监听接收其余的数据帧。

#### RSV1, RSV2, RSV3
各占 1 个比特。一般情况下全为 0。当客户端、服务端协商采用 WebSocket 扩展时，这三个标志位可以非 0，且值的含义由扩展进行定义。如果出现非零的值，且并没有采用 WebSocket 扩展，连接出错。

#### Opcode
占 4 个比特。Opcode 的值决定了应该如何解析后续的数据载荷（data payload）。如果操作代码是不认识的，那么接收端应该断开连接（fail the connection）。代码值如下：

* %x0：表示一个延续帧。当 Opcode 为 0 时，表示本次数据传输采用了数据分片，当前收到的数据帧为其中一个数据分片。
* %x1：表示这是一个文本帧（frame）
* %x2：表示这是一个二进制帧（frame）
* %x3-7：保留的操作代码，用于后续定义的非控制帧。
* %x8：表示连接断开。
* %x8：表示这是一个 ping 操作。
* %xA：表示这是一个 pong 操作。
* %xB-F：保留的操作代码，用于后续定义的控制帧。

#### Mask
占 1 个比特。表示是否要对数据载荷进行掩码操作。从客户端向服务端发送数据时，需要对数据进行掩码操作；从服务端向客户端发送数据时，不需要对数据进行掩码操作。

如果服务端接收到的数据没有进行过掩码操作，服务端需要断开连接。

如果 Mask 是 1，那么在 Masking-key 中会定义一个 掩码键（masking key），并用这个掩码键来对数据载荷进行反掩码。所有客户端发送到服务端的数据帧，Mask 都是 1。

#### Payload length
表示数据载荷的长度。单位是字节。为 7 位，或 7+16 位，或 1+64 位。

假设数 Payload length === x，如果：

* x 为 0~126：数据的长度为 x 字节。
* x 为 126：后续 2 个字节代表一个 16 位的无符号整数，该无符号整数的值为数据的长度。
* x 为 127：后续 8 个字节代表一个 64 位的无符号整数（最高位为 0），该无符号整数的值为数据的长度。

如果 payload length 占用了多个字节的话，payload length 的二进制表达采用网络序（big endian，重要的位在前）。

#### Masking-key
占 0 或 4 字节（32 位）。所有从客户端传送到服务端的数据帧，数据载荷都进行了掩码操作，Mask 为 1，且携带了 4 字节的 Masking-key。如果 Mask 为 0，则没有 Masking-key。

> 备注：载荷数据的长度，不包括 mask key 的长度。

#### Payload data
占 (x+y) 字节。

载荷数据：包括了扩展数据、应用数据。其中，扩展数据 x 字节，应用数据 y 字节。

扩展数据：如果没有协商使用扩展的话，扩展数据数据为 0 字节。所有的扩展都必须声明扩展数据的长度，或者可以如何计算出扩展数据的长度。此外，扩展如何使用必须在握手阶段就协商好。如果扩展数据存在，那么载荷数据长度必须将扩展数据的长度包含在内。

应用数据：任意的应用数据，在扩展数据之后（如果存在扩展数据），占据了数据帧剩余的位置。载荷数据长度 减去 扩展数据长度，就得到应用数据的长度。

### 数据传递
一旦 WebSocket 客户端、服务端建立连接后，后续的操作都是基于数据帧的传递。WebSocket 根据 opcode 来区分操作的类型。比如 0x8 表示断开连接，0x0 - 0x2 表示数据交互。

WebSocket 的每条消息可能被切分成多个数据帧。当 WebSocket 的接收方收到一个数据帧时，会根据 FIN 的值来判断，是否已经收到消息的最后一个数据帧。

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

* 发送方->接收方：ping
* 接收方->发送方：pong

ping、pong 的操作，对应的是 WebSocket 的两个控制帧，opcode 分别是 0x9、0xA。

``` js
//服务端向客户端发送 ping (采用 ws 模块)
ws.ping('', false, true);
```

### 关闭连接
一旦发送或接收到一个 Close 控制帧，这就是说，_WebSocket 关闭阶段握手已启动，且 WebSocket 连接处于 CLOSING 状态。

当底层 TCP 连接已关闭，这就是说 WebSocket 连接已关闭 且 WebSocket 连接处于 CLOSED 状态。 如果 TCP 连接在 WebSocket 关闭阶段已经完成后被关闭，WebSocket 连接被说成已经完全地关闭了。

如果 WebSocket 连接不能被建立，这就是说，WebSocket 连接关闭，但不是完全的 。

### 状态码
当关闭一个已经建立的连接（例如，当在打开阶段握手已经完成后发送一个关闭帧），端点可以表明关闭的原因。 由端点解释这个原因，并且端点应该给这个原因采取动作，本规范是没有定义的。 本规范定义了一组预定义的状态码，并指定哪些范围可以被扩展、框架和最终应用使用。 状态码和任何相关的文本消息是关闭帧的可选的组件。

### Sec-WebSocket-Key/Accept的作用
主要作用在于提供基础的防护，减少恶意连接、意外连接。

* 避免服务端收到非法的websocket连接（比如http客户端不小心请求连接websocket服务，此时服务端可以直接拒绝连接）；
* 确保服务端理解 websocket 连接。因为 ws 握手阶段采用的是 http 协议，因此可能 ws 连接是被一个 http 服务器处理并返回的，此时客户端可以通过 Sec-WebSocket-Key 来确保服务端认识 ws 协议。（并非百分百保险，比如总是存在那么些无聊的 http 服务器，光处理 Sec-WebSocket-Key，但并没有实现 ws 协议。。。）；
* 用浏览器里发起 ajax 请求，设置 header 时，Sec-WebSocket-Key 以及其他相关的 header 是被禁止的。这样可以避免客户端发送 ajax 请求时，意外请求协议升级 （websocket upgrade）；
* 可以防止反向代理返回错误的数据。比如反向代理前后收到两次 ws 连接的升级请求，反向代理把第一次请求的返回给 cache 住，然后第二次请求到来时直接把 cache 住的请求给返回（无意义的返回）；
* Sec-WebSocket-Key 主要目的并不是确保数据的安全性，因为 Sec-WebSocket-Key、Sec-WebSocket-Accept 的转换计算公式是公开的，而且非常简单，最主要的作用是预防一些常见的意外情况（非故意的）。

``` js
//验证前面的返回结果
const crypto = require('crypto');
const magic = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11';
const secWebSocketKey = 'w4v7O6xFTi36lq3RNcgctw==';

let secWebSocketAccept = crypto.createHash('sha1')
	.update(secWebSocketKey + magic)
	.digest('base64');

console.log(secWebSocketAccept);
// Oy4NRAQ13jhfONC7bP8dTKb4PTU=
```

### 数据掩码的作用
WebSocket 协议中，数据掩码的作用是增强协议的安全性。但数据掩码并不是为了保护数据本身，因为算法本身是公开的，运算也不复杂。

## 客户端 API
### WebSocket 构造函数
WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例，提供了很多可以通过该连接发送和接收数据的 API。

``` js
new WebSocket(url, protocols);
```

执行上面语句之后，客户端就会与服务器进行连接。

* url：表示要连接的 URL，这个URL应该为响应 WebSocket 的地址。

* protocols：可选参数，可以是一个单个的协议名字字符串或者包含多个协议名字字符串的数组。这些字符串用来表示子协议，这样做可以让一个服务器实现多种 WebSocket 子协议（例如你可能希望通过制定不同的协议来处理不同类型的交互）。如果没有制定这个参数，它会默认设为一个空字符串。

#### webSocket.binaryType
一个字符串表示被传输二进制的内容的类型。取值应当是"blob"或者"arraybuffer"。"blob"表示使用DOM Blob 对象，而"arraybuffer"表示使用 ArrayBuffer 对象。

#### webSocket.bufferedAmount
只读属性，调用 send() 方法将多字节数据加入到队列中等待传输，但是还未发出。该值会在所有队列数据被发送后重置为 0。而当连接关闭时不会设为 0。如果持续调用 send()，这个值会持续增长。简单理解就是还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。

``` js
var data = new ArrayBuffer(10000000);
ws.send(data);

if (ws.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

#### webSocket.extensions
服务器选定的扩展。目前这个属性只是一个空字符串，或者是一个包含所有扩展的列表。

#### webSocket.protocol
一个表明服务器选定的子协议名字的字符串。这个属性的取值会被取值为构造器传入的 protocols 参数。

#### webSocket.url
只读属性，传入构造器的 URL。它必须是一个绝对地址的 URL。

#### webSocket.readyState
只读属性，返回实例对象连接的当前状态。

* CONNECTING：值为 0 ，表示 正在连接。
* OPEN：值为 1	 ，表示 连接成功，可以通信了。
* CLOSING：值为 2，表示 连接正在关闭。
* CLOSED：值为 3 ，表示 连接已经关闭，或者连接无法建立。

``` js
switch (ws.readyState) {
  case WebSocket.CONNECTING:
    // do something
    break;
  case WebSocket.OPEN:
    // do something
    break;
  case WebSocket.CLOSING:
    // do something
    break;
  case WebSocket.CLOSED:
    // do something
    break;
  default:
    // this never happens
    break;
}
```

#### webSocket.onopen
实例对象的 onopen 属性，用于指定连接成功后的回调函数，当 readyState 的值变为 OPEN 的时候会触发该事件。该事件表明这个连接已经准备好接受和发送数据。这个监听器会接受一个名为 open 的事件对象。

``` js
var ws = new WebSocket('ws://localhost:8080');

ws.onopen = function () {
  ws.send('Hello Server!');
}
```

如果要指定多个回调函数，可以使用 addEventListener 方法:

``` js
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
```

#### webSocket.onclose
实例对象的 onclose 属性，用于指定连接关闭后的回调函数，当 WebSocket 对象的 readyState 状态变为 CLOSED 时会触发该事件。这个监听器会接收一个叫 close 的 CloseEvent 对象。

``` js
ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```

#### webSocket.onmessage
实例对象的 onmessage 属性，用于指定收到服务器数据后的回调函数，这一事件当有消息到达的时候该事件会触发。这个 Listener 会被传入一个名为 message 的 MessageEvent 对象。

``` js
ws.addEventListener("message", function(event) {
  var data = event.data;
  // 处理数据
});
```

服务器数据可能是文本，也可能是 二进制数据（blob 对象或 Arraybuffer 对象）:

``` js
ws.onmessage = function(event) {
  if(typeof event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer) {
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```

除了动态判断收到的数据类型，也可以使用 binaryType 属性，显式指定收到的二进制数据类型。

``` js
// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
  console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```

#### webSocket.onerror
当错误发生时用于监听 error 事件的事件监听器。会接受一个名为 error 的 event 对象。

``` js
ws.addEventListener("error", function(event) {
  // handle error event
});
```

#### close()
关闭 WebSocket 连接或停止正在进行的连接请求。如果连接的状态已经是 closed，这个方法不会有任何效果。

``` js
close(code, reason);
```

* code：可选,一个数字值表示关闭连接的状态号，表示连接被关闭的原因。如果这个参数没有被指定，默认的取值是1000 （表示正常连接关闭）。

* reason：可选，一个可读的字符串，表示连接被关闭的原因。这个字符串必须是不长于123字节的 UTF-8 文本（不是字符）。

异常提示：

* INVALID_ACCESS_ERR：选定了无效的code。
* SYNTAX_ERR：reason 字符串太长或者含有 unpaired surrogates。

#### webSocket.send()
通过 WebSocket 连接向服务器发送数据。支持三种数据类型：DOMString、ArrayBuffer、Blob。

``` js
/* 发送文本 */
ws.send('your message');

/* 发送 Blob 对象 */
var file = document
  .querySelector('input[type="file"]')
  .files[0];
ws.send(file);

/* 发送 ArrayBuffer 对象 */
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```

异常提示：

* INVALID_STATE_ERR：当前连接的状态不是OPEN。
* SYNTAX_ERR：数据是一个包含 unpaired surrogates 的字符串。

## 服务端的实现
常用的 Node 实现有以下三种。

* [Socket.IO](https://socket.io)
* [µWebSockets](https://github.com/uNetworking/uWebSockets)
* [WebSocket-Node](https://github.com/theturtle32/WebSocket-Node)
