# 认识 AJAX
AJAX 的全称是 Asynchronous JavaScript and XML，是指异步 JavaScript 和 XML，它有别于传统 web 开发中采用的同步的方式。

### 优势
* 无需刷新页面请求数据，可以使产品更快、更小、更友好；
* 可以把以前服务端的任务转嫁到客户端来处理，减轻服务器负担，节省带宽；
* 浏览器支持好，无需插件。

### 劣势
* 不支持浏览器的回退按钮；
* 安全性存在问题，能够在用户不知情的情况下发送请求；
* 暴露了 http 交互细节；
* 对搜索引擎(网络爬虫)的支持比较弱；
* 程序不容易调试。

## AJAX 原理
AJAX 的原理简单来说通过 XmlHttpRequest 对象来向服务器发异步请求，从服务器获得数据，然后用 JavaScript 来操作 DOM 而更新页面。

XMLHttpRequest 是 AJAX 的核心机制，它是在 IE5 中首先引入的，是一种支持异步请求的技术。

简单的说，就是 JavaScript 可以及时向服务器提出请求和处理响应，而不阻塞用户，达到无刷新的效果。

> AJAX 必须在服务器环境下才能正常使用。

## 理解同步和异步
* 同步: 客户端发起请求 –> 等待 –> 服务器端处理 —> 等待 –> 响应 –> 页面载入 (请求错误时全部重新载入)。

* 异步: 客户端发起请求 —> 服务器端处理 —> 响应 —> 页面载入(填写时，即时更新，部分返回)。


## JavaScript AJAX
### XMLHttpRequest 对象
主要用来处理服务器端和客户端的通信，它有以下属性：

* onreadystatechange：一个JavaScript函数对象，当readyState属性改变时会调用它。

* responseText：从服务器进程返回数据的字符串形式。

* responseXML：从服务器进程返回的 XML 数据对象。

* status：从服务器返回的数字代码，比如常见的 404（未找到）和 200（已就绪）。

* statusText：伴随状态码的字符串信息。当状态为 200 的时候它是 "OK"，当状态为 404 的时候它是 "Not Found"。

* readyState：对象状态值。

对象状态值包含以下几种：　

* 0：未初始化。未调用 open() 方法。
* 1：启动。已经调用 open() 方法，未调用 send() 方法。
* 2：发送。已经调用 send() 方法，未接收到响应。
* 3：接收。已经接收到部分数据。
* 4：完成。已经接收到全部数据，可以在客户端使用。

### 取消
使用 abort() 来取消当前响应，关闭连接并且结束任何未决的网络活动。当请求相应时间超长时，可以调用此方法，然后将其异步请求取消。如：

``` js
var iscancel = setTimeout(function(){xhr.abort();},5000);
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status ==200){
        alert(xhr.responseText);
　　  clearTimeout(iscancel);
　　}
}
```

### 同步或异步
AJAX 不一定是异步的，可以通过 open 方法的第三个参数来配置(默认为true，异步)。

### 生成时间戳
为了防止缓存(304)，调用 open 时，在第二个参数请求地址后添加一个随机数，保证每次访问的地址不同，避免因为缓存导致请求的文件发生改变，而页面并未随之改变(因为使用了缓存的数据)。

``` js
xhr.open("get", "url?" + Math.random(), true);
```

### post 请求和 get 请求的区别
``` js
xhr.open("post", "请求文件", true);

// 设置请求头部发送时的文本格式，因为 post 方法只能通过表单格式发送
xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

// send 方法中的参数是报文体，而 post 方法传递是通过报文体，调用 send 方法传递的参数是以 kv 对形式的字符串，类似 query string
xhr.send("k=v&k=v");
```

原生的 post 是将传递的参数放到 send() 中以 form data 形式传递，而原生的 get 方法是将数据放到第二个参数的路径 `?` 后面，以查询字符串的形式传递，这和 jQuery 的传递方法不同，jQuery中的 `$.get()` 和 `$.post()` 都是通过第二个参数以 json 的形式传递到接口页面的。

但是通常参数是以 json 形式传递的，如何将 json 对象转换为键值对形式的字符串？

``` js
var data = {
	"names": "Tom",
	"age": 19,
	"sex": "男"
}

// 封装一个格式转换函数
function changeToString(JSON){
	var tempArr = [];
	for(var k in JSON){
		// url 只允许英文、数字和特殊字符，当有中文或其他国家语言出现时，要通过 uri (统一资源标识符)来转换
		tempArr.push(k + "=" + encodeURIComponent(JSON[k]));
	}
	return temp.join("&");
}

// 调用函数转换格式
xhr.send(changeToString(data));
```

