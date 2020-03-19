# Proxy
Proxy 对象就是可以让你去对 JavaScript 中的一切合法对象的基本操作进行自定义，然后用你自定义的操作去覆盖其对象的基本操作。

``` js
let p = new Proxy(target, handler);
```

* target：要代理的对象，可以是 JavaScript 中的任何合法对象(数组, 对象, 函数等)；

* handler：自定义操作方法的一个集合；

* p：被代理后的新对象，它拥有 target 的一切属性和方法，只不过其行为和结果是在 handler 中自定义的。

``` js
let obj = {
  a: 1,
  b: 2,
}

const p = new Proxy(obj, {
  get(target, key, value) {
    if (key === 'c') {
      return '我是自定义的一个结果'
    } else {
      return target[key]
    }
  },

  set(target, key, value) {
    if (value === 4) {
      target[key] = '我是自定义的一个结果'
    } else {
      target[key] = value
    }
  }
})

console.log(obj.a) // 1
console.log(obj.c) // undefined
console.log(p.a) // 1
console.log(p.c) // 我是自定义的一个结果

obj.name = 'Fay';
console.log(obj.name); // Fay
obj.age = 4;
console.log(obj.age); // 4

p.name = 'Fay';
console.log(p.name); // Fay
p.age = 4;
console.log(p.age); // 我是自定义的一个结果
```

在上面代码中，第二个参数 handler 对象是由 get 和 set 两个函数方法组成的，这两个方法会在一个对象被 get 和 set 时被调用执行，以代替原生对象上的操作。实际上 handler 是 ES6 所新设计的一个对象，它的作用就是用来自定义代理对象的各种可代理操作。它本身一共有 13 种方法，每种方法都可以代理一种操作。

``` js
// 读取代理对象的原型时触发，如执行 Object.getPrototypeOf(proxy)
handler.getPrototypeOf()

// 设置代理对象的原型时触发，如执行 Object.setPrototypeOf(proxy, null)
handler.setPrototypeOf()

// 判断一个代理对象是否是可扩展时触发，如执行 Object.isExtensible(proxy)
handler.isExtensible()

// 让一个代理对象不可扩展时触发，如执行 Object.preventExtensions(proxy)
handler.preventExtensions()

// 获取代理对象某个属性描述时触发，如执行 Object.getOwnPropertyDescriptor(proxy, "foo")
handler.getOwnPropertyDescriptor()

// 定义代理对象某个属性描述时触发，如执行 Object.defineProperty(proxy, "foo", {})
handler.defineProperty()

// 判断代理对象是否拥有某个属性时触发，如执行 "foo" in proxy
handler.has()

// 读取代理对象的某个属性时触发，如执行 proxy.foo
handler.get()

// 给代理对象的某个属性赋值时触发，如执行 proxy.foo = 1
handler.set()

// 删除代理对象的某个属性时触发，如执行 delete proxy.foo
handler.deleteProperty()

// 获取代理对象的所有属性键时触发，如执行 Object.getOwnPropertyNames(proxy)
handler.ownKeys()

// 调用一个目标对象为函数的代理对象时触发，如执行 proxy()
handler.apply()

// 给一个目标对象为构造函数的代理对象构造实例时触发，如执行 new proxy()
handler.construct()
```

### Proxy 作用
* 拦截和监视外部对对象的访问；

* 降低函数或类的复杂度；

* 在复杂操作前对操作进行校验或对所需资源进行管理。

如，可以监听对象身上发生了什么事情，并在这些事情发生后执行一些相应的操作。一下子让我们对一个对象有了很强的追踪能力，同时在数据绑定方面也很有用处。

``` js
// 定义被监听的目标对象
let engineer = { name: 'Fay', age: 50 }

// 定义处理程序
let interceptor = {
  set(receiver, property, value) {
    console.log(property, 'is changed to', value)
    receiver[property] = value
  }
}

// 创建代理以进行侦听
engineer = new Proxy(engineer, interceptor)

// 做一些改动来触发代理
engineer.age = 18; // 控制台输出：age is changed to 18
```