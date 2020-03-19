# 实用方法
## 双向数据绑定
采用订阅发布模式，利用 Object.defineProperty 来实现 MVVM。

``` js
const input = document.querySelector('input')
const obj = {}

Object.defineProperty(obj, 'data', {
  enumerable: false,  // 不可枚举
  configurable: false, // 不可删除
  set(value){
    input.value = value
    _value = value
    // console.log(input.value)
  },
  get(){
    return _value
  }
}) 
obj.data = '123'
input.onchange = e => {
  obj.data = e.target.value
}
```

## 数据扁平化
``` js
var data = [
  {
    value: "浙江",
    children: [
      { value: "杭州", children: [{ value: "西湖", children: [{ value: "A区" }] }] }
    ]
  },
  {
    value: "四川",
    children: [
      {
        value: "成都",
        children: [
          { value: "锦里", children: [{ value: "D区" }] },
          { value: "方所", children: [{ value: "B区" }] }
        ]
      },
      { value: "阿坝", children: [{ value: "九寨沟", children: [{ value: "C区" }] }] }
    ]
  }
];


// 把一个树形结构，变成扁平化列表
function toFlat(tree, [key, ...rest], result = {}) {
  if (result[key] == null) {
    result[key] = tree.value;
  } else if (result[key] !== tree.value) {
    result = {
      ...result,
      [key]: tree.value
    };
  }
  return rest.length ? toFlatList(tree.children, rest, result) : [result];
}

// 把一个树形结构的列表，变成扁平化列表
function toFlatList(treeList, keys, result = {}) {
  return treeList.reduce(
    (list, tree) => list.concat(toFlat(tree, keys, result)),
    []
  );
}

// 转换树形结构为列表结构
function treeToList(treeList = [], keys) {
  return toFlatList(treeList, keys);
}

var result = treeToList(data, ["province", "city", "name", "area"]);
console.log("result", result);
```

## 数据树形化
``` js
var data = [
  {
    province: "浙江",
    city: "杭州",
    name: "西湖",
    area: 'A区'
  },
  {
    province: "四川",
    city: "成都",
    name: "锦里",
    area: 'D区'
  },
  {
    province: "四川",
    city: "成都",
    name: "方所",
    area: 'B区'
  },
  {
    province: "四川",
    city: "阿坝",
    name: "九寨沟",
    area: 'C区'
  }
];

// 将一个扁平对象，根据 keys 树形化
function toTree(obj, [key, ...rest], result = {}) {
    if (result.value == null) {
        result.value = obj[key]
        if (rest.length) {
            result.children = toTreeList(obj, rest)
        }
    } else if (result.value === obj[key] && rest.length) {
        toTreeList(obj, rest, result.children)
    }
    return result
}

// 将一个扁平对象的树形化产物，不重复地放到 list 里
function toTreeList(obj, keys, list = []) {
    let value = obj[keys[0]]
    let target = list.find(item => item.value === value)
    if (target) {
        toTree(obj, keys, target)
    } else {
        list.push(toTree(obj, keys))
    }
    return list
}

// 将一个扁平化对象组成的列表，变成树形化的列表
function listToTree(list=[], keys=[]) {
    return list.reduce(
        (result, obj) => toTreeList(obj, keys, result),
        []
    )
}

console.log('result', listToTree(data, ['province', 'city', 'name', 'area']));
```

又如：扁平对象，转为树形对象。parent字段为空字符串的节点为根节点：

``` js
var input = {
  h3: {
    parent: 'h2',
    name: '副总经理(市场)'
  },
  h1: {
    parent: 'h0',
    name: '公司机构'
  },
  h7: {
    parent: 'h6',
    name: '副总经理(总务)'
  },
  h4: {
    parent: 'h3',
    name: '销售经理'
  },
  h2: {
    parent: 'h1',
    name: '总经理'
  },
  h8: {
    parent: 'h0',
    name: '财务总监'
  },
  h6: {
    parent: 'h4',
    name: '仓管总监'
  },
  h5: {
    parent: 'h4',
    name: '销售代表'
  },
  h0: {
    parent: '',
    name: 'root'
  }
};

var plain2Tree = function (obj) {
  var key, res
  for(key in obj) {
    var parent = obj[key].parent
    if(parent === '') {
      res = obj[key]
    } else {
      obj[parent][key] = obj[key]
    }
  }
  return res
}
```