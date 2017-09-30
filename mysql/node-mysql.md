# node + mysql 入门教程
MySQL 是最流行的关系型数据库管理系统，关联数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。
## 一、安装与配置MySQL
### MAC安装
1、 官网直接下载MySQL社区版（MySQL Community Edition）;

2、 选择dmg的文件进行下载，下载完毕后，双击安装，一路默认即可；

>注意：最后会有一个弹出框，这里是你的mysql root账号的初始密码。

3、 打开系统偏好设置，选择mysql，点击start mysql server按钮开启服务;

4、 打开终端输入以下命令将mysql添加进环境变量

    echo "export PATH=$PATH:/usr/local/mysql/bin" >> ~/.bash_profile
5、 重启终端后输入以下代码查看，可以看到/usr/local/mysql/bin已经添加到PATH中

    echo $PATH
    
6、 在Windows上，安装时请选择UTF-8编码，以便正确地处理中文。

在Mac或Linux上，需要编辑MySQL的配置文件，把数据库默认的编码全部改为UTF-8。MySQL的配置文件默认存放在/etc/my.cnf或者/etc/mysql/my.cnf：

```
[client]
default-character-set = utf8

[mysqld]
default-storage-engine = INNODB
character-set-server = utf8
collation-server = utf8_general_ci
```

7、 直接输入以下命令来登陆mysql，密码就是安装时弹出框中的初始密码（注意mysql中每条语句以分号结束）：

    mysql -u root -p;

8、 或者不直接登录，使用以下命令修改密码后再登录（如果已经登录，可以输入 \q 后回车，可以退出登录）

    mysqladmin -u root -p password;
    
回车之后，先输入原密码，在输入新密码。
    
### 基础语法
#### 创建数据库
    CREATE DATABASE [IF NOT EXISTS] my_db;
    CREATE DATABASE my_db;
    
#### 查看数据库
    SHOW DATABASES;
    
#### 切换到该数据库
    USE my_db;
    
#### 查看当前使用的数据库
    SELECT database();
    
#### 创建表
    CREATE TABLE users(
        id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(30) NOT NULL,
        age TINYINT UNSIGNED NOT NULL,
        password VARCHAR(32) NOT NULL,
        regdate DATETIME default now()
        )ENGINE=InnoDB DEFAULT CHARSET=UTF8;
        
#### 查看当前数据库中的所有表
    show tables; 
    
#### 查看表结构
    desc table_name;
    
#### 插入数据
1、 为表中所有字段添加数据

    INSERT INTO users VALUES(null, 'fay', 18,,sysdate());

注意：
* values中的值必须与表中的字段一一对应。
* 插入的数据应与字段中的数据类型相同
* 数据的大小应该在列的规定范围内，例如不能将一个长度为80的字符串插入到长度为40个列中
* 字符和日期型数据应该包含在单引号中
* 如果要插入一个空值，不指定或者使用NULL

2、 按照指定列添加数据

    INSERT INTO users(列1名, 列2名,...) VALUES(列1值, 列2值,...);

注意：values中的值必须与列声明中的列一一对应。

3、 同时添加多条记录

    INSERT INTO users VALUES (value1,value2,value3...),(value1,value2,value3...),(value1,value2,value3),...;
    
#### 修改数据表
    增加列: ALTER TABLE users ADD colum datatype;

    修改列: ALTER TABLE users MODIFY colum datatype;

    删除列: ALTER TABLE users DROP colum;

    修改表名: rename TABLE users to new_table_name;

    修改列名: ALTER TABLE users change colum_name new_colum_name datatype;
    
#### 更新全部数据
    UPDATE 表名 SET 列名=值, 列名=值[,列名=值];
    
#### 按条件更新
    UPDATE 表名 SET 列名=值, 列名=值[,列名=值] WHERE 条件;
    
提示：
* UPDATE语句可以用新值更新原有表中行的列。
* SET字句指定要修改哪些列和要给与哪些值
* WHERE需要给定一个条件，表示要更新符号该条件的行，没有WHERE字句，则更新所有行
* 
#### 删除全部数据
    DELETE FROM users;
    
#### 根据条件删除
    DELETE FROM users WHERE 条件;
    
#### 删除数据表
    DROP TABLE users;
    
#### 查询数据库
    语法：SELECT [DISTINCT] *||{colum1, colum2, colum3...} FROM users WHERE 条件;

如，查询表中所有数据：

    SELECT * FROM users;
    
#### 删除数据库
    DROP DATABASE [IF EXISTS] users;

## 二、安装node-mysql：
当然进行这一步的前提是已安装了node.js，然后新建项目并cd到项目根目录下，依次执行以下操作：

    npm init
    npm install mysql --save

