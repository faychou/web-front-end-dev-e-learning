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

### 路由信息提取到单独文件中
一般我们会把路由信息 routes 提取到一个单独的文件中，如 `route-config.js`：

``` js
import PageOne from './components/PageOne.vue'
import PageTwo from './components/PageTwo.vue'

export default [
  { 
    path: '/',
    component: PageOne 
  },{ 
    path: '/two', 
    component: PageTwo
  }
];
```

然后在 main.js 中引入： `import routes from './route-config.js'`。

## API
### `<router-link>`
创建 a 标签来定义导航链接。

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
<router-link :to="'/home/'+id">home1</router-link>
<!-- 或者 -->
<router-link to="{name:'list',params: {id: id}}">home1</router-link>

<!-- 带查询参数，下面的结果为 /register?plan=private -->
<router-link :to="{ path: 'register', query: { plan: 'private' }}">Register</router-link>
```

#### active-class
设置 链接激活时使用的 CSS 类名，默认值: "router-link-active"。

#### replace
调用 router.replace() 而不是 router.push()，于是导航后不会留下 history 记录。默认值: false。

``` html
<router-link :to="{ path: '/abc'}" replace></router-link>
```

#### append
设置 append 属性后，则在当前（相对）路径前添加基路径。例如，我们从 /a 导航到一个相对路径 b，如果没有配置 append，则路径为 /b，如果配了，则为 /a/b。默认值: false。

``` html
<router-link :to="{ path: 'relative/path'}" append></router-link>
```

#### tag
有时候想要 `<router-link>` 渲染成某种标签，例如 `<li>`。 于是我们使用 tag prop 类指定何种标签，同样它还是会监听点击，触发导航。默认值: `<a>`。

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
展示我们匹配到的组件的区域。

### `$router`
`$route` 是一个数组，里面包含路由的所有信息。通过注入路由，我们可以在组件里用 `this.$router` 来访问它。

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

### router.push(location, onComplete?, onAbort?)
在 Vue 实例内部，你可以通过 `$router` 访问路由实例。因此你可以调用 `this.$router.push`。当你点击 `<router-link>` 时，这个方法会在内部调用，所以说，点击 `<router-link :to="...">` 等同于调用 `router.push(...)`，这个方法会向 history 栈添加一个新的记录。

``` js
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: 123 }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

可选的在 `router.push` 或 `router.replace` 中提供 onComplete 和 onAbort 回调作为第二个和第三个参数。这些回调将会在导航成功完成 (在所有的异步钩子被解析之后) 或终止 (导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行相应的调用。

> 注意：如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 `/users/1` -> `/users/2`)，你需要使用 beforeRouteUpdate 来响应这个变化 (比如抓取用户信息)。

### router.replace(location, onComplete?, onAbort?)
跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

### router.go(n)
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 `window.history.go(n)`。

``` js
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```
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

这跟代码调用 router.push() 是一回事：

``` js
router.push({ name: 'user', params: { userId: 123 }})
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

像 `/user/for` 和 `/user/bar` 都将映射到相同的路由。

| 模式        | 匹配路径   |  `$route.params`  |
| --------   | :-----:   | :----: |
| `/user/:username`        | `/user/evan`      |   `{ username: 'evan' }`    |
| `/user/:username/post/:post_id`     | `/user/evan/post/123` |   `{ username: 'evan', post_id: 123 }`  |

`$route` 对象：

* `$route.query`：URL 中查询参数；
* `$route.hash`：哈希值；
* `$route.path`：路径；
* `$route.fullPath`：完整路径；
* `$route.matched`
* `$route.name`

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