### 数据转化
从后台发送来的数据，前端并不知道数据是什么格式，也就不能直接拿来就使用，要先进行判断。

方法一：使用 `JSON.parse()` 方法将字符串转换为 json 对象，但是有兼容问题存在，IE6，7，8 不兼容这个方法。

``` js
// data 是从后台传回的 response 数据，要先判断是 JSON 对象还是 JSON 对象形式的字符串
var dataObj = typeof data == "object" ? data : JSON.parse(data);
```

方法二：使用 `eval()` 方法，没有兼容问题，`eval()` 可以将 str 变成执行语句，但是转换对象形式的字符串要注意，字符串外面必须加括号。

``` js
var dataObj = typeof data == "object" ? data : eval("("+ data +")");
```

方法三：使用内置构造函数 `Function()`, 通过构造函数 new 的实例立即执行返回 json 对象。

``` js
var dataObj = typeof data == "object" ? data : (new Function("return" + data))();
```

### AJAX 封装
#### 简单的 GET 请求封装
``` js
/**
 * 一个简单的get请求
 * @param {String}   url     请求地址,文件名
 * @param {Function} fnSucc  请求成功时执行的函数
 * @param {Function} fnFaild 请求失败执行的函数
 */
function AJAX(url, fnSucc, fnFaild) {
    //1.创建ajax对象
    var oAjax = null;
     
    if (window.XMLHttpRequest) {
        //IE6以上
        oAjax = new XMLHttpRequest();
    } else {
        oAjax = new ActiveXObject("Microsoft.XMLHTTP");
    }
    //2.连接服务器
    //open(方法,url,是否异步)
    oAjax.open("GET", url, true);
    //3.发送请求
    oAjax.send();
    //4.接收返回
    //OnRedayStateChange事件
    oAjax.onreadystatechange = function () {
        if (oAjax.readyState === 4) {
            if (oAjax.status === 200) {
                //                alert("成功" + oAjax.responseText);
                fnSucc(oAjax.responseText);
            } else {
                //                alert("服务器响应失败!");
                if (fnFaild) {
                    fnFaild();
                }
            }
        }
    };
}
```

#### 改造后的封装
``` js
/**
 * AJAX函数封装
 * @param {string} url     请求地址（必须）
 * @param {object} options 发送请求的选项参数
 *   @config {string} [options.type] 请求发送的类型。默认为GET。
 *   @config {Object} [options.data] 需要发送的数据。
 *   @config {Function} [options.onsuccess] 请求成功时触发，function(oAjax.responseText, oAjax)。（必须）
 *   @config {Function} [options.onfail] 请求失败时触发，function(oAjax)。(oAJax为XMLHttpRequest对象)
 *
 *@returns {XMLHttpRequest} 发送请求的XMLHttpRequest对象
 */
function AJAX(url, options) {
    //1.创建ajax对象
    var oAjax = null;
        
    if (window.XMLHttpRequest) {
        //IE6以上
        oAjax = new XMLHttpRequest();
    } else {
        oAjax = new ActiveXObject("Microsoft.XMLHTTP");
    }
    
    //2.连接服务器
    //open(方法,url,是否异步)
    var param = ""; //请求参数。
    
    //只有data存在，且为对象使才执行
    var data = options.data ? options.data : -1; //缓存data
    if (typeof (data) === "object") {
        for (var key in data) { //请求参数拼接
            if (data.hasOwnProperty(key)) {
                param += key + "=" + data[key] + "&";
            }
        }
        param.replace(/&$/, "");
    } else {
        param = "timestamp=" + new Date().getTime();
    }
    
    //3.发送请求
    var type = options.type ? options.type.toUpperCase() : "GET";
    if (type === "GET") {
        oAjax.open("GET", url + "?" + param, true);
        oAjax.send();
    } else {
        oAjax.open("POST", url, true);
        oAjax.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
        oAjax.send(param);
    }
    
    //4.接收返回
    //OnRedayStateChange事件
    oAjax.onreadystatechange = function () {
        if (oAjax.readyState === 4) {
            if (oAjax.status === 200) {
                //请求成功。形参为获取到的字符串形式的响应数据
                options.onsuccess(oAjax.responseText, oAjax);
            } else {
                //先判断是否存在请求失败函数
                //存在时，形参为XMLHttpRequest对象，便于进行错误进行处理
                if (options.onfail) {
                    options.onfail(oAjax);
                }
            }
        }
    };
    return oAjax;//发送请求的XMLHttpRequest对象
}
```

