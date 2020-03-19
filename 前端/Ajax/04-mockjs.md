# MockJS
在前后端分离的开发环境中，前端需要等待后端给出接口及接口文档之后，才能继续开发。而 mock.js 可以让前端独立于后端进行开发。基于 mock.js 前端的数据请求可以自给自足，"独立" 于后端开发。在项目前期,前端可以专心于自己的前端展示，后端可以专心于自己后端的接口和数据处理。但前后端分离最怕的情况就是：前端自己玩自己的，后端也自己玩自己的。等到联调时，发现接口完全牛头不对马嘴! 所以在开发时，前后端也要时刻保持沟通，及时确认最新的接口和数据结构。(利用 swagger 管理)。

通过 mockjs 提供的方法，你可以轻松地创造大量随机的文本，数字，布尔值，日期，邮箱，链接，图片，颜色等。

mockjs 可以进行强大的 ajax 拦截，能判断请求类型，获取到 url，请求参数等。然后可以返回 mock 的假数据，或者你自己编好的 json 文件，功能强大易上手。

## 使用
### 安装
``` bash
npm install mockjs --save-dev
```

### 根目录创建 mock 文件夹结构
``` bash
mock
  -- index.js
```

相关 mock 代码：

``` js
// ./mock/index.js
const Mock = require('mockjs');

const data = [];
let row = {
	'string|1-9':'string',
	'number|1-9':1,
	'boolean|1-9':false,
	'undefined|1-9':undefined,
	'null|1-9':null,
	'object|1-9':{object1:'object1',object2:'object2',object3:'object3'},
	'array|1-9':['array1','array2'],
	'function|1-9':()=>'function'
};
data.push(row);

// 定义模拟接口只能接收 post 请求，定义返回的数据为 data1
Mock.mock('/Get/list', 'post', data);
```

## 数据模板定义
数据模板的格式为： `'属性名|生成规则':'属性值'`。

生成规则一共有7种，分别是：

* 'name|min-max': value

* 'name|count': value

* 'name|min-max.dmin-dmax': value

* 'name|min-max.dcount': value

* 'name|count.dmin-dmax': value

* 'name|count.dcount': value

* 'name|+step': value

对于不同的数据类型，可以使用的生成规则是不同的，属性值的数据类型可以是 Number、Boolean、String、Object、Array、Function、Null，不可以是 Undefined。

### String
``` js
'name|min-max': 'value' 通过重复 'value' 生成一个字符串，重复次数大于等于 min，小于等于 max。
```

如：

``` js
Mock.mock({
  "string|1-10": "★"   
}) // 随机出现1到10个字符"★" 
// => 
{ 
  "string": "★★★★★★"
}
```

``` js
'name|count': 'value' 通过重复 'value' 生成一个字符串，重复次数等于 count
```

如：
 
``` js
Mock.mock({                   
"string|3": "★"              
})            
// =>
{
  "string": "★★★"
}
```

### Number
``` js
'name|min-max.dmin-dmax': number
// min-max 指整数部分的最小值与最大值
// dmin-dmax 指小数位的位数
```

如：

``` js
Mock.mock({                      
"number|1-100.1-10": 1              
})                                     

// =>
{
  "number": 75.75862
} 
```

``` js
'name|+step': number
// 初始值为预设的 name 值，每重新请求一次时数值 name 会增加一个 step 值
```

### Boolean
``` js
'name|1': boolean                        
  Mock.mock({                         
    "boolean|1": true                        
  }) // true 和 false 出现的几率是二分之一 

// =>
{
  "boolean": false
}

'name|min-max': boolean                 
    Mock.mock({                          
    "boolean|1-2": true                 
  }) // value 的概率是 min / (min + max)，值为 !value 的概率是 max / (min + max)
  
// =>
{
  "boolean": true
}
```

### Object
``` js
'name|count': object
'name|min-max': object
//原理同字符串
```

### Array
``` js
'name|1': array
'name|min-max': array
'name|count': array
```

## Random 工具类定义
MockJS 提供了一组方法让我们快速随机出想要的数据。

``` js
//mock.js
let data2 = [] // 用于接受生成数据的数组
let size = [
  '300x250', '250x250', '240x400', '336x280', 
  '180x150', '720x300', '468x60', '234x60', 
  '88x31', '120x90', '120x60', '120x240', 
  '125x125', '728x90', '160x600', '120x600', 
  '300x600'
] // 定义随机值
for(let i = 0; i < 10; i ++) { //生成10个对象放到数组中
  let template = {
    'Boolean': Random.boolean, // 可以生成基本数据类型
    'Natural': Random.natural(1, 100), // 生成1到100之间自然数
    'Integer': Random.integer(1, 100), // 生成1到100之间的整数
    'Float': Random.float(0, 100, 0, 5), // 生成0到100之间的浮点数,小数点后尾数为0到5位
    'Character': Random.character(), // 生成随机字符串,可加参数定义规则
    'String': Random.string(2, 10), // 生成2到10个字符之间的字符串
    'Range': Random.range(0, 10, 6), // 生成一个随机数组
    'Date': Random.date(), // 生成一个随机日期,可加参数定义日期格式
    'Image': Random.image(Random.size, '#02adea', 'Hello'), // Random.size表示将从size数据中任选一个数据
    'Color': Random.color(), // 生成一个颜色随机值
    'Paragraph':Random.paragraph(2, 5), //生成2至5个句子的文本
    'Name': Random.name(), // 生成姓名
    'Url': Random.url(), // 生成web地址
    'Address': Random.province() // 生成地址
  }
  data2.push(template)
}
Mock.mock('/Get/list2', 'post', data2) // 声明模拟接口

//App.vue
axios('/Get/list2').then(res => {
	this.dataShow2 = res;
});
```

## 拦截/模拟 ajax 请求
### Mock.mock( rurl, template )
记录数据模板。当拦截到匹配 rurl 的 Ajax 请求时，将根据数据模板 template 生成模拟数据，并作为响应数据返回。

### Mock.mock( rurl, rtype, template )
记录数据模板。当拦截到匹配 rurl 和 rtype 的 Ajax 请求时，将根据数据模板 template 生成模拟数据，并作为响应数据返回。

``` js
//调用mock方法模拟数据
Mock.mock(/\.json/, {
    'list|1-10': [{
        'id|+1': 1,
        'email': '@EMAIL'
    }]//所有ajax地址以.json结尾的都被拦截，并且返回数据
})
```

