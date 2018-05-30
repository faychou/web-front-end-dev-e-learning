# class
constructor 方法是类的默认方法，通过 new 命令生成对象实例时，自动调用该方法。

``` js
class Person {
  constructor(name,age) {
    this.name = name
    this.age = age
  }
  showName() {
    console.log('姓名：' + this.name)
  }
}

const person1 = new Person('faychou',18)
person1.showName
```

> 注意：

1. constructor 只做初始化；
2. 这里的 showName 实际上就是放在原型上的方法，可以通过 `console.dir(Person)` 查看。