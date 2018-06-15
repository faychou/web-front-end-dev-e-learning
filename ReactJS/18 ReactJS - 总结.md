# React 总结
## 资源加载
### 引入图片
#### 方法一：
``` js
<img src={require('../img/icon1.png')} alt="" />
```

#### 方法二：
``` js
import logo from '../img/logo.png'

<img src={logo} alt="" />
```

#### 方法三(不推荐)：
``` html
<img src='http://localhost:3000/img/logo.png' alt="" />
```

### 背景图片引用
``` js
const divStyle = {
  backgroundImage: 'url(' + imgUrl + ')',
  // 或者 background: `url${require("img/logo.png")}`
};

const MyComponent = () {
  return <div style={divStyle}></div>;
}
```
