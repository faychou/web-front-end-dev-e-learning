# Vue 路由
## 安装
	npm install vue-router --save
	
## 基本使用
``` html
<!-- 使用 router-link 组件来导航. -->
<!-- 通过传入 `to` 属性指定链接. -->
<!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
<router-link to="/foo">Go to Foo</router-link>

<!-- 路由匹配到的组件将渲染在这里 -->
<router-view></router-view>
```

### 例子1
``` js
// index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import App from './App'
import Page01 from './components/page01'
import Page02 from './components/page02'
// 也可使用下面的懒加载写法
//const Page01 = (resolve) => require(['./components/page01'],resolve)

Vue.use(VueRouter) //全局安装路由功能

//定义路径
const routes = [
  { path: '/', component: Page01 },
  { path: '/02', component: Page02 },
]

//创建路由对象
const router = new VueRouter({
  routes
})

new Vue({
  el: '#app',
  template: '<App/>',
  components: { App },
  router
})
```
``` html
<!-- App.vue -->
<template>
  <div id="app">
    <!-- router-link 定义跳转路径 -->
    <router-link to="/">01</router-link>
    <router-link to="/02">02</router-link>
    <br/>
    <!-- router-view 来渲染组件 -->
    <router-view></router-view>
  </div>
</template>
```

``` html
<!-- page01.vue -->
<template>
  <div>
    <h1>page02</h1>
  </div>
</template>
```
``` html
<!-- page02.vue -->
<template>
  <div>
    <h1>page02</h1>
  </div>
</template>
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

像 `/user/fo` 和 `/user/bar` 都将映射到相同的路由。

| 模式        | 匹配路径   |  `$route.params`  |
| --------   | :-----:   | :----: |
| `/user/:username`        | `/user/evan`      |   `{ username: 'evan' }`    |
| `/user/:username/post/:post_id`     | `/user/evan/post/123` |   `{ username: 'evan', post_id: 123 }`  |

`$route` 对象：

* `$route.query`
* `$route.hash`
* `$route.path`
* `$route.fullPath`
* `$route.matched`
* `$route.name`

### 监听路由参数的变化
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