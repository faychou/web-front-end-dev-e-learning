# vue 技巧
## 删除数组索引
在数组中删除一项的标准做法是用 Array.splice(index, 1)

``` js
del (index) {
  this.arr.splice(index ,1);
}
```

在 Vue.js 2.2.0+ 的版本里可以直接用 Vue.delete

``` js
del (index) {
  this.$delete(this.arr, index);
}
```

## 选中 input 框中文字
调用 select 方法即可

``` html
<input @focus="$event.target.select()">
```

组件中调用就需要加上 native 属性了

``` html
<component @focus.native="$event.target.select()"></component>
```

## 子组件修改父组件的数据
在 Vue 中子组件是不允许修改父组件穿进来的值，否则就会引发一个报错，如果需要的只是修改渲染的数据，那么可以 emit 出去，然后在父组件重新赋值，通过双向绑定，触发子组件的再次渲染。

``` js
<!--父组件HTML内容-->
<children-component :value="data" @changeProp="changeData"></children-component>


<!--父组件的js内容-->
export default({
    data(){
        return {
            data : [1,2,3,4]
        }
    },
    methods : {
        changeData(value){
            this.data = value;
        }
    }
})

<!--子组件的js内容-->
export default({
    props : ["value"],
    created : {
        this.$emit("changeProp",[5,6,7,8])
    }
})
```
原理就是 emit 一个数据到父组件上去，然后在父组件中接受到这个传递上来的新值，将 data 赋予新值，然后重新传递到了子组件，起到一个变向修改子组件的效果。

## Vue 给每个组件提供了一个默认的父子传递事件 v-model
vue 为每个组件提供了一个默认 v-model 的语法糖。

``` js
<!--父组件HTML内容-->
<children-component v-mode="data" ></children-component>

<!--父组件的js内容-->
export default({
    data(){
        return {
            data : [1,2,3,4]
        }
    },
    watch : {
        //可以在此处监听子组件传递上来的数据
        data(n,o){
            console.log(n,o);   //[5,6,7,8],[1,2,3,4]
        }
    }
})

<!--子组件的js内容-->

export default({
    <!--大家注意我下面这行代码是打了注释的，说明我在子组件没有定义任何属性-->
    //  props : ["value"]
    <!--并且我接下去直接在代码中使用了this.value(一个完全没有定义过的value)。-->
    created : {
        console.log(this.value);
        this.$emit("input",[5,6,7,8]);  //当我想改变传进来值的时候
    }
})
```

对于这个语法糖的解释。

``` js
//注意，该组件不是表单组件
<children-component :value="data" @input="data = arguments[0]"></children-component>
```

## 全局一次性引用写好的组件
假如我们写好了一些组件，接下去肯定还要引入和使用吧。但是你写了这么多组件，在每个地方一个个引用想要的是一件很麻烦的事情。我们最好是在一个初始的地方一次性全部引入，然后在用的地方直接使用。

``` js
// 在文件开头初始引入所有的组件文件
import b from "./components/common/b.vue"
import c from "./components/common/c.vue"

const components = [b,c];

const install = function (Vue, opts = {}) {
	components.map(component => {
		Vue.component(component.name, component);
	});
};

export default install
```

然后直接在启动的main.js文件里面引入就好：

``` js
import ui from "install.js"
Vue.use(ui);
```

然后你就可以随意在任何一个组件里面直接调用了，比自己之前在每个组件里重复调用要方便一点。

``` js
<b v-model="data1"></b>
<c v-model="data2"></c>
```