# Vue 其他 API
## Vue.set(target,key,value)
设置对象属性。

## Vue.set(目标数组，索引，替换值)
重设属性值。

``` js
methods: {
  addItem () {
    //使用Vue.set()方法
    Vue.set(this.list,1,{
      name: 'app',
      price: 100
    })
  }
}
```