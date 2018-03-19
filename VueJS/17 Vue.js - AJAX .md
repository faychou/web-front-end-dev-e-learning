# Vue AJAX
## vue-resource(作者已停止更新)

## axios（官网推荐）
### 使用方法一：
步骤一：

	npm install --save axios

步骤二：

``` js
// 在main.js中引入

import axios from 'axios'
Vue.prototype.$http = axios
```

步骤三：

``` js
// 在组件中使用axios

this.$http.get(URL).then(response => {
  // success callback
}, response => {
  // error callback
})
```

### 使用方法二：
步骤一：

	npm install --save axios vue-axios

步骤二：

``` js
// 在main.js中引入

import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios, axios)
```

步骤三：

``` js
// 在组件中使用axios

this.axios.get('/api/user').then((response) => {
    console.log(response.data)
}).catch((error) => {
    console.error(error)
})
```

axios 更详细的使用方法 => [(官网入口)](https://github.com/mzabriskie/axios)