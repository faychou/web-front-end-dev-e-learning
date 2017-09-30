# 认识 AJAX

## HTTP
### 一个完整的HTTP请求过程
1. 建立TCP连接
2. Web浏览器向Web服务器发送请求命令
3. Web浏览器发送请求头信息
4. Web服务器- 应答
5. Web服务器- 发送应答头信息
6. Web服务器- 向浏览器发送数据
7. Web服务器- 关闭TCP连接

> Ajax必须在服务器环境下才能正常使用

### HTTP请求
* HTTP请求的方法或动作如是GET还是POST请求；

* 正在请求的URL,总得知道请求的地址是什么吧；

* 请求头,包含一些客户端环境信息,身份验证信息等；

* 请求体,也就是请求正文,请求正文中可以包含客户端提交的查询字符串信息,表单信息等等。

### HTTP响应
* 一个数字和文字组成的状态码,用来显示请求是成功还是失败；

* 响应头,响应头和请求头一样包含许多有用的信息,例如服务器类型,日期时间,内容类型和长度等；

* 响应体,也就是响应正文。

### HTTP请求方式
|---- 请求方式 ----|---- 用途 ----|---- 安全性 ----|---- 大小 ----|
|---- GET ----|---- 用于信息获取/查询 ----|---- 安全性低(使用url传递参数所有人可见) ----|---- 容量低(2000个字符) ----|
|---- POST ----|---- 用于修改服务器上的资源 ----|---- 安全性一般 ----|---- 容量几乎无限 ----|
|----  ----|----  ----|----  ----|----  ----|
## 理解同步和异步
* 同步: 客户端发起请求–等待–>服务器端处理—等待–>响应–>页面载入 (请求错误时全部重新载入)。

* 异步: 客户端发起请求—>服务器端处理—>响应—>页面载入(填写时,即时更新,部分返回)。

### 常见的HTTP状态码
状态码	描述	原因短语
200	请求成功.一般用于GET和POST方法	OK
301	资源移动.所请求资源移动到新的URL,浏览器自动跳转到新的URL	Moved Permanently
304	未修改.所请求资源未修改读取缓存数据	Not Modified
400	请求语法错误,服务器无法理解	Bad Request
404	未找到资源,可以设置个性”404页面”	Not Found
500	服务器内部错误	internal Server Error

## AJAX封装
### 简单的 GET 请求封装
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

### 改造后的封装
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