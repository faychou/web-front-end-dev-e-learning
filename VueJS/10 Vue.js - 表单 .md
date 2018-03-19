# Vue 表单
``` html
<input v-model="message">

<textarea v-model="message" placeholder="add multiple lines"></textarea>

<!-- 当选中时，`picked` 为字符串 "a" -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` 为 true 或 false -->
<input type="checkbox" v-model="toggle">

<!-- 当选中时，`selected` 为字符串 "abc" -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

> 当 v-bind 和 v-model 同时出现在一个 input 上时，这个 v-bind 会失效。

### 复选框
``` html
<input type="checkbox" v-model="checked">
```

这里的 v-model 绑定了 checked，但是 checked 这个变量应该是一个 boolean 值，表示这个 checkbox 是否被选中，是绑定到了 prop 属性。

另一种是给两个绑定值，如下：

``` html
<input type="checkbox" v-model="checked" :true-value="a" :false-value="b">
```

这里的 a 和 b 都是变量，选中的时候 checked 值等于 a，即 `app.checked = app.a`，否则 `app.checked = app.b`。这样 checked 就不是 boolean 值了。

> 注意：checkbox 单个复选框的时候，不能使用 `:value="xxx"` 绑定值，即使绑定了也没用，会被舍弃。

``` html
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>

<span>Checked names: {{ checkedNames }}</span>

<script>
  new Vue({
    el: '...',
    data: {
      checkedNames: [],
    }
  })
</script>
```

这里 `type="checkbox"` 是一定要有的，如果没有 `type="checkbox"`，会报错，type 值必须是字符串，不能通过 `v-bind` 进行绑定使用动态值，否则 Vue 会报错。

`data.checkedNames` 一定要是数组，当用户勾选了上面 checkbox 的其中一个时，它的 value 值会被丢到 `data.checkedNames` 这个数组中来。而它的 value 值则可以通过 `v-bind` 绑定一个动态数据。

在 HTML 中，通过相同的一个 name 值来确定这组 checkbox 是一个组的，而在这里，则是通过 `v-model` 的值是同一个值来确定是一个组的。这个时候就可以使用 `:value` 来动态绑定 value 值了，这和单个复选框不一样。

### 单选按钮
``` html
<input type="radio" id="one" value="One" v-model="picked">
<input type="radio" id="two" value="Two" v-model="picked">

<span>Picked: {{ picked }}</span>

<script>
  new Vue({
    el: '#example-4',
    data: {
      picked: '',
    }
  })
</script>
```

单选框组跟多个 checkbox 复选框组是一样的。包括 `:value` 绑定。唯一不同的是 picked 是一个单选值，所以是一个值，而不是数组。

### 列表
#### 单选列表
``` html
<select id="example-5" v-model="selected">
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<span>Selected: {{ selected }}</span>

<script>
  new Vue({
    el: '#example-5',
    data: {
      selected: null
    }
  })
</script>
```

#### 多选列表（绑定到一个数组）
``` html
<select id="example-6" v-model="selected" multiple style="width: 50px">
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<span>Selected: {{ selected }}</span>

<script>
  new Vue({
    el: '#example-6',
    data: {
      selected: []
    }
  })
</script>
```

因为 multiple 要选择的值是一组，因此 selected 是一个数组。

#### 动态渲染：
``` html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
   {{ option.text }}
  </option>
</select>

<span>Selected: {{ selected }}</span>

<script>
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
</script>
```

### 修饰符
#### .lazy
在默认情况下， v-model 在 input 事件中同步输入框的值与数据 (除了 上述 IME 部分)，但你可以添加一个修饰符 lazy ，从而转变为在 change 事件中同步：

``` html
<!-- 在 "change" 而不是 "input" 事件中更新 -->
<input v-model.lazy="msg" >
```

#### .number
如果想自动将用户的输入值转为 Number 类型（如果原值的转换结果为 NaN 则返回原值），可以添加一个修饰符 number 给 `v-model` 来处理输入值：

``` html
<input v-model.number="age" type="number">
```

因为在 `type="number"` 时 HTML 中输入的值也总是会返回字符串类型。

#### .trim
如果要自动过滤用户输入的首尾空格，可以添加 trim 修饰符到 `v-model` 上过滤输入：

``` html
<input v-model.trim="msg">
```