## 跨域
在浏览器里只要满足以下条件任一个就算跨域：

* 请求协议不同，例：http和https
* 域名不同，例：xxx.com和mmm.com
* 端口不同，例：xxx.com和xxx.com:81

### 解决跨域的方法
### jsonp
JSONP 实现跨域的原理是动态创建 script 标签，src 是具有跨域访问文件的能力，创建指定的地址和调用特定的回调方法获取数据，JSONP 只支持 get 方法。

``` js
// 基于 jQuery
$.ajax({
  url: 'http://localhost:8080/getUser?callback=?',
  dataType: 'jsonp',
  jsonpCallback: 'person',
  success: function(data) {
    //...
  }
});
```

### CORS
CORS（Cross-Origin Resource Sharing）中文名叫 跨域资源共享。实现跨域的原理是在 http 请求头中加上指定的标记来告诉浏览器是否允许加载跨域的资源，也是现在主流的跨越解决方案。

``` js
Access-Control-Allow-Origin: <origin> | *
Access-Control-Allow-Credentials: true // 是否允许浏览器读取response的内容
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```

#### Access-Control-Allow-Origin
该头部是客户端发起的请求的一部分，包含了应用所在的域。由于安全原因，浏览器不会允许用户重写这个值。

#### Access-Control-Allow-Credentials
该头部只需要在服务器支持通过 cookie 认证的情况下出现在响应中。这种情况下，其唯一合法值就是 true。

#### Access-Control-Allow-Headers
格式为一个逗号分隔的列表，表示服务器将会支持的请求头部值。如果使用了自定义头部（比如 x-authentication-token），则应该将其置于这个头部响应中，并返回到 OPTIONS 调用中；除非该请求被阻塞了。

#### Access-Control-Expose-Headers
相似的是，该响应应包含一个头部列表，表示将在实际的响应中出现的值，并应在客户端中有效。所有其他头部则会被限制。

### iframe + location.hash
iframe+location.hash 实现跨域是使用 iframe 加载资源，然后在 iframe 中修改父窗口的 location.hash，因为 location.hash 的信息会展示在 url 上，所以 url 的长度限制了我们传输信息内容的长度。

###  iframe + window.name
iframe+location.hash 实现跨域是使用 iframe 加载资源，然后在 iframe 窗口加载的内容修改 window.name 的值，使用了 window.name 的特性，在当前窗口页所有加载的页面共享一个 window.name，但是 window.name 的容量限制为不超过2m。

### iframe + document.domain
如果两个页面的主域名相同，但是子域名不同，可以修改 document.domain 为同一个域名，实现父子域名的跨域通信，只限制主域名相同的情况下。（不算严格意义上的跨域）

### postMessage
postMessage 是 html5 标准的新特性，使用该 api 可以实现多种场景的跨域通信，但是在一些比较老旧的浏览器可能不支持此方法。

### 反向代理
使用 nginx 或者 nodejs 中间件通过反向代理实现跨域访问。

### WebSocket 协议
WebScoket 协议支持跨域通信。

## fetch
默认不带 cookie。

``` js
fetch(url, {
  method: 'POST',
  body: Json.stringify(comment)
})
.then((res)=> {
  console.error(res);
})
.catch((e)=>{
  console.error(e)
})
```

解决浏览器兼容，引用 polyfill：

``` bash
# 安装好后 import
npm install --save whatwg-fetch
```

fetch 也有不少的问题：

