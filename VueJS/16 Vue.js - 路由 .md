# Vue 路由
## 安装
### 直接下载
可直接在 [https://unpkg.com/vue-router/dist/vue-router.js](https://unpkg.com/vue-router/dist/vue-router.js) 下载 vue-router.js 或者引用CDN。

``` html
<script src="vue.js"></script>
<script src="vue-router.js"></script>
```

### npm
	npm install vue-router --save
	
## 简单入门
``` js
// index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import App from './App'
import PageOne from './components/PageOne'
import PageTwo from './components/PageTwo'
// 也可使用下面的懒加载写法
//const PageOne = (resolve) => require(['./components/PageOne'],resolve)

Vue.use(VueRouter) //全局安装路由功能，如果使用全局的 script 标签，则无须如此

// 定义路径，每个路由应该映射一个组件
const routes = [
  { path: '/', component: PageOne },
  { path: '/two', component: PageTwo },
]

// 创建 router 实例，然后传 `routes` 配置
const router = new VueRouter({
  routes // （缩写）相当于 routes: routes
})

// 注入路由
new Vue({
  el: '#app',
  router,
  render: h => h(App)
})
```
``` html
<!-- App.vue -->
<template>
  <div id="app">
    <!-- router-link 定义跳转路径 -->
    <router-link to="/">one</router-link>
    <router-link to="/two">two</router-link>
    <br/>
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
  </div>
</template>
<script>
export default {
  data () {
    return {
    }
  }
}
</script>
```

``` html
<!-- PageOne.vue -->
<template>
  <div>
    <h1>page-one</h1>
  </div>
</template>
<script>
export default {
  data () {
    return {
    }
  }
}
</script>
```

``` html
<!-- PageTwo.vue -->
<template>
  <div>
    <h1>page-two</h1>
  </div>
</template>
<script>
export default {
  data () {
    return {
    }
  }
}
</script>
```

### 分离路由配置
一般我们会把路由配置信息 router 提取到一个单独的文件中，如 `route/index.js`：

``` js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

import PageOne from './components/PageOne.vue'
import PageTwo from './components/PageTwo.vue'

const routes = [
  { 
    path: '/',
    component: PageOne 
  },{ 
    path: '/two', 
    component: PageTwo
  }
]

export default new VueRouter({
  mode: 'history',
  routes
})
```

然后在 main.js 中引入： `import router from './route/index.js'`。

## 路由组件
### `<router-link>`
使用 router-link 组件来导航，它会被渲染成 a 标签，通过传入 `to` 属性指定链接地址。

``` html
<!-- 字符串 -->
<router-link to="home">Home</router-link>

<!-- 使用 v-bind 的 JS 表达式 -->
<router-link :to="'home'">Home</router-link>

<!-- 同上 -->
<router-link :to="{ path: 'home' }">Home</router-link>
  
<!-- 以上都会渲染成这样 -->
<a href="home">Home</a>

<!-- 动态路径，以下结果为 /home/1 -->
<router-link :to="'/home/'+id">{{$router.params.id}}</router-link>
<!-- 或者 -->
<router-link to="{name:'list',params: {id: id}}">home1</router-link>

<!-- 带查询参数，下面的结果为 /register?plan=private -->
<router-link :to="{ path: 'register', query: { plan: 'private' }}">{{$router.query.plan}}</router-link>
```

#### active-class
设置链接激活时使用的 CSS 类名，默认值: "router-link-active"。

#### replace
该属性会调用 `$router.replace()`，于是导航后不会留下 history 记录，默认值: false。

``` html
<router-link :to="{ path: '/abc'}" replace></router-link>
```

#### append
设置 append 属性后，则在当前（相对）路径前添加基路径。例如，我们从 /a 导航到一个相对路径 b，如果没有配置 append，则路径为 /b，如果配了，则为 /a/b。默认值: false。

``` html
<router-link :to="{ path: 'relative/path'}" append></router-link>
```

#### tag
有时候想要 `<router-link>` 渲染成其他标签，例如 `<li>`。 就可以使用 tag 属性指定标签，同样它还是会监听点击事件，触发导航。默认值: `<a>`。

``` html
<router-link to="/foo" tag="li">foo</router-link>

<!-- 渲染结果 -->
<li>foo</li>
```

#### exact
``` html
<!-- 这个链接只会在地址为 / 的时候被激活 -->
<router-link to="/" exact>
```

### `<router-view>`
路由匹配到的组件将在这里渲染。

## API
### `$router`
`$router` 是一个数组，里面包含路由的所有信息。我们可以在组件里通过调用 `this.$router` 来访问它。

``` js
export default {
  computed: {
    username () {
      // 我们很快就会看到 `params` 是什么
      return this.$route.params.username
    }
  },
  methods: {
    goBack () {
      window.history.length > 1
        ? this.$router.go(-1)
        : this.$router.push('/')
    }
  }
}
```

### `$router.push(location, onComplete?, onAbort?)`
当调用 `this.$router.push` 的时候可以进行路由的跳转，例如：登录成功后，通过该方法跳转到首页面。该方法其实相当于点击 `<router-link to="...">` ，然后会在内部调用 `this.$router.push(...)，向 history 栈添加一个新的记录。

``` js
// 字符串
this.$router.push('/home')

// 对象
this.$router.push({ path: 'home' })

// 命名的路由
this.$router.push({ name: 'user', params: { userId: 123 }})

// 带查询参数，变成 /register?plan=private
this.$router.push({ path: 'register', query: { plan: 'private' }})
```

onComplete 和 onAbort 函数作为第二个和第三个参数。这些回调函数将会在导航成功完成 (在所有的异步钩子被解析之后) 或终止 (导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行相应的调用。

> 注意：如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 `/users/1` -> `/users/2`)，你需要使用 beforeRouteUpdate 来响应这个变化 (比如抓取用户信息)。

### `$router.replace(location, onComplete?, onAbort?)`
跟 `$router.push` 很像，唯一的不同就是，它不会向 history 添加新记录，而是替换掉当前的 history 记录。

### `$router.go(n)`
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 `window.history.go(n)`。

``` js
// 在浏览器记录中前进一步，等同于 history.forward()
this.$router.go(1)

// 后退一步记录，等同于 history.back()
this.$router.go(-1)

// 前进 3 步记录
this.$router.go(3)

// 如果 history 记录不够用，那就默默地失败
this.$router.go(-100)
this.$router.go(100)
```

### `$router` 对象：
可以获取当前路由的相关信息：

* `$route.query`：URL 中查询参数；
* `$route.hash`：哈希值；
* `$route.path`：路径；
* `$route.fullPath`：完整路径；
* `$route.matched`
* `$route.name`
* `$route.meta`：路由元信息

## 命名路由
有时候，通过一个名称来标识一个路由显得更方便一些，特别是在链接一个路由，或者是执行一些跳转的时候。你可以在创建 Router 实例的时候，在 routes 配置中给某个路由设置名称。

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```

要链接到一个命名路由，可以给 router-link 的 to 属性传一个对象：

``` html
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
```

这跟调用 `$router.push()` 是一回事：

``` js
$router.push({ name: 'user', params: { userId: 123 }})
```

## 动态路由匹配
一个『路径参数』使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。

``` js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

像 `/user/for` 和 `/user/bar` 都将映射到该路由下。

| 模式        | 匹配路径   |  `$route.params`  |
| --------   | :-----:   | :----: |
| `/user/:username`        | `/user/evan`      |   `{ username: 'evan' }`    |
| `/user/:username/post/:post_id`     | `/user/evan/post/123` |   `{ username: 'evan', post_id: 123 }`  |

### 监听路由参数的变化
当使用路由参数时，例如从 `/user/foo` 导航到 `/user/bar`，原来的组件实例是直接复用而非销毁再创建，所以路由切换时不会调用组件的生命周期钩子。此时如果想对路由参数的变化作出响应的话，可以使用 watch 来监测 `$route` 对象：

``` js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

或者使用 2.2 中引入的 beforeRouteUpdate 守卫：

``` js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

## 重定向
重定向』的意思是，当用户访问 `/a` 时，URL 将会被替换成 `/b`，然后匹配路由为 `/b`。

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

重定向的目标也可以是一个命名的路由：

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
})
```

甚至是一个方法，动态返回重定向目标：

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```

> 注意：导航守卫并没有应用在跳转路由上，而仅仅应用在其目标上。在下面这个例子中，为 `/a` 路由添加一个 beforeEach 或 beforeLeave 守卫并不会有任何效果。

## 别名
`/a` 的别名是 `/b`，意味着，当用户访问 `/b` 时，URL 会保持为 `/b`，但是路由匹配则为 `/a`，就像用户访问 `/a` 一样。别名』的功能让你可以自由地将 UI 结构映射到任意的 URL，而不是受限于配置的嵌套路由结构。

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

## 路由匹配模式
``` js
new Router({
  routers:[],
  mode: "hash", //默认hash | history 可以达到隐藏地址栏hash值 | abstract，如果发现没有浏览器的 API 则强制进入
  linkActiveClass : "now" //当前匹配的导航链接将被自动添加now类
})
```

如果设置为 history ，则需要后台配置支持。因为我们的应用是个单页应用，如果后台没有正确的配置，当用户在浏览器直接访问 https://faychou.cn/home 可能就会返回 404。所以需要后台设置对于所有路径都会返回 index.html 文件然后前端在 Vue 应用里面覆盖所有的路由情况，对于不存在的路由返回 404 页面。

``` js
routes: [
  { path: '*', component: NotFoundComponent }
]
```

``` js
/* 后端配置例子 */
// Apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.html [L]
</IfModule>
//除了 mod_rewrite，也可以使用 FallbackResource。

// nginx
location / {
  try_files $uri $uri/ /index.html;
}

// 原生 Node.js
const http = require('http')
const fs = require('fs')
const httpPort = 80

http.createServer((req, res) => {
  fs.readFile('index.htm', 'utf-8', (err, content) => {
    if (err) {
      console.log('We cannot open "index.htm" file.')
    }

    res.writeHead(200, {
      'Content-Type': 'text/html; charset=utf-8'
    })

    res.end(content)
  })
}).listen(httpPort, () => {
  console.log('Server listening on: http://localhost:%s', httpPort)
})

// Express，请考虑使用 connect-history-api-fallback 中间件。
```

## 嵌套路由
``` html
<div id="app">
  <!-- 顶层路由 -->
  <router-view></router-view>
</div>

<script>
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <!-- 子路由 -->
      <router-view></router-view>
    </div>
  `
}

const router = new VueRouter({
  routes: [
    { 
      path: '/user/:id', 
      component: User,
      children: [
        {
          //当 /user/:id 匹配成功，默认渲染子路由
          //UserHome 会被渲染在 User 的 <router-view> 中
          path: '', 
          component: UserHome 
        },
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
</script>
```

> 注意,以 / 开头的嵌套路径会被当作根路径。

## 命名视图
有时候想同时（同级）展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar（侧导航） 和 main（主内容） 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

``` html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

### 嵌套命名视图
``` html
<!-- UserSettings.vue -->
<div>
  <h1>User Settings</h1>
  <NavBar/>
  <router-view/>
  <router-view name="helper"/>
</div>
```

``` js
{
  path: '/settings',
  // 你也可以在顶级路由就配置命名视图
  component: UserSettings,
  children: [{
    path: 'emails',
    component: UserEmailsSubscriptions
  }, {
    path: 'profile',
    components: {
      default: UserProfile,
      helper: UserProfilePreview
    }
  }]
}
```

## 路由切换动效
``` html
<!--app.vue 根组件-->
<template>
  <div id="app">
    <transition name="fade" mode="out-in">
      <router-view></router-view>
    </transition>
  </div>
</template>
<script>
  export default {
    name: 'app',
    components: {}
  }
</script>
<style>
  .fade-enter-active,.fade-leave-active {
    transition: opacity .2s ease;
  }
  .fade-enter,.fade-leave-active {
    opacity: 0;
  }
</style>
```

## 导航守卫(beforeRouteUpdate)
参数或查询的改变并不会触发进入/离开的导航守卫。你可以通过观察 `$route` 对象来应对这些变化，或使用 beforeRouteUpdate 的组件内守卫。

## 全局守卫(router.beforeEach)
``` js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})

