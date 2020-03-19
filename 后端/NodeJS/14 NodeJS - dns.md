## 内置模块-`dns`

DNS 是因特网上作为域名和 IP 地址相互映射的一个分布式数据库，能够使用户更方便的访问互联网，而不用去记住能够被机器直接读取的 IP 数串。简单的说，就是把域名（网址）解析成对应的 IP 地址。Node.js 的 dns 模块，提供了 DNS 解析功能。

#### `dns.lookup(hostname[, options], callback)`

使用操作系统底层的 DNS 服务解析，将一个域名（如：'www.faychou.cn'）解析为第一个找到的 A 记录（IPv4）或 AAAA 记录（IPv6）。

* `hostname` 表示要解析的域名。
* `options` 可以是一个对象或整数。如果没有提供 `options` 参数，则 IP v4 和 v6 地址都可以。如果 `options` 是整数，则必须是 4 或 6。如果 `options` 是对象时，会包含以下两个可选参数：
  - `family`：可选，IP 版本。如果提供，必须是 4 或 6。不提供则，IP v4 和 v6 地址都可以
  - `hints`：可选。如果提供，可以是一个或者多个 `getaddrinfo` 标志。若不提供，则没有标志会传给 `getaddrinfo`。

* `callback` 回调函数，参数包含(`err`,、`address`、` family`)。出错时，参数 `err` 是 `Error` 对象。`address` 参数表示 IP v4 或 v6 地址。`family` 参数是 4 或 6，表示 `address` 协议版本。

``` js
const dns = require('dns')

dns.lookup(`www.faychou.cn`, (err, address, family) => {
  if (err) throw err
  console.log( address, family)
})
```

#### `dns.resolve(hostname[, rrtype], callback)`

连接到 DNS 服务器解析域名，在 `dns` 模块中，除 `dns.lookup()` 方法外都是使用 DNS 服务器进行域名解析，解析时需要连接到网络。

`dns.resolve` 将一个域名（如 'www.faychou.cn'）解析为一个` rrtype` 指定类型的数组。

* `hostname` 表示要解析的域名。

* `rrtype` 有以下可用值:

  | rrtype  |   records 包含   | 结果的类型 |       快捷方法       |
  | :-----: | :--------------: | :--------: | :------------------: |
  |   'A'   | IPv4 地址 (默认) |   string   |   `dns.resolve4()`   |
  | 'AAAA'  |    IPv6 地址     |   string   |   `dns.resolve6()`   |
  |  'ANY'  |     任何记录     |   Object   |  `dns.resolveAny()`  |
  | 'CNAME' |   规范名称记录   |   string   | `dns.resolveCname()` |
  |  'MX'   |   邮件交换记录   |   Object   |  `dns.resolveMx()`   |
  | 'NAPTR' | 名称权限指针记录 |   Object   | `dns.resolveNaptr()` |
  |  'NS'   |  名称服务器记录  |   string   |  `dns.resolveNs()`   |
  |  'PTR'  |     指针记录     |   string   |  `dns.resolvePtr()`  |
  |  'SOA'  |   开始授权记录   |   Object   |  `dns.resolveSoa()`  |
  |  'SRV'  |     服务记录     |   Object   |  `dns.resolveSrv()`  |
  |  'TXT'  |     文本记录     |  string[]  |  `dns.resolveTxt()`  |

* `callback` 回调函数，参数包含(`err`、`addresses`)。出错时，参数 `err` 是 Error 对象。`addresses` 根据记录类型的不同返回值也不同。

``` js
const dns = require('dns')

dns.resolve('www.faychou.cn', 'A', (err, addresses) => {
  if (err) throw err
  console.log(`IP地址 : ${JSON.stringify(addresses)}`)
})

// or
dns.resolve4('www.faychou.cn', (err, addresses) => {
  if (err) throw err
  console.log(`IP地址 : ${JSON.stringify(addresses)}`)
})
```

#### `dns.reverse(ip, callback)`

* `ip` 表示要反向解析的 IP 地址。
* `callback` 回调函数，参数包含(`err`、`domains`)。出错时，参数 `err` 是 `Error` 对象。`domains` 解析后的域名数组。

``` js
dns.reverse('8.8.8.8', (err, domains) => {
  if (err) throw err
  console.log(domains) // [ 'dns.google' ]
})
```

#### `dns.lookupService(address, port, callback)`

* `address` 表示要解析的 IP 地址字符串。
* `port` 表示要解析的端口号。
* `callback` 回调函数，参数包含(`err`、 `hostname`、 `service`)。出错时，参数 `err` 是 `Error` 对象。

``` js
dns.lookupService('127.0.0.1', 80, function(err, hostname, service) {
  if (err) throw err
  console.log(hostname, service)
})
```

