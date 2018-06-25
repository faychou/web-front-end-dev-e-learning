# React 总结
## 资源加载
### 引入图片
#### 方法一：
``` js
// 注意：require 里只能写字符串，不能写变量
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

#### 方法四：
以上方法都是只针对少量图片，且是直接加载本地图片的，如果是直接从数据库读取图片地址，量少且有顺序可以这样：

``` js
import img1 from'./../images/img1.png';
import img2 from'./../images/img2.png';
import img3 from'./../images/img3.png';

const imgsArr = [img1,img2,img3];

render(){
  return(
    <div>
      {
        imgsArr.map(function(name) {
          return(
            <img src={name} />
          )
        }) 
      }
    </div>
  )
}
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
