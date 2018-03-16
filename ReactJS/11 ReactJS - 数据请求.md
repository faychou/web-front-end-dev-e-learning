# 数据请求
我们使用 axios 来请求服务器端的数据， axios 是基于 Promise 的 HTTP 请求客户端，可同时在浏览器和 node.js 中使用。

### 安装方法
```
npm install --save axios
```

### 引入
``` js
  import axios from 'axios'
```

### axios
``` js
// 发送 GET 请求 (默认)
axios('/user/12345');

// 发送 POST 请求
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```

### 其它 API
* axios.request(config)
* axios.get(url[, config])
* axios.delete(url[, config])
* axios.head(url[, config])
* axios.options(url[, config])
* axios.post(url[, data[, config]])
* axios.put(url[, data[, config]])
* axios.patch(url[, data[, config]])

### config配置
``` js
{
  // 向服务器发送请求的url
  url: '/user',

  // 请求方法，默认是GET方法
  method: 'get',

  // 基础URL路径，假如url不是绝对路径，如https://some-domain.com/api/v1/login?name=jack,那么向服务器发送请求的URL将会是baseURL + url
  baseURL: 'https://some-domain.com/api/',

  // 允许在请求发送到服务器之前修改该请求，此方法只适用于PUT、POST和PATCH方法中。而且，此方法最后必须返回一个string、ArrayBuffer或者Stream。
  transformRequest: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],

  // 允许在数据传递到then/catch之前修改response数据。此方法最后也要返回数据。
  transformResponse: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],

  // 发送自定义Headers头文件，头文件中包含了http请求的各种信息。
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // 发送请求的查询参数对象，对象中的数据会被拼接成url?param1=value1&param2=value2。
  params: {
    ID: 12345
  },

  // params参数序列化器
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // 发送POST、PUT或者PATCH请求的数据对象。
  data: {
    firstName: 'Fred'
  },

  // 请求超时设置，单位为毫秒。
  timeout: 1000,

  // 表明是否有跨域请求需要用到证书
  withCredentials: false, // default

  // 允许用户处理更易于测试的请求。返回一个Promise和一个有效的response。
  adapter: function (resolve, reject, config) {
    /* ... */
  },

  // 表明提供凭证用于完成http的身份验证。这将会在headers中设置一个Authorization授权信息。自定义Authorization授权要设置在headers中。
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  }

  // 表示服务器将返回响应的数据类型，
  // 有arraybuffer、blob、document、json、text、stream这6个类型，默认是json类似数据。
  responseType: 'json',

  // 用作 xsrf token 值的 cookie 名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // 带有 xsrf token 值 http head 名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

  // `progress` allows handling of progress events for 'POST' and 'PUT uploads'
  // as well as 'GET' downloads
  progress: function(progressEvent) {
    // Do whatever you want with the native progress event
  }
}
```

### 简单案例
GET 请求：

``` js
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (err) {
    console.log(err);
  });

// or
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (err) {
    console.log(err);
  });
```

POST 请求：

``` js
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (err) {
    console.log(err);
  });
```

发送多个并发请求：

``` js
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // Both requests are now complete
  }));
```

### 拦截器
可以在处理 then 或 catch 之前拦截请求和响应。

``` js
// 添加一个请求拦截器
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// 添加一个响应拦截器
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });

// 移除一个拦截器
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```