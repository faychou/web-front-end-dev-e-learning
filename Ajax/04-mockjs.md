# mockjs
通过 mockjs 提供的方法，你可以轻松地创造大量随机的文本，数字，布尔值，日期，邮箱，链接，图片，颜色等。

mockjs 可以进行强大的 ajax 拦截，能判断请求类型，获取到 url，请求参数等。然后可以返回 mock 的假数据，或者你自己编好的 json 文件，功能强大易上手。

前后端分离:由于mockjs,前端的数据请求可以自给自足, "独立" 于后端开发.在项目前期,前端可以专心于自己的前端展示,后端可以专心于自己后端的接口和数据处理.但前后端分离最怕的情况就是:前端自己玩自己的,后端也自己玩自己的.等到联调时,发现接口完全牛头不对马嘴! 所以在开发时,前后端也要时刻保持沟通,及时确认最新的接口和数据结构。(利用 swagger 管理)

## 使用
### 安装
``` bash
npm install mockjs --save-dev
```

### 根目录创建 mock 文件夹结构
``` bash
mock
  |--
```

## 数据模板定义
### String
``` js
'name|min-max': 'value' 通过重复 'value' 生成一个字符串，重复次数大于等于 min，小于等于 max。
```

如：

``` js
Mock.mock({
  "string|1-10": "★"   
}) //随机出现1到10个字符"★" 
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

### Boolean
``` js
'name|1': boolean                       { 
  Mock.mock({                  =>       "boolean": false
    "boolean|1": true                   }     
  }) //true和false出现的几率是二分之一 

'name|min-max': boolean         =>        { 
    Mock.mock({                          "boolean": true
    "boolean|1-2": true                 }
  }) // value 的概率是 min / (min + max)，值为 !value 的概率是 max / (min + max)。
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