* 兼容性问题
* 使用繁琐，详见参考文献之 [fetch 没有你想象的那么美](http://undefinedblog.com/window-fetch-is-not-as-good-as-you-imagined/)
* 不支持 jsonp（虽然理论上不应该支持，但实际上日常还是需要使用的）
* 只对网络请求报错，对400，500都当做成功的请求，需要二次封装
* 默认不会带 cookie，需要添加配置项
* 不支持 abort，不支持超时控制，使用 setTimeout 及 Promise.race 的实现* 的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
* 没有办法原生监测请求的进度，而 XHR 可以

## axios 库
Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

### 安装
``` bash
npm install axios
```

### GET 请求
``` js
// 语法：
axios.get(url[, config])

// url 中带有 query 参数
axios.get('/user?id=89')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// url 和参数分离，使用对象
axios.get('/user', {
  params: {
    id: 12345
  }
})
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

### POST 请求
``` js
// 语法
axios.post(url[, data[, config]])

// 示例
axios.post('/user', { 
  name: 'faychou' 
})
  .then((response) => {
    // do something
  })
  .catch((error) => {
    console.log(error);
  });
```

防止 CSRF，那我们需要在请求中的 headers 加上 X-XSRF-TOKEN：

``` js
axios.post('/user', { name: 'faychou' }, {
  headers: {
    'X-XSRF-TOKEN': 'xxxxxxxx',
  },
}).then((result) => {
  // do something
});
```

默认情况下，axios 会将JS对象序列化为 JSON 对象。为了使用 `application/x-www-form-urlencoded` 格式发送请求，我们可以这样：

``` js
// 使用 qs 包，将对象序列化为字符串，该模块不需要安装
// var qs = require('qs')
import qs from 'qs'
const data = qs.stringify({ 'bar': 123 }) // ===> "bar=123"
axios.post('/foo', data)

// 或者：
axios.post('/foo', 'bar=123&age=19')
```

### 多个并发请求
可以通过 axios.all 发起多个并发请求，比如说一次性获取两条数据：

``` js
function getHTTP1() {
  return axios.get('https://api.faychou.cn/1');
}

function getHTTP2() {
  return axios.get('https://api.faychou.cn/2');
}

axios.all([
  getHTTP1(), 
  getHTTP2()
])
.then(axios.spread((response1, response2) => {
  // 两个请求现在都执行完成
  console.log(response1.data);
  console.log(response2.data);
}))
.catch(error => {
  console.log(error);
});
```

### axios API
可以通过向 axios(config) 传递相关配置来创建请求：

``` js
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});

// or
axios(url[, config])
```

其他请求：

* axios.request(config)

* axios.delete(url[, config])

* axios.head(url[, config])

* axios.put(url[, data[, config]])

* axios.patch(url[, data[, config]])

### 创建实例
可以使用自定义配置新建一个 axios 实例：

``` js
axios.create([config])

var instance = axios.create({
  baseURL: 'https://some-domain.com/api/', // 自动添加请求前缀地址
  timeout: 1000, // 超时
  headers: {'X-Custom-Header': 'foobar'} // 请求头
});
```

### 配置
默认配置：

``` js
// 设置请求地址前缀
axios.defaults.baseURL = 'https://www.faychou.cn';

// 请求头
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;

// post 请求头设置
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

其他配置选项，url 是必需的，如果没有指定 method，请求将默认使用 get 方法：

``` js
{
  // 请求的服务器 URL
  url: '/user',

  // 请求方法
  method: 'post', // 默认 get

  // baseURL 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL
  baseURL: 'https://www.faychou.cn/api/',

  // 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data) {
    // 对 data 进行任意转换处理
    return qs.stringify(data)
  }],

  // 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // 自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // 与请求一起发送的 URL 参数
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    ID: 12345
  },

  // 一个负责 params 序列化的函数
  // qs 模块
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // 发送的数据
  // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
  // 在没有设置 `transformRequest` 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fay'
  },

  // 请求超时的毫秒数(0 表示无超时时间)
  // 如果请求超过 timeout 的时间，请求将被中断
  timeout: 1000,

  // 表示跨域请求时是否需要使用凭证
  withCredentials: false, // 默认的

  // 允许自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应
  adapter: function (config) {
    //
  },

  // 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 Authorization 头，覆写掉现有的任意使用 headers 设置的自定义 Authorization 头
  auth: {
    username: 'FayChou',
    password: '123456'
  },

  // 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // 默认的

  //  用作 xsrf token 的值的 cookie 的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // 承载 xsrf token 的值的 HTTP 头的名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // 默认的

  // 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

  // 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

  // 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。
  // 如果返回 true (或者设置为 null 或 undefined)，promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // 默认的
  },

  // 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为 0，将不会 follow 任何重定向
  maxRedirects: 5, // 默认的

  // httpAgent 和 httpsAgent 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // keepAlive 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // proxy 定义代理服务器的主机名称和端口
  // auth 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 Proxy-Authorization 头，覆写掉已有的通过使用 `header` 设置的自定义 Proxy-Authorization 头。
  proxy: {
    host: '127.0.0.1',
    port: 8080,
    auth: : {
      username: 'admin',
      password: 'admin'
    }
  },

  // 指定用于取消请求的 cancel token
  cancelToken: new CancelToken(function (cancel) {
    //...
  })
}
```

### 拦截器
拦截器会拦截发送的每一个请求，请求发送之前执行 request 中的函数，请求发送完成之后执行 response 中的函数：

``` js
// 请求拦截器
axios.interceptors.request.use(function (config) {
    // 所有请求之前都要执行的操作

    return config;
  }, function (error) {
    // 错误处理

    return Promise.reject(error);
  });

// 响应拦截器
axios.interceptors.response.use(function (response) {
    // 所有请求完成后都要执行的操作

    return response;
  }, function (error) {
    // 错误处理
    return Promise.reject(error);
  });
```