## 三、基本使用：
在项目根目录下新建index.js文件，编写以下代码：

``` javascript
var mysql = require('mysql');
var connection = mysql.createConnection({
    host : 'localhost',
    user : 'root',
    password : 'secret',
    database : 'my_db'
});

connection.connect(); //建立连接
// 执行SQL语句
connection.query('SELECT * FROM users', function(err, rows, fields) {
    if (err) throw err;
    console.log('The solution is: ', rows[0].solution);
});

connection.end(); //关闭连接
```
执行

    node index.js 
    
确保你在执行之前已经启动了Mysql服务。

### 注意事项：
1、 每个方法调用在连接队列中是按顺序执行。

2、 当使用 end() 函数关闭连接时候，你必须要确保你的所有查询都已经发送给了mysql服务器。否者end()之后的查询是无效的。

3、 一个连接只能隐式的调用一个query函数。

### 连接可选参数：
| 参数名              | 代表值          |
| -------------      |:-------------:|
| host               | 数据库的主机名（默认： localhost) |
| port               | 数据库服务器的端口（默认： 3306）|
| localAddress       | 使用TCP连接的源IP地址（可选）|
| socketPath         | 使用Unix域套接字连接的路径，当使用 host 与 port 的时候可以忽略。|
| user               | 这个是MySQL身份验证的用户名。|
| password           | MySQL用户的密码。|
| database           | 连接的数据库名(可选)。|
| charset            | 连接之后的字符编码(默认:' UTF8_GENERAL_CI',值得提醒的是，这个值必须都是大写字母)。|
| timezone           | 这个是储存数据的是当前时间。(默认:local)。|
| connectTimeout     | MySQL初始连接的超时时间。单位是毫秒(默认:无超时限制)。|
| stringifyObjects   | 把值转换为 Stringify 对象。(默认：false)。|
| insecureAuth       | 允许使用老（不安全）的身份认证方式去连接MySQL数据库.(默认： false)。|
| typeCast           | 是否把结果值转换为原生的 javascript 类型(默认: true)。|
| queryFormat        | 一个可以自己定义查询格式函数(具体见Custom format)。|
| supportBigNumbers  | 当在数据库中处理一个大数(BIGINT和DECIMAL)数据类型的时候，你需要启用这个选项(默认: false)。       |
| dateStrings        | 强制为date类型(TIMESTAMP，DATETIME，DATE)转化为一个字符串返回而不是转换成 javascript的date类型对象。(默认: false)|
| debug              | 答应协议详细信息到标准输出(默认: false)。|
| trace              | 产生栈跟踪当在库的入口点出现error时,当调用数次很多时性能会略微下降(默认: true)。|
| multipleStatements | 允许每个mysql语句有多条查询.使用它时要非常注意，因为它很容易引起sql注入攻击(默认:false)。 |
| flags              | 使用连接标示符号标示出超过默认的值的连接。它也可以加到默认的黑名单连接中。更多信息参考（Connection Flags）。|
| ssl                | 使用ssl参数对象(格式参考crypto.createCredentials参数)或者是用一个包含ssl配置的字符串名。目前仅仅支持亚马逊的ca证书。|
| bigNumberStrings   | 这个选项需要bigNumberStrings与 supportBigNumbers同时启用，强制把数据库中大数(BIGINT和DECIMAL)数据类型的值转换为javascript字符对象串对象返回。(默认:false)。当启用supportBigNumbers选项，但 bigNumberStrings是未启用的状态。当无法用javascript数字对象(JavaScript Number objects)所表达的时候就会返回的是一个big number字符串对象(值的范围要在 [-253, +253]之间).否则将会返回一个Number类型的对象。如果supportBigNumbers是false那么这个选项会被自动忽略。|

### 连接数据库：
1、 方法一

``` javascript
var connection = mysql.createConnection({
    host: 'localhost',
    user : 'me',
    password : 'secret',
    database : 'my_db'
});
connection.connect();
```

2、 方法二

``` javascript
var pool  = mysql.createPool({
    connectionLimit : 10,
    host: 'localhost',
    user: 'me',
    password: 'secret',
    database: 'my_db'
});
```
### 查询数据库
#### 方法一
``` javascript
var mysql = require('mysql');

//创建连接池
var pool = mysql.createPool({
    connectionLimit : 10,
    host:"localhost",
    user : 'me',
    password : 'secret',
    database : 'my_db',
    port:"3306"
});
//创建连接
pool.getConnection(function(err,connection){
    connection.query('SELECT * FROM users',function(err, results, fields) {
        // 释放连接
        connection.release();

        if (err) throw err;
        console.log(results);
    })
});

// 终止连接池连接
pool.end(function (err) {
  // all connections in the pool have ended 
});
```

