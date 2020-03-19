# 搭建开发环境
### 手动搭建环境
见 webpack。

### 快速环境搭建（老版）
``` bash
# 环境安装
npm install -g create-react-app

# 创建项目
create-react-app my-app cd my-app

# 运行项目
npm start || npm test || npm run build
```

### 快速环境搭建（新版）
``` bash
npm install -g create-react-app

# 创建项目
npx create-react-app my-app

# 进入项目
cd my-app

# 开启项目
npm start

# 或者：使用 npm
npm init react-app my-app

# npm start or yarn start
# npm test or yarn test
# npm run build or yarn build

# 或者：使用 yarn
yarn create react-app my-app

```

> 注：npm 5.2+ 可以直接使用 npx。

### 启用 sass
``` bash
# 安装依赖
npm install node-sass --save
# yarn add node-sass
```

``` js
// 使用
import React, {Component} from 'react';
import './App.scss';

class App extends Component {
  render() {
    return (
      <div className="App">
        ...
      </div>
    );
  }
}

export default App;
```

> 注：react-scripts@2.0.0 以上版本才适用。

### 添加全局的资源（图片、字体、svg、视频等）
在公共目录下，你可以放字体文件、图片、svg 等文件，访问这些文件最好添加 `%PUBLIC_URL%` 作为根目录。

``` html
<link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
```

### 添加自定义环境变量文件
项目根目录下创建 .env 文件，文件内部添加 key=value 的配置可以直接应用于项目的编译中：

```
REACT_APP_TITLE=abc
```

> 如果创建自定义的环境变量必须以 `REACT_APP_` 开头。

在项目中可以直接用process.env.XXX访问我们的自定义的环境变量：

``` jsx
// jsx
<input type="text" value={process.env.REACT_APP_title} />
```

``` html
<!--html-->
<title>%REACT_APP_TITLE%</title>
```

### 配置 TypeScript
``` bash
# 方式一：新建项目直接配置
npx create-react-app my-app --typescript
#or
yarn create react-app my-app --typescript


# 方式二：为现有的 React 项目添加 TypeScript
npm install --save typescript @types/node @types/react @types/react-dom @types/jest
# or
yarn add typescript @types/node @types/react @types/react-dom @types/jest
```

### 反向代理设置
直接在 package.json 文件中添加 proxy 属性：

``` json
{
  // ...
  "proxy": "http://localhost:3333",
  // ...
}
```

也可以通过模块自定义配置代理：

``` bash
npm install http-proxy-middleware --save
$ # or
$ yarn add http-proxy-middleware
```

创建： src/setupProxy.js 文件:

``` js
const proxy = require('http-proxy-middleware');
module.exports = function(app) {
  app.use(proxy('/api', { target: 'http://localhost:3333/' }));
};
```

### HTTPS 配置
需要用 HTTPS 进行开发调试：

Windows (cmd.exe)：

``` bash
set HTTPS=true&&npm start
```

Windows (Powershell)：

``` bash
($env:HTTPS = "true") -and (npm start)
```

Linux, macOS (Bash)：

``` bash
HTTPS=true npm start
```

