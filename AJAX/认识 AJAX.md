# 认识 AJAX
AJAX 的全称是 Asynchronous JavaScript and XML，是指异步 JavaScript 和 XML，它有别于传统 web 开发中采用的同步的方式。

## AJAX 原理
AJAX 的原理简单来说通过 XmlHttpRequest 对象来向服务器发异步请求，从服务器获得数据，然后用 JavaScript 来操作 DOM 而更新页面。

XMLHttpRequest 是 AJAX 的核心机制，它是在 IE5 中首先引入的，是一种支持异步请求的技术。

简单的说，就是 JavaScript 可以及时向服务器提出请求和处理响应，而不阻塞用户，达到无刷新的效果。

## HTTP
### 一个完整的 HTTP 请求过程
1. 建立 TCP 连接；
2. Web 浏览器向 Web 服务器发送请求命令；
3. Web 浏览器发送请求头信息；
4. Web 服务器- 应答；
5. Web 服务器- 发送应答头信息；
6. Web 服务器- 向浏览器发送数据；
7. Web 服务器- 关闭 TCP 连接。

> AJAX 必须在服务器环境下才能正常使用。

### HTTP 请求
* HTTP 请求的方法或动作如是 GET 还是 POST 请求；

* 正在请求的 URL，总得知道请求的地址是什么吧；

* 请求头，包含一些客户端环境信息，身份验证信息等；

* 请求体，也就是请求正文，请求正文中可以包含客户端提交的查询字符串信息，表单信息等等。

### HTTP 响应
* 一个数字和文字组成的状态码，用来显示请求是成功还是失败；

* 响应头，响应头和请求头一样包含许多有用的信息，例如服务器类型，日期时间，内容类型和长度等；

* 响应体，也就是响应正文。

### HTTP 请求方式
|  请求方式  |         用途        |                安全性             |        大小      |
| --------- | ------------------ | -------------------------------- | ---------------- |
|    GET    |   用于信息获取/查询   |  安全性低(使用url传递参数所有人可见)  | 容量低(2000个字符) |
|    POST   | 用于修改服务器上的资源 |              安全性一般            |    容量几乎无限    |
|    PUT    | 在服务器更新资源（客户端提供完整资源数据） |               |      |
|    DELETE  |   从服务器删除资源   |                       |         |
|    HEAD   |  从服务器获取报头信息（不是资源）|  |  |

### 媒体格式类型
* application/json： JSON数据格式

* application/x-www-form-urlencoded：url 参数形式

* multipart/form-data：from表单

* text/plain：纯文本的方式

## 理解同步和异步
* 同步: 客户端发起请求 –> 等待 –> 服务器端处理 —> 等待 –> 响应 –> 页面载入 (请求错误时全部重新载入)。

* 异步: 客户端发起请求 —> 服务器端处理 —> 响应 —> 页面载入(填写时，即时更新，部分返回)。

### 常见的 HTTP 状态码
| 状态码 |	                         描述                        |        短语       |
| ----- | ---------------------------------------------------- | ---------------- |
|  200	 |  请求成功。一般用于 GET 和 POST 方法                     |  OK
|  301	 |  资源移动。所请求资源移动到新的 URL，浏览器自动跳转到新的 URL | Moved Permanently
|  304	 |  未修改。所请求资源未修改读取缓存数据                      | Not Modified
|  400	 |  请求语法错误，服务器无法理解	                          | Bad Request
|  404	 |  未找到资源，可以设置个性”404页面”	                       | Not Found
|  500	 |  服务器内部错误	                                        | internal Server Error

## JavaScript AJAX
### XMLHttpRequest 对象
主要用来处理服务器端和客户端的通信，它有以下属性：

* onreadystatechange：每次状态改变所触发事件的事件处理程序。

* responseText：从服务器进程返回数据的字符串形式。

* responseXML：从服务器进程返回的DOM兼容的文档数据对象。

* status：从服务器返回的数字代码，比如常见的404（未找到）和200（已就绪）。

* status Text：伴随状态码的字符串信息。

* readyState：对象状态值。

对象状态值包含以下几种：　

* 0：未初始化。未调用 open() 方法。
* 1：启动。已经调用 open() 方法，未调用 send() 方法。
* 2：发送。已经调用 send() 方法，未接收到响应。
* 3：接收。已经接收到部分数据。
* 4：完成。已经接收到全部数据，可以在客户端使用。

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
### 同源策略

### jsonp

### CORS
CORS（Cross-Origin Resource Sharing）中文名叫 跨域资源共享。

#### Access-Control-Allow-Origin
该头部是客户端发起的请求的一部分，包含了应用所在的域。由于安全原因，浏览器不会允许用户重写这个值。

#### Access-Control-Allow-Credentials
该头部只需要在服务器支持通过 cookie 认证的情况下出现在响应中。这种情况下，其唯一合法值就是 true。

#### Access-Control-Allow-Headers
格式为一个逗号分隔的列表，表示服务器将会支持的请求头部值。如果使用了自定义头部（比如 x-authentication-token），则应该将其置于这个头部响应中，并返回到 OPTIONS 调用中；除非该请求被阻塞了。

#### Access-Control-Expose-Headers
相似的是，该响应应包含一个头部列表，表示将在实际的响应中出现的值，并应在客户端中有效。所有其他头部则会被限制。

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

## axios 库
### Get 请求
``` js
const url = 'https://www.faychou.cn/api/list'

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
```

### Post 请求
默认情况下，axios 会将JS对象序列化为 JSON 对象。为了使用 `application/x-www-form-urlencoded` 格式发送请求，我们可以这样：

``` js
// 使用 qs 包，处理将对象序列化为字符串
// npm i -S qs
// var qs = require('qs')
import qs from 'qs'
qs.stringify({ 'bar': 123 }) ===> "bar=123"
axios.post('/foo', qs.stringify({ 'bar': 123 }))

// 或者：
axios.post('/foo', 'bar=123&age=19')
```

### 全局配置
``` js
// 设置请求公共路径：
axios.defaults.baseURL = 'https://www.faychou.cn'
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

### 请求超时
请求过程中，如果服务器或者网络不稳定掉包了, 要解决这个问题，我们需要设置一个请求超时，当超时后重新请求。

``` js
//在main.js设置全局的请求次数，请求的间隙
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
