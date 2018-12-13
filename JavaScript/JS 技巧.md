# JS 技巧
### JavaScript 事件监听兼容写法
包括 IE 下 this 指向问题。

``` js
function addEventHandler(oTarget, sEventType, fnHandler) {
  if(oTarget.addEventListener) {
    oTarget.addEventListener(sEventType,fnHandler,false);
  } else if(oTarget.attachEvent) {
    oTarget.attachEvent("on"+sEventType,function() {
      return fnHandler.call(oTarget,window.event);
    });
  } else{
    oTarget["on"+sEventType]=fnHandler;
  }
}
```

### 使用!!操作符转换布尔值
有时候我们需要对一个变量查检其是否存在或者检查值是否有一个有效值，如果存在就返回 true 值。为了做这样的验证，我们可以使用 `!!` 操作符来实现是非常的方便与简单。对于变量可以使用 `!!variable` 做检测，只要变量的值为:0、null、" "、undefined 或者 NaN 都将返回的是 false，反之返回的是 true。这种做法也就是强制转任何值为布尔值。

``` js
console.log(!!10); //true
console.log(!!0); //false
console.log(!!"abc"); //true
console.log(!!""); //false

// 简单地说就是把右侧的值转为 bool 值
```

### 添加和删除 class
``` js
//添加 Class
document.getElementById("MyElement").className += " MyClass";

//删除 Class
document.getElementById("MyElement").className = document.getElementById("MyElement").className.replace(/(?:^|\s)MyClass(?!\S)/,'');
```

### 检查一个字符串是否为空最简单的方法
``` js
if (strValue) {
  //do something
}
```

### jQuery 中判断一个元素是否存在
``` js
if ($(selector).length)
```

### 克隆对象
``` js
function clone(obj) {
  // Handle the 3 simple types, and null or undefined
  if (null == obj || "object" != typeof obj) return obj;

  // Handle Date
  if (obj instanceof Date) {
    var copy = new Date();
    copy.setTime(obj.getTime());
    return copy;
  }

  // Handle Array
  if (obj instanceof Array) {
    var copy = [];
    for (var i = 0, var len = obj.length; i < len; ++i) {
      copy[i] = clone(obj[i]);
    }
    return copy;
  }

  // Handle Object
  if (obj instanceof Object) {
    var copy = {};
      for (var attr in obj) {
        if (obj.hasOwnProperty(attr)) copy[attr] = clone(obj[attr]);
        }
    return copy;
  }

  throw new Error("Unable to copy obj! Its type isn't supported.");
}
```

### JavaScript中浮点数的相等判断不能用 ==
``` js
console.log(0.1+0.2 == 0.3);//false
console.log(Math.abs(0.1+0.2 - 0.3) < 0.000001);//true
```

### 获取 URL 中的参数
``` js
function getQueryString() {
  var result = {}, queryString = location.search.substring(1),
    re = /([^&=]+)=([^&]*)/g, m;

  while (m = re.exec(queryString)) {
    result[decodeURIComponent(m[1])] = decodeURIComponent(m[2]);
  }

  return result;
}

// demo
var myParam = getQueryString()["myParam"];
```

### 根据日计算年龄
``` js
function getAge(dateString) {
  var today = new Date();
  var birthDate = new Date(dateString);
  var age = today.getFullYear() - birthDate.getFullYear();
  var m = today.getMonth() - birthDate.getMonth();

  if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
    age--;
  }
  return age;
}
```

### 判断当前页面是否被放入了 iframe 中
``` js
if(self==top){
  //not in iframe
}else{
  //in iframe
}
```

### 生成n位随机数
``` js
let getRandom = n => Math.random().toString().slice(-n);
getRandom(6)   //6位随机数
```

### 生成16进制颜色
``` js
let colorCode = '#' +('00000' +(Math .random()* 0x1000000 << 0).toString(16)).slice(- 6);
```

### n到m间随机整数
``` js
et randomNum = (n,m) => Math.floor(Math.random()*(m-n) + n);
randomNum(2,10)   //2-10之间的整数
```

### url参数转json
``` js
let urlToJson = url => {
  let json = {};
  if (!!!url) return json;
  let data = url.split('?')[1] ? url.split('?')[1].split('&') : [];
  for(let i=0; i<data.length; i++) {
    let k = data[i].split('=');
    k[0] && (json[k[0]] = k[1] || '');
  }
  return json;
}
```

### 给 localStorage 设置一个过期时间
方法一：