#### 方法二（推荐）
``` javascript
var mysql = require('mysql');
var connection = mysql.createConnection({
    host: 'localhost',
    user : 'me',
    password : 'secret',
    database : 'my_db'
});

connection.connect(function(err) {
    if (err) {
        console.error('error connecting: ' + err.stack);
        return;
    }
    // 取得MySQL的连接ID（“线程ID”）
    console.log('connected as id ' + connection.threadId);
});
connection.query('这里输入sql语句',function(err,results,fields) {
    //执行操作
});
connection.end();
```
query的第二个参数是一个回调函数，三个参数分别代表：

* err：执行操作失败信息
* results：查找的结果
* fields：返回结果的字段信息

有以下三种方式执行sql语句：

1、 connection.query(sqlString, callback)

``` javascript
connection.query('SELECT * FROM `users` WHERE `name` = "Jack"',  function(err, results, fields) {
    // TODO
});
```

2、 connection.query(sqlString, values, callback)

``` javascript
connection.query('SELECT * FROM `users` WHERE `name` = ?', ['Jack'],  function(err, results, fields) {
    // TODO
});
```

3、 connection.query(options, callback)

``` javascript
connection.query({
    sql: 'SELECT * FROM `users` WHERE `name` = ?',
    timeout: 40000, // 40s
    values: ['Jack']
}, function(err, results, fields) {
    // TODO
});
```
#### 方法三
``` javascript
var mysql = require('mysql');
var connection = mysql.createConnection({
    host: 'localhost',
    user : 'me',
    password : 'secret',
    database : 'my_db'
});

connection.connect();

var query = connection.query('这里输入sql操作语句');

query.on('error',function(err) {
    throw err;
});

query.on('fields',function(fields) {
    console.log(fields);
});
query.on('result',function(row) {
    console.log(row);
});

connection.end();
```

### 对query的值进行隐码：
为了避免sql的隐码攻击，可以运行quey之前，对用户数据进行隐藏。

1、 方法一

``` javascript
//略
var key = '_edit_lock';
var queryString = 'SELECT * FROM usersdata WHERE meta_key = ?';

connection.query(queryString,[key],function(err,rows,fields) {
    if(err) throw err;

    console.log(rows);
});
//略
```
2、 方法二

``` javascript
//略
var key = '_edit_lock';
var queryString = 'SELECT * FROM usersdata WHERE meta_key = ' + connection.escape(key);

connection.query(queryString,function(err,rows,fields) {
    if(err) throw err;

    console.log(rows);
});
//略
```

### 关闭连接：
1、 方法一

``` javascript
//当查询完毕,结束连接,这种方式比较推荐,会有回调函数
connection.end(function(err) {
    if(err){
        console.log(err);
    }else {
        console.log('end');
    }
});
```

2、 方法二

``` javascript
    //当查询完毕,结束连接,这种方式会立即断开连接,没有回调函数
    connection.destroy();
```
### 意外关闭连接的处理
``` javascript
connection.on('close',function(err) {
    if(err) {
        //连接意外中断，重新连接
        connection = mysql.createConnection(connection.config();
    } else {
        console.log('conneciton closed normally')
    }
});
```
注意: connection.config 对象保存着最近的链接信息，你可以通过它重新连接到mysql服务器。可以添加到console.log()中输出。

## 四、增查改删
对数据的使用也就是增查改删，所以接下来看看如何实现这几步的。

``` javascript
var mysql  = require('mysql');
var connection = mysql.createConnection({
    host  : 'localhost',
    user : 'me',
    password : 'secret',
    database : 'my_db'
});

connection.connect();

// 增加记录
connection.query('INSERT INTO users(id,name,age) VALUES(0,"faychou",22)',function(err,rows,fields) {
    if(err) throw err;
    console.log(rows);
    console.log(rows.insertId);
});
// 或者
var post = {name:"faychou",age:18};
connection.query('INSERT INTO users SET ?',post,function(err,rows,fields) {
    if(err) throw err;
    console.log(rows);
    console.log(rows.insertId);
});

// 删除记录
connection.query('DELETE FROM users WHERE id=?',[12],function(err,rows,fields) {
    if(err) throw err;
    console.log(rows.affectedRows);
});

// 修改记录
connection.query('UPDATE users SET name=?,age=? WHERE id=?',["fay",18,1],function(err,rows,fields) {
    if(err) throw err;
    console.log(rows.affectedRows);
});

// 查询记录
connection.query("SELECT * FROM users" , function(err, rows, fields) {
    if (err) {
        throw err;
    }
    if (rows) {
        for(var i = 0 ; i < rows.length ; i++) {
            console.log(rows[i].id,rows[i].username,rows[i].password);
        }
    }
});

connection.end();
```