# Redis-数据结构
Redis 的每一种数据结构都由一个 `key` 和 `value` 组成。所有数据结构的 `key` 的值都是任意合法的字符串，不同的数据结构的区别就在于 `value` 存储的值的不同而不同。

Redis 分为五种基础数据结构：`String`、`Hash`、`List`、`Set`、`Sort Set`。三种复杂数据结构：`Bitmaps`、`Hyperloglog`、`Geo`。

### String

字符串，是构建其他数据结构的基础。

#### `set`

赋值。

``` nginx
# 语法
set key value

# 例如
set test 123
```

#### `get`

取值。

``` nginx
# 语法
get key

# 例如
get test
```

#### `getset`

设置值并取值。

``` nginx
# 语法
getset key value

# 例如
getset test 321
```

#### `mset`/`mget`

设置获取多个键值。

``` nginx
# 语法
mset key value [key value...] 
mget key [key...]

# 例如
mset k1 v1 k2 v2 k3 v3
mget k1 k2
```

#### `del`

`del` 命令用于删除一个或多个 `key`，多个 `key` 之间用空格分隔，其返回值为整数，表示成功删除了多少个存在的 `key`，因此，如果只删除一个 `key`，则可以从返回值中判断是否成功，如果删除多个 `key`，则只能得到删除成功的数量。

``` nginx
# 语法
del key [key ...] # 时间复杂度O(n)

# 例如
del test
del test1 test2 test3
```

#### `incr`/`incrby`

递增数字 当存储的字符串是整数时，Redis 提供了一个实用的命令 `INCR`，其作用是让当前键值递增，并返回递增后的值。

而 `incrby` 用于增加指定的整数。

``` nginx
# 语法
incr key
incrby key increment

# 例如
set num 1
incr num # (integer) 2
incrby num 2 # (integer) 4
```

#### `decr `/`decryby`

递减数值 `decr`。

减少指定的数值 `decryby`。

``` nginx
# 语法
decr key
decryby key decrement

# 例如
set num 7
decr num # (integer) 6
decrby num 2 # (integer) 4
```

#### `append`

向键值的末尾追加value。如果键不存在则将该键的值设置为value，即相当于 SET key value。返回值是追加后字符串的总长度。

``` nginx
# 语法
append key value

# 例如
set str hello
append str "world"
```

#### `strlen`

获取字符串长度，如果键不存在则返回0。

``` nginx
# 语法：
strlen key

# 例如
strlen str
```

### Hash

散列类型，它提供了字段和字段值的映射。字段值只能是字符串类型，不支持散列类型、集合类型等其它类型。

#### `hset`

赋值，不区分插入和更新操作，当执行插入操作时返回1，当执行更新操作时返回0。

``` nginx
# 语法
hset key field value

# 例如
hset user username zhangsan
```

#### `hmset`

一次设置多个字段值。

``` nginx
# 语法
hmset key field value [field value...]

# 例如
hmset user age 20 username lisi
```

#### `hsetnx`

当字段不存在时赋值，类似 `hset`，区别在于如果字段存在，该命令不执行任何操作。

``` nginx
# 语法
hsetnx key field value

# 例如
hsetnx user age 30
```

#### `hget`

一次获取一个字段值。

``` nginx
# 语法
hget key field

# 例如
hget user username
```

#### `hmget`

一次可以获取多个字段值。

``` nginx
# 语法
hmget key field [field...]

# 例如
hmget user age username
```

#### `hgetall`

获取所有字段值。

``` nginx
# 语法
hgetall key

# 例如
hgetall user
```

#### `hdel`

可以删除一个或多个字段，返回值是被删除的字段的个数。

``` nginx
# 语法
hdel key field [field...]

# 例如
hdel user age
hdel user age username
```

#### `hincrby`

增加数字。

``` nginx
# 语法
hincrby key field increment

# 例如
hincrby user age 2
```

#### `hexists`

判断字段是否存在。

``` nginx
# 语法
hexists key field

# 例如
hexists user age
```

#### `hkeys`

只获取字段名或字段值。

``` nginx
# 语法
hkeys key hvals key

# 例如
hkeys user
```

#### `hlen`

获取字段数量 。

``` nginx
# 语法
hlen key

# 例如
hlen user
```



### List

Redis 的 List 是采用来链表来存储的，所以对于 Redis 的 List 数据类型的操作，是操作 List 的两端数据来操作的。

#### `lpush`

向列表左边增加元素。

``` nginx
# 语法
lpush key value [value...]

# 例如
lpush list:1 1 2 3
```

#### `rpush`

向列表右边增加元素。

``` nginx
# 语法
rpush key value [value...]

# 例如
rpush list:1 4 5 6
```

#### `lrange`

查看列表命令是列表类型最常用的命令之一，获取列表中的某一片段，将返回 `start`、`stop` 之间的所有元素（包含两端的元素），索引从0开始。索引可以是负数，如：“-1”代表最后边的一个元素。

``` nginx
# 语法
lrange key start stop

# 例如
lrange list:1 0 2
lrange list:1 0 -1
```



### Set

集合，在哈希列表的基础上实现。

### Sort Set

有序集合。

### Bitmaps