//当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 等待中。

/* 
每个守卫方法接收三个参数：
to: Route: 即将要进入的目标 路由对象
from: Route: 当前导航正要离开的路由
next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。
  next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是  confirmed （确认的）。
  next(false): 中断当前的导航。如果浏览器的 URL 改变了（可能是用户手动或者浏览器后退按钮），那么 URL 地址会重置到 from 路由对应的地址。
  next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。
  next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。
  确保要调用 next 方法，否则钩子就不会被 resolved。
*/
```

## 路由拦截
``` js
router.beforeEach((to, from, next) => {
//假设登陆成功后，user信息保存在sessionStorage中。
  if (to.path == '/login') {
    sessionStorage.removeItem('user');
  //如果访问登录页，清空之前sessionStorage中的user信息
  }
  let user = JSON.parse(sessionStorage.getItem('user'));
  if (!user && to.path != '/login') {
    next({ path: '/login' })
  } else {
    next()
  }
//如果访问非登陆页，判断是否有保存的user信息，如果没有，则判断为非法访问，重定向到登录页面
});
```

* to: 即将要进入的目标路由对象
* from: 当前导航正要离开的路由
* next: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。
  * `next()`: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed （确认的）。
  * `next(false)`: 中断当前的导航。如果浏览器的 URL 改变了（可能是用户手动或者浏览器后退按钮），那么 URL 地址会重置到 from 路由对应的地址。
  * `next('/')` 或者 `next({ path: '/' })`: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。

确保要调用 next 方法，否则钩子就不会被 resolved。

### http拦截器
拦截器是全局的，拦截器可以在请求发送前和发送请求后做一些处理。拦截器在一些场景下会非常有用，比如请求发送前在 headers 中设置 access_token  ，或者在请求失败时，提供通用的处理方式。

``` js
// http request 拦截器
axios.interceptors.request.use(
    config => {
        if (store.state.token) {  // 判断是否存在token，如果存在的话，则每个http header都加上token
            config.headers.Authorization = `token ${store.state.token}`;
        }
        return config;
    },
    err => {
        return Promise.reject(err);
    });

