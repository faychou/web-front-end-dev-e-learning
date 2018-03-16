# DOM
浏览器把 html 渲染成的树，也就是 Document 结构。

在内存中，存的不是html树，是一棵对应html各个节点的对象树，而且对象树的节点是与html树的节点一一对应的。

所以 DOM 是 JavaScript 操作网页的接口，全称为“文档对象模型”（Document Object Model）。它的作用是将网页转为一个 JavaScript 对象，从而可以用脚本进行各种操作（比如增删内容）。

## 节点
最顶层的节点 document，代表整个文档。html 代表根节点，是文档里面最高的一层，其他所有的 html 标签都是他的下级。

### Node.childNodes
返回一个 NodeList 集合（伪数组），成员包括当前节点的所有子节点。注意，除了 HTML 元素节点，该属性返回的还包括 Text 节点(标签之间的换行符也算)和 Comment 节点。如果当前节点不包括任何子节点，则返回一个空的 NodeList 集合。由于 NodeList 对象是一个动态集合，一旦子节点发生变化，立刻会反映在返回结果之中。

### Node.firstChild 和 Node.lastChild
firstChild 属性返回当前节点的第一个子节点，如果当前节点没有子节点，则返回 null。

lastChild 属性返回当前节点的最后一个子节点，如果当前节点没有子节点，则返回 null。

### Node.nextSibling 和 Node.previousSibling
nextSibling 属性返回紧跟在当前节点后面的第一个同级节点。如果当前节点后面没有同级节点，则返回 null。

previousSibling 属性返回当前节点前面的、距离最近的一个同级节点。如果当前节点前面没有同级节点，则返回 null。

### Node.parentNode
parentNode 属性返回当前节点的父节点。对于一个节点来说，它的父节点只可能是三种类型：element 节点、document 节点和 documentfragment 节点。

而且 document 节点和 documentfragment 节点，它们的父节点都是 null 。另外，对于那些生成后还没插入 DOM 树的节点，父节点也是 null。

### Node.parentElement
parentElement 属性返回当前节点的父 Element 节点。如果当前节点没有父节点，或者父节点类型不是 Element 节点，则返回 null。

> 注意： 在 IE 浏览器中，只有 Element 节点才有该属性，其他浏览器则是所有类型的节点都有该属性。

### ownerDocument
该返回当前节点所在的顶层文档对象，即 document 对象。

## 节点属性
### nodeName
返回 node 的名字，如果是 element 那名字是大写的，其他的名字前面写上 #。

如果是 element，那么 nodeName === tagName。

如果是 text，那么 nodeName = #text， tagName = undefined。

### nodeType
返回 node 的类型，一般用数字表示，1 表示 element(也可以用 Node.ELEMENT_NODE 来表示)，3 表示 text(Node.TEXT_NODE)。

* Node.DOCUMENT_NODE：值为 9，一个 Document 节点
* Node.DOCUMENT_TYPE_NODE：值为 10，doctype标签
* Node.ELEMENT_NODE：值为 1，代表一个元素节点
* Node.ATTRIBUTE_NODE：值为 2，标签的属性，已被弃用
* Node.TEXT_NODE：值为 3，Element 或者 Attr 中实际的文字
* Node.COMMENT_NODE：值为 8，注释
* Node.DOCUMENT_FRAGMENT_NODE：值为11，文档的片段

而我们最常用的就是 Document、Element、Text。

### nodeValue
nodeValue 属性返回或设置当前节点的值。对于 text, comment 节点来说, nodeValue 返回该节点的文本内容，对于 attribute 节点来说, 返回该属性的属性值，而对于 document 和 element 节点来说，返回 null。

## DOM 操作
### createElement()
创建一个元素节点。

### createTextNode()
创建一个文本节点。

### node.appendChild(childNode)
将一个节点添加到指定父节点的子节点列表的末尾。

### node.insertBefore(newNode,oldNode)
在当前节点的某个子节点之前再插入一个子节点。

### node.removeChild() 和 node.replaceChild()
removeChild 方法是从当前节点删除一个子节点，不过内存里面依然存在，只不过不在页面显示了，返回的就是被移除的那个节点。所以说一个节点移除以后，依然可以使用它，比如插入到另一个节点下面。

replaceChild 方法用于将一个新的节点，替换掉当前节点的一个子节点。它接受两个参数，第一个参数是用来替换的新节点，第二个参数将要被替换走的子节点。它返回被替换走的那个节点。

### node.cloneNode()
克隆一个 node，分为浅拷贝和深拷贝：

浅拷贝，Node.cloneNode() 只克隆元素节点本身，而不会克隆它的子节点。包括它的文本节点。

深拷贝，Node.cloneNode(true) 克隆元素的所有属性以及子节点。

### node.contains(childNode)
判断一个节点是不是另一个节点的子节点。

### node.hasChildNodes()
判断该节点是否还有子节点，有子节点就返回 true。

### node.normalize()
就是规范化的意思。什么是规范化，在一个"规范化"后的 DOM 树中，不存在一个空的文本节点，或者两个相邻的文本节点。

``` js
var wrapper = document.createElement("div");

wrapper.appendChild(document.createTextNode("Part 1 "));
wrapper.appendChild(document.createTextNode("Part 2 "));

// 这时(规范化之前),wrapper.childNodes.length === 2
// wrapper.childNodes[0].textContent === "Part 1 "
// wrapper.childNodes[1].textContent === "Part 2 "

wrapper.normalize();
// 现在(规范化之后), wrapper.childNodes.length === 1
// wrapper.childNodes[0].textContent === "Part 1 Part 2"
```

## DOM 属性
### getAttribute()

### setAttribute()

### style

### getComputedStyle
获取指定 DOM 的所有 css 属性。

``` js
//语法: var style = window.getComputedStyle("元素", "伪类"|null);

var h3 = document.getElementById("1234");
var style = window.getComputedStyle(h3,null);
```

#### getComputedStyle 与 style 区别?
* getComputedStyle 只读,而 style 可读可写；
* getComputedStyle 把所有 CSS 都列出来(包括未设置的),而 style 只列举设置了的；
* getComputedStyle.length 表示浏览器支持的 CSS 属性个数,而 style.length 为 0。

#### 兼容IE6~8
``` js
elementStyle = (element.currentStyle ? element.currentStyle : window.getComputedStyle(element, null)));
```
