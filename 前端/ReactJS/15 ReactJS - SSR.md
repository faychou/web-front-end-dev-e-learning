# 服务器端渲染(SSR)
SSR，俗称 服务端渲染 (Server Side Render)，讲人话就是: 直接在服务端层获取数据，渲染出完成的 HTML 文件，直接返回给用户浏览器访问。

在学习 react 服务器端渲染之前，首先来了解一个概念--同构。

### 什么是同构？

一套代码既可以在服务端运行又可以在客户端运行，这就是同构应用。简而言之, 就是服务端直出和客户端渲染的组合, 能够充分结合两者的优势，并有效避免两者的不足。

### 为什么同构？
* 性能: 通过 Node 直出, 将传统的三次串行 http 请求简化成一次 http 请求，降低首屏渲染时间；

* SEO: 服务端渲染对搜索引擎的爬取有着天然的优势。

* 兼容性: 部分展示类页面能够有效规避客户端兼容性问题，比如白屏。

### 生态
* [react-server](https://react-server.io): React 服务端渲染框架；

* [Next.js](https://github.com/zeit/next.js): 是一个用于 React 应用的极简的服务端渲染框架。

* [beidou](https://github.com/alibaba/beidou): 阿里自己的同构框架，基于eggjs, 定位是企业级同构框架。

## React + Router + Redux + Koa
### 1、项目结构：
``` bash
build
public
src
  |-- client
  |-- server
```

### 2、server 中使用 Koa 路由监听页面访问
``` js
import * as Router from 'koa-router'

const router = new Router()
// 如果中间也提供 Api 层
router.use('/api/home', async () => {
  // 返回数据
})

router.get('*', async (ctx) => {
  // 返回 HTML
})
```

### 3、通过访问 url 匹配 前端页面路由
``` js
// 前端页面路由
import { pages } from '../../client/app'
import { matchPath } from 'react-router-dom'

// 使用 react-router 库提供的一个匹配方法
const matchPage = matchPath(ctx.req.url, page)
```

### 4、通过页面路由的配置进行数据获取。通常可以在页面路由中增加 SSR 相关的静态配置，用于抽象逻辑，可以保证服务端逻辑的通用性
``` js
class HomePage extends React.Component{
	public static ssrConfig = {
		  cache: true,
         fetch() {
        	  // 请求获取数据
         }
    }
}
```

获取数据通常有两种情况，第一种：中间层也使用 http 获取数据，则此时 fetch 方法可前后端共享；

``` js
const data = await matchPage.ssrConfig.fetch()
```

中间层并不使用 http，是通过一些 内部调用，例如 Rpc 或 直接读数据库 等，此时也可以直接由服务端调用对应的方法获取数据。通常，这里需要在 ssrConfig 中配置特异性的信息，用于匹配对应的数据获取方法。

``` js
// 页面路由
class HomePage extends React.Component{
	public static ssrConfig = {
        fetch: {
        	 url: '/api/home',
        }
    }
}

// 根据规则匹配出对应的数据获取方法
// 这里的规则可以自由，只要能匹配出正确的方法即可
const controller = matchController(ssrConfig.fetch.url)

// 获取数据
const data = await controller(ctx)
```

### 5、创建 Redux store，并将数据dispatch到里面
``` js
import { createStore } from 'redux'
// 获取 Clinet层 reducer
// 必须复用前端层的逻辑，才能保证一致性；
import { reducers } from '../../client/store'

// 创建 store
const store = createStore(reducers)
 
// 获取配置好的 Action
const action = ssrConfig.action

// 存储数据	
store.dispatch(createAction(action)(data))
```

### 6、注入 Store， 调用renderToString将 React Virtual Dom 渲染成 字符串
``` js
import * as ReactDOMServer from 'react-dom/server'
import { Provider } from 'react-redux'

// 获取 Clinet 层根组件
import { App } from '../../client/app'

const AppString = ReactDOMServer.renderToString(
	<Provider store={store}>
		<StaticRouter
			location={ctx.req.url}
			context={{}}>
			<App />
		</StaticRouter>
	</Provider>
)
```

### 7、将 AppString 包装成完整的 html 文件格式；


### 8、此时，已经能生成完整的 HTML 文件。但只是个纯静态的页面，没有样式没有交互。接下来我们就是要插入 JS 与 CSS。我们可以通过访问前端打包后生成的asset-manifest.json文件来获取相应的文件路径，并同样注入到 Html 中引用。
``` js
const html = `
	<!DOCTYPE html>
	<html lang="zh">
		<head></head>
		<link href="${cssPath}" rel="stylesheet" />
		<body>
			<div id="App">${AppString}</div>
			<script src="${scriptPath}"></script>
		</body>
	</html>
`
```

### 9、进行 数据脱水: 为了把服务端获取的数据同步到前端。主要是将数据序列化后，插入到 html 中，返回给前端
``` js
import serialize from 'serialize-javascript'
// 获取数据
const initState = store.getState()
const html = `
	<!DOCTYPE html>
	<html lang="zh">
		<head></head>
		<body>
			<div id="App"></div>
			<script type="application/json" id="SSR_HYDRATED_DATA">${serialize(initState)}</script>
		</body>
	</html>
`

ctx.status = 200
ctx.body = html
```

### 10、Client 层 数据吸水: 初始化 store 时，以脱水后的数据为初始化数据，同步创建 store
``` js
const hydratedEl = document.getElementById('SSR_HYDRATED_DATA')
const hydrateData = JSON.parse(hydratedEl.textContent)

// 使用初始 state 创建 Redux store
const store = createStore(reducer, hydrateData)
```

## Next.js
### 安装
``` bash
npm install --save next react react-dom
```

Next.js 4 只支持 React 16。

### 运行
首先在 package.json 中添加以下代码：

``` json
{
  "scripts": {
    "dev": "next"
  }
}
```

接着打开终端，进入项目目录并运行：

``` bash
npm run dev
```

最后打开浏览器 http://localhost:3000 ，此时我们会看到 404 页面。

此时在项目中新建一个 `./pages/index.js` 文件：

``` js
const Index = () => (
  <div>
    <p>Hello Next.js</p>
  </div>
)

export default Index
```

然后再次访问 http://localhost:3000 ，就可以看到 “Hello Next.js” 了。所以我们可以知道 Next.js 以 `./pages` 目录作为页面渲染目录的的服务器端渲染。

另外一点，如果我们的代码写错了，Next.js 会帮我们在浏览器中显示出来。

### 静态文件服务
在项目的根目录新建 static 文件夹，然后就可以通过 `/static/` 开头的路径来引用此文件夹下的文件：

``` js
export default () => <img src="/static/logo.png" />
```


