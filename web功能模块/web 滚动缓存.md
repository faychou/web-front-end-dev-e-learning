# web滚动缓存
在项目中，我们可能会遇到这样的需求：从商品列表页中，点击某一商品，进入到详情页。从详情页中返回到商品列表页，页面应当显示开始点击某一商品位置，而不是回到商品列表页的顶部。也就是说，滚动条的位置应该缓存下来。

## vue思路
* 商品列表需要被缓存下来,页面的缓存方式请查看 vue 官方文档 keep-alive 来缓存页面，这样，在详情页面返回的时候，页面不至于重新加载。


* 在商品列表生命周期 activated 中，监听当前 scrollContainer 父元素的滚动事件，滚动时的回调中，获取到 scrollTop 的值，存入以及在 deactivated 中移除掉当前滚动事件的监听。

* 在商品列表中，点击进入详情页中的时候，将滚动条位置被缓存下来了，可以放到 sessionStorage 或者 localStorage 中。如果使用了 vuex ，可以直接将值放入 vuex 中进行管理，但是注意一点，如果存储在 vuex 的 store 上，页面刷新就会丢失，所以建议保存在本地存储合适点。

* 从详情页中返回的时候，同时要做这样的操作，将你存入缓存中的 scrollTop 值重新赋予给当前 div 的滚动条

``` html
<div class="scrollContainer" ref="scroll"> 
  <ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
    <li>7</li>
    <li>8</li>
    <li>9</li>
    <li>10</li>  
  </ul>
</div>
```

``` js
computed:{
  ...mapGetters([
    "home_list_top"    //vuex中的存放的滚动条的位置
  ])
}
//...
methods:{
  recordScrollPosition(e) {
    //实时存入到vuex中
    this.$store.dispatch("setHomeListTop",e.target.scrollTop);    
  }
}
//...
activated(){  //滚动条位置的监听放到activated是因为此页面被keep-alive缓存了
  //this.$refs.scroll拿到滚动的dom，即scrollContainer，this.home_list_top是存入到vuex里的值
  this.$refs.scroll.scrollTop = this.home_list_top;        
  this.$refs.scroll.addEventListener("scroll",this.recordScrollPosition);    //添加绑定事件
},
deactivated(){  //keep-alive 的页面跳转时，移除scroll事件
  //清除绑定的scroll事件
  this.$refs.scroll.removeEventListener("scroll",this.recordScrollPosition);
}
```