如果你想在稍后移除拦截器，可以这样：

``` js
var myInterceptor = axios.interceptors.request.use(function () {
  /*...*/
});
axios.interceptors.request.eject(myInterceptor);
```

### 请求超时
请求过程中，如果服务器或者网络不稳定掉包了, 要解决这个问题，我们需要设置一个请求超时，当超时后重新请求。

``` js
// 设置全局的请求次数，请求的间隙
axios.defaults.retry = 4;
axios.defaults.retryDelay = 1000;

axios.interceptors.response.use(undefined, function axiosRetryInterceptor(err) {
    var config = err.config;
    // If config does not exist or the retry option is not set, reject
    if(!config || !config.retry) return Promise.reject(err);
    
    // Set the variable for keeping track of the retry count
    config.__retryCount = config.__retryCount || 0;
    
    // Check if we've maxed out the total number of retries
    if(config.__retryCount >= config.retry) {
        // Reject with the error
        return Promise.reject(err);
    }
    
    // Increase the retry count
    config.__retryCount += 1;
    
    // Create new promise to handle exponential backoff
    var backoff = new Promise(function(resolve) {
        setTimeout(function() {
            resolve();
        }, config.retryDelay || 1);
    });
    
    // Return the promise in which recalls axios to retry the request
    return backoff.then(function() {
        return axios(config);
    });
});
```

### 错误处理
``` js
axios.get('/user/12345')
  .catch(function (error) {
    if (error.response) {
      // 请求已发出，但服务器响应的状态码不在 2xx 范围内
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else {
      // Something happened in setting up the request that triggered an Error
      console.log('Error', error.message);
    }
    console.log(error.config);
  });
```

可以使用 validateStatus 配置选项定义一个自定义 HTTP 状态码的错误范围：

``` js
axios.get('/user/12345', {
  validateStatus: function (status) {
    return status < 500; // 状态码在大于或等于500时才会 reject
  }
});
```

### 取消请求
使用 CancelToken 取消请求：

``` js
var CancelToken = axios.CancelToken;
var source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function(thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // 处理错误
  }
});

// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.');
```

还可以通过传递一个 executor 函数到 CancelToken 的构造函数来创建 cancel token：

``` js
var CancelToken = axios.CancelToken;
var cancel;

axios.get('/user/12345', {
  cancelToken: new CancelToken(function executor(c) {
    // executor 函数接收一个 cancel 函数作为参数
    cancel = c;
  })
});

// 取消请求
cancel();
```

### 二次封装 axios 示例一
``` js
import axios from 'axios';
import Qs from 'qs';


function checkStatus(err) {
    let msg = "", level = "error";
    switch (err.response.status) {
      case 401:
        msg = "您还没有登陆";
        break;
      case 403:
        msg = "您没有该项权限";
        break;
      case 404:
        msg = "资源不存在";
        break;
      case 500:
        msg = "服务器发生了点意外";
        break;
    }
    try {
      msg = res.data.msg;
    } catch (err) {
      //
    } finally {
      if (msg !== "" && msg !== undefined && msg !== null) {
        store.dispatch('showSnackBar', {text: msg, level: level});
      }
    }
    return err.response;
  }
  
  function checkCode(res) {
    if ((res.status >= 200 && res.status < 300) && (res.data.status >= 200 && res.data.status < 300)) {
      let msg = "", level = "success";
      switch (res.data.status) {
        case 201:
          msg = "创建成功";
          break;
        case 204:
          msg = "删除成功";
          break;
      }
      try {
        msg = res.data.success;
      } catch (err) {
        // ...
      } finally {
        
      }
      return res;
    }

    return res;
  
  }

//这里封装axios的get,post,put,delete等方法
export default {
    get(url, params) {
      return axios.get(
        url,
        params,
      ).then(checkCode).catch((error)=>{console.log(error)});
    },
    post(url, data) {
      return axios.post(
        url,
        Qs.stringify(data),
      ).then(checkCode).catch(checkStatus);
    },
    put(url, data) {
      return axios.put(
        url,
        Qs.stringify(data),
      ).then(checkCode).catch(checkStatus);
    },
    delete(url, data) {
      return axios.delete(
        url,
        {data: Qs.stringify(data)},
      ).then(checkCode).catch(checkStatus);
    },
    patch(url, data) {
      return axios.patch(
        url,
        data,
      ).then(checkCode).catch(checkStatus);
    },
  };
```