``` js
/**
 * 使用方法：
 * const mLzLocalStorage = new LzLocalStorage('lz');
 *
 * mLzLocalStorage.set('name', 'lz', -1);
 * mLzLocalStorage.get('name'); // 输出lz 不会过期
 *
 * mLzLocalStorage.set('name', 'lz', 3 * 1000);
 * mLzLocalStorage.get('name'); // 3秒内容可以取到lz, 3秒过后获取，取不到值，
 */

// 缓存方式
const localStorage = window.localStorage;

/**
 * 设置缓存
 * @param {String} key 键
 * @param {*} value 值
 */
function setItem(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
}

/**
 * 获取缓存
 * @param {String} key 键
 * @returns {*|null}
 */
function getItem(key) {
    const result = localStorage.getItem(key);
    if (result) {
        return JSON.parse(result);
    }
    return null;
}

/**
 * 移除缓存
 * @param key
 */
function removeItem(key) {
    localStorage.removeItem(key);
}

/**
 * 获取当前时间戳（毫秒）
 * @returns {number}
 */
function getNow() {
    return Date.parse(new Date());
}

// 缓存表前缀
const mapKey = 'lz-map-';

/**
 * localStorage 操作类
 * 支持过期时间
 */
class LzLocalStorage {

    /**
     * @param {String} namespace 命名空间
     */
    constructor(namespace = '') {
        // 缓存命名空间
        this.namespace = namespace;
        // 缓存表key
        this.mapKey = mapKey + this.namespace;
        // 缓存表
        this.map = getItem(this.mapKey) || {};
    }

    /**
     * 设置缓存
     * @param {String} key 键
     * @param {*} value 值
     * @param {Number} expires 过期时间（毫秒） -1不过期，
     */
    set(key, value, expires = -1) {
        const k = this.getReallyKey(key);
        const obj = {
            expires,
            time: getNow()
        };
        setItem(k, value);
        this.map[key] = obj;
        setItem(this.mapKey, this.map);
    }

    /**
     * 获取真实的缓存键
     * @param {String} key 键
     * @returns {string}
     */
    getReallyKey(key) {
        return this.namespace + "-" + key;
    }

    /**
     * 获取缓存内容
     * @param {String} key 键
     * @param {*} defaultValue 默认返回值
     * @returns {*} 若未取到则返回defaultValue
     */
    get(key, defaultValue) {
        const k = this.getReallyKey(key);
        let obj = this.map[key];
        // 缓存存在
        if (obj) {
            // 缓存无过期时间
            if (obj.expires > -1) {
                // 在缓存时间内
                if (getNow() - obj.time <= obj.expires) {
                    return getItem(k) || defaultValue;
                } else {
                    this.delete(key);
                }
            } else {
                return getItem(k) || defaultValue;
            }
        }
        return defaultValue;
    }

    /**
     * 获取命名空间下所有缓存
     * @returns {Object}
     */
    getAll() {
        const result = {};
        for(const i in this.map) {
            result[i] = this.get(i);
        }
        return result;
    }

    /**
     * 删除单个缓存
     * @param {String} key 键
     */
    delete(key) {
        const k = this.getReallyKey(key);
        delete this.map[key];
        // 重置缓存表
        setItem(this.mapKey, this.map);
        // 删除缓存
        removeItem(k);
    }

    /**
     * 删除所有命名空间缓存
     */
    deleteAll() {
        for(const i in this.map) {
            this.delete(i);
        }
    }
}
```

方法二：

``` js
class storage {

	constructor(props) {
		this.props = props || {}
		this.source = this.props.source || window.localStorage
		this.initRun();
	}
	initRun(){
		/*
		* set 存储方法
		* @ param {String} 	key 键
		* @ param {String} 	value 值，存储的值可能是数组/对象，不能直接存储，需要转换 JSON.stringify
		* @ param {String} 	expired 过期时间，以分钟为单位
		*/
		const reg = new RegExp("__expires__");
		let data = this.source;
		let list = Object.keys(data);
		if(list.length > 0){
			list.map((key,v)=>{
				if( !reg.test(key )){
					let now = Date.now();
					let expires = data[`${key}__expires__`]||Date.now+1;
					if (now >= expires ) {
						this.remove(key);
					};
				};
				return key;
			});
		};
	}
	
set(key, value, expired) {
	/*
	* set 存储方法
	* @ param {String} 	key 键
	* @ param {String} 	value 值，
	* @ param {String} 	expired 过期时间，以分钟为单位，非必须
   * 存储的值可能是数组/对象，不能直接存储，需要转换 JSON.stringify	*/
	let source = this.source;
	source[key] = JSON.stringify(value);
	if (expired){
		source[`${key}__expires__`] = Date.now() + 1000*60*expired
	};
	return value;
}

get(key) {
	/*
	* get 获取方法
	* @ param {String} 	key 键
	* @ param {String} 	expired 存储时为非必须字段，所以有可能取不到，默认为 Date.now+1
	* 但存储时expired为非必须参数，所以默认为当前时间+1，即长期有效
	* 存储的值可能是数组/对象，取出后不能直接返回，需要转换 JSON.parse
	*/
	const source = this.source,
	expired = source[`${key}__expires__`]||Date.now() + 1;
	const now = Date.now();

	if ( now >= expired ) {
		this.remove(key);
		return;
	}
	return source[key] && JSON.parse(source[key]);
}

remove(key) {
	const data = this.source,
		value = data[key];
	delete data[key];
	delete data[`${key}__expires__`];
	return value;
}

}
```