// http response 拦截器
axios.interceptors.response.use(
    response => {
        return response;
    },
    error => {
        if (error.response) {
            switch (error.response.status) {
                case 401:
                    // 返回 401 清除token信息并跳转到登录页面
                    store.commit(types.LOGOUT);
                    router.replace({
                        path: 'login',
                        query: {redirect: router.currentRoute.fullPath}
                    })
            }
        }
        return Promise.reject(error.response.data)   // 返回接口返回的错误信息
    });
```

### 路由拦截案例
#### 第一步：定义路由
在定义路由的时候多添加一个自定义字段 requireAuth，用于判断该路由的访问是否需要登录。如果用户已经登录，则顺利进入路由， 否则就进入登录页面。

``` js
const routes = [
    {
        path: '/',
        name: '/',
        component: Index
    },
    {
        path: '/repository',
        name: 'repository',
        meta: {
            requireAuth: true,  // 该字段来判断该路由是否需要登录权限
        },
        component: Repository
    },
    {
        path: '/login',
        name: 'login',
        component: Login
    }
];
```

#### 第二步：路由拦截
利用钩子函数 beforeEach() 对路由进行判断。

``` js
router.beforeEach((to, from, next) => {
    if (to.meta.requireAuth) {  // 判断该路由是否需要登录权限
        if (store.state.token) {  // 通过 vuex state 获取当前的 token 是否存在
            next();
        }
        else {
            next({
                path: '/login',
                query: {redirect: to.fullPath}  // 将跳转的路由 path 作为参数，登录成功后跳转到该路由
            })
        }
    }
    else {
        next();
    }
})
```

#### 第三步：拦截器
此时只是简单的前端路由控制，并不能真正阻止用户访问需要登录权限的路由。还有可能当前 token 失效，但是本地依然保存了 token。此时去访问需要登录权限的路由时，应该让用户重新登录。 这时候就需要结合 http 拦截器 + 后端接口返回的 http 状态码来判断。这里使用 axios 的拦截器。通过配置 http response inteceptor，当后端接口返回 401 Unauthorized（未授权），让用户重新登录。

``` js
// http request 拦截器
axios.interceptors.request.use(
    config => {
        if (store.state.token) {  // 判断是否存在token，如果存在的话，则每个http header都加上token
            config.headers.Authorization = `token ${store.state.token}`;
        }
        return config;
    },
    err => {
        return Promise.reject(err);
    });