位图，在 `string` 的基础上进行位操作，可以实现节省空间的数据结构。

### Hyperloglog

用于估计一个 `set` 中元素数量的概率性的数据结构。

### Geo

`geospatial` 地理空间索引半径查询。

### BloomFilter

布隆过滤器。

### Redis 的通用命令

#### `keys`

`keys` 命令的作用是列出 Redis 所有的 `key`,该命令的时间复杂度为 `O(N)`，`N` 随着 Redis 中 `key` 的数量增加而增加，因此 Redis 有大量的 `key`，`keys` 命令会执行很长时间，而由于 Redis 是单线程，某个命令耗费过长时间，则会导致后面的的所有请求无法得到响应，因此，千万不要轻易在生产服务器上使用 `keys` 命令。

``` nginx
# 语法
keys pattern #pattern可为一个包含匹配模式的字符串，可以包含*,+,?,[a-z]等模式。

# 例如
keys hello*
```

#### `exists`

`exists` 命令用于判断一个或多个 `key` 是否存在，判断多个 `key` 时，`key` 之间用空格分隔，`exists` 的返回值为整数，表示当前判断有多少个 `key` 是存在的。

``` nginx
# 语法
exists key [key ...] # 时间复杂度O(1)

# 例如
exists test1 test2 
```



#### `expire`/`pexpire`

`expire` 设置 `key` 在多少秒之后过期，`pexpire` 设置 `key` 在多少毫秒之后过期,成功返回1，失败返回0。

``` nginx
# 语法
expire key seconds # 时间复杂度为O(1)
pexpire key milliseconds # 时间复杂度为O(1)

# 例如
expire test 10 # 设置10秒后过期
pexpire test1_value 10000 # 设置10000毫秒(10s)后过期
```

#### `ttl`/`pttl`

`ttl` 和 `pttl` 命令用于获取 `key` 的过期时间，其返回值为整型，代表的意义分为几种情况：

* 当 `key` 不存在或过期时间，返回-2。

* 当 `key` 存在且永久有效时，返回-1。

* 当 `key` 有设置过期时间时，返回为剩下的秒数(`pttl` 为毫秒数)

``` nginx
# 语法
ttl key # 时间复杂度O(1)
pttl key # 时间复杂度O(1)

# 例如
ttl test
```

#### `expireat`/`pexpireat`

设置 `key` 在某个时间戳过期，`expreat` 参数时间戳用秒表示，而 `pexpireat` 则用毫秒表示，与 `expire` 和 `pexpire` 功能类似，返回1表示成功，0表示失败。

``` nginx
# 语法
expireat key timestamp # 时间复杂度为O(1)
pexpireat key milliseconds-timestamp # 时间复杂度为O(1)

# 例如
expireat test 1560873600 # 2019-06-19 00:00:00
pexpireat test1 156087360000 # 2019-06-19 00:00:00的毫秒表示
```

#### `persist`

移除 `key` 的过期时间，将 `key` 设置为永久有效，当 `key` 设置了过期时间，使用 `persist` 命令移除后返回1，如果 `key` 不存在或本身就是永久有效的，则返回0。

``` nginx
# 语法
persist key # 时间复杂度O(1)

# 例如
persist test
```

#### `type`

判断 `key` 是什么类型的数据结构，返回值为 `string`、`list`、`set`、`hash`、`zset` 这5种基础数据结构。

``` nginx
# 语法
type key # 时间复杂度O(1)

# 例如
type test
```

> `geo`、`hyperloglog` 和 `bitmaps` 等复杂的数据结构，都是在这五种基础数据结构上实现，比如 `geo` 是 `zset` 类型，`hyperloglog` 和 `bitmaps` 都为 `string`。

### Redis 中的内存维护策略

Redis 作为优秀的中间缓存件，时常会存储大量的数据，即使采取了集群部署来动态扩容，也应该即时的整理内存，维持系统性能。

#### 方式一：为数据设置超时时间

``` nginx
expirekeytime(以秒为单位)--这是最常用的方式
setex(Stringkey,intseconds,Stringvalue)--字符串独有的方式
```

如果没有设置时间，那缓存就是永不过期。

#### 方式二：采用LRU算法动态将不用的数据删除

内存管理的一种页面置换算法，对于在内存中但又不用的数据块（内存块）叫做 `LRU`，操作系统会根据哪些数据属于 `LRU` 而将其移出内存而腾出空间来加载另外的数据。

* `volatile-lru`：设定超时时间的数据中,删除最不常使用的数据。

* allkeys-lru`：查询所有的 `key` 中最近最不常使用的数据进行删除，这是应用最广泛的策略。

* `volatile-random`：在已经设定了超时的数据中随机删除.。

* `allkeys-random`：查询所有的 `key`，之后随机删除。

* `volatile-ttl`：查询全部设定超时时间的数据，之后排序，将马上将要过期的数据进行删除操作。

* `noeviction`：如果设置为该属性,则不会进行删除操作，如果内存溢出则报错返回。

* `volatile-lfu`：从所有配置了过期时间的键中驱逐使用频率最少的键。

* `allkeys-lfu`：从所有键中驱逐使用频率最少的键。