// http response 拦截器
axios.interceptors.response.use(
    response => {
        return response;
    },
    error => {
        if (error.response) {
            switch (error.response.status) {
                case 401:
                    // 返回 401 清除token信息并跳转到登录页面
                    store.commit(types.LOGOUT);
                    router.replace({
                        path: 'login',
                        query: {redirect: router.currentRoute.fullPath}
                    })
            }
        }
        return Promise.reject(error.response.data)   // 返回接口返回的错误信息
    });
```

对应的，如果是登出的话，清除当前 token，再跳转到首页即可。

## 路由返回保持位置不变
如果用户在列表页滚动了很长，当进入详情页后再返回列表页，之前的列表页位置得保持不变。如果要实现这个功能，需要引入 keep-alive 。

* 包裹动态组件时会缓存组件实例，而不是销毁；

* keep -alive 内路由切换时会调用  activated 和  deactivated 这两个钩子；

* 套在 router -view 外面，受到影响的范围就是  router -view 里面的路由跳转。

注意事项：

* 使用后会导致 created 可能不被调用，需要把一些逻辑移到  activated；

* 针对不需要保留状态的情况，可以在 deactivated 中调用  `$destroy()`。

以上方法可以解决返回时不会刷新页面，从而保持原来的位置不变，但如果从列表页进入详情页后，更改了列表状态，那么列表页需要刷新这一条数据。此时还是需要用 vuex 来解决这个问题，在详情页的 deactivated 钩子更新列表中对应的该条数据，同样依赖后端对于详情和列表接口描述订单采用同样的数据格式，代码大致如下：

``` js
deactivated () {
  this.$store.commit('AUCTION_LIST_INDEX', this.index, this.$data)
}
```

## 路由组件按需加载
在 Vue 项目中，一般使用 vue-cli 构建项目后，在 `npm run build` 的时候会打包成一个整个的 js 文件，如果页面一多，会导致这个文件非常大，加载缓慢，为了解决这个问题，需要将他分成多个小文件，而且还要实现异步按需加载，即用到了再加载。

### 方法一：webpack
webpack 提供的 `require.ensure()`,这样可以实现按需加载，并且你可以将多个相同类的组件打包成一个文件，只要给他们指定相同的 chunkName 即可，如示例中的 demo 将会打包成一个文件。

``` js
{
  path: '/home',
  name: 'Home',
  component: r => require.ensure([], () => r(require('../components/Home')), 'demo')
},
{
  path: '/about',
  name: 'About',
  component: r => require.ensure([], () => r(require('../components/About')), 'demo')
}
```

### 方法二：Vue 异步组件
这种方法可以实现按需加载，并且一个组件会打包成一个 js 文件：

``` js
{
  path: '/Home',
  name: 'Home',
  component: resolve => require(['../components/Home'], resolve)
}
```

### es6 import
首先，可以将异步组件定义为返回一个 Promise 的工厂函数 (该函数返回的 Promise 应该 resolve 组件本身)：

``` js
const Foo = () => Promise.resolve({ /* 组件定义对象 */ })
```

第二，在 Webpack 2 中，我们可以使用动态 import 语法来定义代码分块点 (split point)：

``` js
import('./Foo.vue') // 返回 Promise
```

这样做的结果是每个组件都会打包成一个 js 文件，有时候我们想把某个路由下的所有组件都打包在同个异步块 (chunk) 中。只需要使用 命名 chunk：

``` js
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
```
