---
title: Node.js 教程
date: 2024-10-02 10:06:08
tags: 前端
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Node.js%20%E6%95%99%E7%A8%8B/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Node.js%20%E6%95%99%E7%A8%8B/preview.jpg
---



# Node.js 教程

## 简介



JS 是脚本语言，脚本语言都需要一个解析器才能运行。对于写在 HTML 页面里的 JS，浏览器充当了解析器的角色。而对于需要独立运行的 JS，NodeJS 就是一个解析器

每一种解析器都是一个运行环境，不但允许 JS 定义各种数据结构，进行各种计算，还允许 JS 使用运行环境提供的内置对象和方法做一些事情。例如运行在浏览器中的 JS 的用途是操作 DOM，浏览器就提供了 `document` 之类的内置对象。而运行在 NodeJS 中的 JS 的用途是操作磁盘文件或搭建 HTTP 服务器，NodeJS 就相应提供了 `fs`、`http` 等内置对象



------

## 模块



编写稍大一点的程序时一般都会将代码模块化。在 NodeJS 中，一般将代码合理拆分到不同的 JS 文件中，每一个文件就是一个模块，而文件路径就是模块名

在编写每个模块时，都有 `require`、`exports`、`module `三个预先定义好的变量可供使用



------

### require



`require` 函数用于在当前模块中加载和使用别的模块，传入一个模块名，返回一个模块导出对象。模块名可使用相对路径（以`./`开头），或者是绝对路径（以`/`或`C:`之类的盘符开头）。另外，模块名中的 `.js` 扩展名可以省略。以下是一个例子

```js
var foo1 = require('./foo');
var foo2 = require('./foo.js');
var foo3 = require('/home/user/foo');
var foo4 = require('/home/user/foo.js');

// foo1至foo4中保存的是同一个模块的导出对象
```



另外，可以使用以下方式加载和使用一个 JSON 文件

```js
var data = require('./data.json');
```



------

### exports



`exports` 对象是当前模块的导出对象，用于导出模块公有方法和属性。别的模块通过`require`函数使用当前模块时得到的就是当前模块的`exports`对象。以下例子中导出了一个公有方法

```js
exports.hello = function () {
    console.log('Hello World!');
};
```



------

### module



通过`module`对象可以访问到当前模块的一些相关信息，但最多的用途是替换当前模块的导出对象。例如模块导出对象默认是一个普通对象，如果想改成一个函数的话，可以使用以下方式

```js
module.exports = function () {
    console.log('Hello World!');
};
```

以上代码中，模块默认导出对象被替换为一个函数



一个模块中的 JS 代码仅在模块第一次被使用时执行一次，并在执行过程中初始化模块的导出对象。之后，缓存起来的导出对象被重复利用



------

### 主模块



通过命令行参数传递给NodeJS以启动程序的模块被称为主模块。主模块负责调度组成整个程序的其它模块完成工作。例如通过以下命令启动程序时，`main.js`就是主模块

```sh
node main.js
```



例如有以下目录

```
- /home/user/hello/
    - util/
        counter.js
    main.js
```

其中`counter.js`内容如下：

```js
var i = 0;

function count() {
    return ++i;
}

exports.count = count;
```

主模块`main.js`内容如下：

```js
var counter1 = require('./util/counter');
var counter2 = require('./util/counter');

console.log(counter1.count());
console.log(counter2.count());
console.log(counter2.count());
```

运行该程序的结果如下

```
1
2
3
```

可以看到，`counter.js`并没有因为被require了两次而初始化两次



------

## 代码组织与部署

### 模块路径解析规则



我们已经知道，`require`函数支持斜杠（`/`）或盘符（`C:`）开头的绝对路径，也支持`./`开头的相对路径。但这两种路径在模块之间建立了强耦合关系，一旦某个模块文件的存放位置需要变更，使用该模块的其它模块的代码也需要跟着调整，变得牵一发动全身。因此，`require`函数支持第三种形式的路径，写法类似于`foo/bar`，并依次按照以下规则解析路径，直到找到模块位置

1. 内置模块

   如果传递给`require`函数的是 NodeJS 内置模块名称，不做路径解析，直接返回内部模块的导出对象，例如`require('fs')`

2. node_modules 目录

   NodeJS 定义了一个特殊的`node_modules`目录用于存放模块。例如某个模块的绝对路径是`/home/user/hello.js`，在该模块中使用`require('foo/bar')`方式加载模块时，则 NodeJS 依次尝试使用以下路径

   ```
    /home/user/node_modules/foo/bar
    /home/node_modules/foo/bar
    /node_modules/foo/bar
   ```

3. NODE_PATH 环境变量

   与 PATH 环境变量类似，NodeJS 允许通过 NODE_PATH 环境变量来指定额外的模块搜索路径。NODE_PATH 环境变量中包含一到多个目录路径，路径之间在 Linux 下使用`:`分隔，在 Windows 下使用`;`分隔。例如定义了以下 NODE_PATH 环境变量：

   ```
    NODE_PATH=/home/user/lib:/home/lib
   ```

   当使用`require('foo/bar')`的方式加载模块时，则 NodeJS 依次尝试以下路径

   ```
    /home/user/lib/foo/bar
    /home/lib/foo/bar
   ```



------

### 包（package）



我们已经知道了 JS 模块的基本单位是单个 JS 文件，但复杂些的模块往往由多个子模块组成。为了便于管理和使用，我们可以把由多个子模块组成的大模块称做`包`，并把所有子模块放在同一个目录里

在组成一个包的所有子模块中，需要有一个入口模块，入口模块的导出对象被作为包的导出对象。例如有以下目录结构

```
- /home/user/lib/
    - cat/
        head.js
        body.js
        main.js
```

其中`cat`目录定义了一个包，其中包含了3个子模块。`main.js`作为入口模块，其内容如下：

```
var head = require('./head');
var body = require('./body');

exports.create = function (name) {
    return {
        name: name,
        head: head.create(),
        body: body.create()
    };
};
```

在其它模块里使用包的时候，需要加载包的入口模块。接着上例，使用`require('/home/user/lib/cat/main')`能达到目的，但是入口模块名称出现在路径里看上去不是个好主意。因此我们需要做点额外的工作，让包使用起来更像是单个模块



index.js

当模块的文件名是`index.js`，加载模块时可以使用模块所在目录的路径代替模块文件路径，因此接着上例，以下两条语句等价

```
var cat = require('/home/user/lib/cat');
var cat = require('/home/user/lib/cat/index');
```

这样处理后，就只需要把包目录路径传递给`require`函数，感觉上整个目录被当作单个模块使用，更有整体感



package.json

如果想自定义入口模块的文件名和存放位置，就需要在包目录下包含一个`package.json`文件，并在其中指定入口模块的路径。上例中的`cat`模块可以重构如下。

```
- /home/user/lib/
    - cat/
        + doc/
        - lib/
            head.js
            body.js
            main.js
        + tests/
        package.json
```

其中`package.json`内容如下。

```
{
    "name": "cat",
    "main": "./lib/main.js"
}
```

如此一来，就同样可以使用`require('/home/user/lib/cat')`的方式加载模块。NodeJS 会根据包目录下的`package.json`找到入口模块所在位置



------

## NPM

### 简介



NPM 是随同 NodeJS 一起安装的包管理工具，能解决 NodeJS 代码部署上的很多问题，常见的使用场景有以下几种：

- 允许用户从 NPM 服务器下载别人编写的三方包到本地使用。
- 允许用户从 NPM 服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到 NPM 服务器供别人使用。

可以看到，NPM建立了一个 NodeJS 生态圈，NodeJS 开发者和用户可以在里边互通有无



------

### 使用场景



* 下载第三方包

  如果使用到的三方包比较多，在终端下一个包一条命令地安装未免太人肉了。因此 NPM 对`package.json`的字段做了扩展，允许在其中申明三方包依赖，比如如下：

  ```
  {
      "name": "node-echo",
      "main": "./lib/echo.js",
      "dependencies": {
          "argv": "0.0.2"
      }
  }
  ```

  这样处理后，在工程目录下就可以使用`npm install`命令批量安装三方包了。更重要的是，当以后`node-echo`也上传到了NPM服务器，别人下载这个包时， NPM 会根据包中申明的三方包依赖自动下载进一步依赖的三方包

  如此一来，用户只需关心自己直接使用的三方包，不需要自己去解决所有包的依赖关系

* 安装命令行程序

* 发布代码



------

### 版本号



使用 NPM 下载和发布代码时都会接触到版本号。NPM 使用语义版本号来管理代码

语义版本号分为`X.Y.Z`三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新

```
+ 如果只是修复bug，需要更新Z位。

+ 如果是新增了功能，但是向下兼容，需要更新Y位。

+ 如果有大变动，向下不兼容，需要更新X位。
```

版本号有了这个保证后，在申明三方包依赖时，除了可依赖于一个固定版本号外，还可依赖于某个范围的版本号。例如`"argv": "0.0.x"`表示依赖于`0.0.x`系列的最新版`argv`



------

## 文件操作

### Buffer



>    **官方文档：**  http://nodejs.org/api/buffer.html

让前端觉得如获神器的不是 NodeJS 能做网络编程，而是 NodeJS 能够操作文件。小至文件查找，大至代码编译，几乎没有一个前端工具不操作文件。换个角度讲，几乎也只需要一些数据处理逻辑，再加上一些文件操作，就能够编写出大多数前端工具



JS语言自身只有字符串数据类型，没有二进制数据类型，因此 NodeJS 提供了一个与`String`对等的全局构造函数`Buffer`来提供对二进制数据的操作。除了可以读取文件得到`Buffer`的实例外，还能够直接构造，例如：

```js
var bin = new Buffer([ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]);
```

`Buffer`与字符串类似，除了可以用`.length`属性得到字节长度外，还可以用`[index]`方式读取指定位置的字节，例如：

```js
bin[0]; // => 0x68;
```

`Buffer`与字符串能够互相转化，例如可以使用指定编码将二进制数据转化为字符串：

```js
var str = bin.toString('utf-8'); // => "hello"
```

或者反过来，将字符串转换为指定编码下的二进制数据：

```js
var bin = new Buffer('hello', 'utf-8'); // => <Buffer 68 65 6c 6c 6f>
```



`Buffer`与字符串有一个重要区别。字符串是只读的，并且对字符串的任何修改得到的都是一个新字符串，原字符串保持不变。至于`Buffer`，更像是可以做指针操作的 C 语言数组。例如，可以用`[index]`方式直接修改某个位置的字节

```js
bin[0] = 0x48;
```

而`.slice`方法也不是返回一个新的`Buffer`，而更像是返回了指向原`Buffer`中间的某个位置的指针，因此对`.slice`方法返回的`Buffer`的修改会作用于原`Buffer`。也因此，如果想要拷贝一份`Buffer`，得首先创建一个新的`Buffer`，并通过`.copy`方法把原`Buffer`中的数据复制过去。这个类似于申请一块新的内存，并把已有内存中的数据复制过去

```js
var bin = new Buffer([ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]);
var dup = new Buffer(bin.length);

bin.copy(dup);
dup[0] = 0x48;
console.log(bin); // => <Buffer 68 65 6c 6c 6f>
console.log(dup); // => <Buffer 48 65 65 6c 6f>
```

总之，`Buffer`将 JS 的数据处理能力从字符串扩展到了任意二进制数据



------

### Stream



>    **官方文档：**  http://nodejs.org/api/stream.html

当内存中无法一次装下需要处理的数据时，或者一边读取一边处理更加高效时，我们就需要用到数据流。NodeJS 中通过各种`Stream`来提供对数据流的操作

以大文件拷贝程序为例，我们可以为数据来源创建一个只读数据流，示例如下：

```js
var rs = fs.createReadStream(pathname);

rs.on('data', function (chunk) {
    doSomething(chunk);
});

rs.on('end', function () {
    cleanUp();
});
```

上边的代码中`data`事件会源源不断地被触发，不管`doSomething`函数是否处理得过来。代码可以继续做如下改造，以解决这个问题

```js
var rs = fs.createReadStream(src);

rs.on('data', function (chunk) {
    rs.pause();
    doSomething(chunk, function () {
        rs.resume();
    });
});

rs.on('end', function () {
    cleanUp();
});
```

以上代码给`doSomething`函数加上了回调，因此我们可以在处理数据前暂停数据读取，并在处理数据后继续读取数据

此外，我们也可以为数据目标创建一个只写数据流，示例如下：

```js
var rs = fs.createReadStream(src);
var ws = fs.createWriteStream(dst);

rs.on('data', function (chunk) {
    ws.write(chunk);
});

rs.on('end', function () {
    ws.end();
});
```

我们把`doSomething`换成了往只写数据流里写入数据后，以上代码看起来就像是一个文件拷贝程序了。但是以上代码存在上边提到的问题，如果写入速度跟不上读取速度的话，只写数据流内部的缓存会爆仓。我们可以根据`.write`方法的返回值来判断传入的数据是写入目标了，还是临时放在了缓存了，并根据`drain`事件来判断什么时候只写数据流已经将缓存中的数据写入目标，可以传入下一个待写数据了。因此代码可以改造如下：

```js
var rs = fs.createReadStream(src);
var ws = fs.createWriteStream(dst);

rs.on('data', function (chunk) {
    if (ws.write(chunk) === false) {
        rs.pause();
    }
});

rs.on('end', function () {
    ws.end();
});

ws.on('drain', function () {
    rs.resume();
});
```

以上代码实现了数据从只读数据流到只写数据流的搬运，并包括了防爆仓控制。因为这种使用场景很多，例如大文件拷贝程序，NodeJS 直接提供了`.pipe`方法来做这件事情，其内部实现方式与上边的代码类似



------

### File System



>    **官方文档：**  http://nodejs.org/api/fs.html

NodeJS 通过`fs`内置模块提供对文件的操作。`fs`模块提供的 API 基本上可以分为以下三类：

* 文件属性读写

  其中常用的有`fs.stat`、`fs.chmod`、`fs.chown`等等

* 文件内容读写

  其中常用的有`fs.readFile`、`fs.readdir`、`fs.writeFile`、`fs.mkdir`等等

* 底层文件操作

  其中常用的有`fs.open`、`fs.read`、`fs.write`、`fs.close`等等



NodeJS 最精华的异步 IO 模型在`fs`模块里有着充分的体现，例如上边提到的这些 API 都通过回调函数传递结果。以`fs.readFile`为例：

```js
fs.readFile(pathname, function (err, data) {
    if (err) {
        // Deal with error.
    } else {
        // Deal with data.
    }
});
```

如上边代码所示，基本上所有`fs`模块 API 的回调参数都有两个。第一个参数在有错误发生时等于异常对象，第二个参数始终用于返回 API 方法执行结果

此外，`fs`模块的所有异步 API 都有对应的同步版本，用于无法使用异步操作时，或者同步操作更方便时的情况。同步 API 除了方法名的末尾多了一个`Sync`之外，异常对象与执行结果的传递方式也有相应变化。同样以`fs.readFileSync`为例：

```js
try {
    var data = fs.readFileSync(pathname);
    // Deal with data.
} catch (err) {
    // Deal with error.
}
```

`fs`模块提供的 API 很多，这里不一一介绍，需要时请自行查阅官方文档



------

### Path



>    **官方文档：**  http://nodejs.org/api/path.html

操作文件时难免不与文件路径打交道。NodeJS 提供了`path`内置模块来简化路径相关操作，并提升代码可读性。以下分别介绍几个常用的 API

* path.normalize

  将传入的路径转换为标准路径，具体讲的话，除了解析路径中的`.`与`..`外，还能去掉多余的斜杠。如果有程序需要使用路径作为某些数据的索引，但又允许用户随意输入路径时，就需要使用该方法保证路径的唯一性。以下是一个例子：

  ```js
  var cache = {};
  
  function store(key, value) {
    cache[path.normalize(key)] = value;
  }
  
  store('foo/bar', 1);
  store('foo//baz//../bar', 2);
  console.log(cache);  // => { "foo/bar": 2 }
  ```

* path.join

  将传入的多个路径拼接为标准路径。该方法可避免手工拼接路径字符串的繁琐，并且能在不同系统下正确使用相应的路径分隔符。以下是一个例子：

  ```js
  path.join('foo/', 'baz/', '../bar'); // => "foo/bar"
  ```

* path.extname

  当我们需要根据不同文件扩展名做不同操作时，该方法就显得很好用。以下是一个例子：

  ```js
  path.extname('foo/bar.js'); // => ".js"
  ```

`path`模块提供的其余方法也不多，稍微看一下官方文档就能全部掌握



------

## 网络操作

### HTTP



>    **官方文档：**  http://nodejs.org/api/http.html

NodeJS 本来的用途是编写高性能 Web 服务器。我们首先在这里重复一下官方文档里的例子，使用 NodeJS 内置的`http`模块简单实现一个 HTTP 服务器

```js
var http = require('http');

http.createServer(function (request, response) {
    response.writeHead(200, { 'Content-Type': 'text-plain' });
    response.end('Hello World\n');
}).listen(8124);
```

以上程序创建了一个 HTTP 服务器并监听`8124`端口，打开浏览器访问该端口`http://127.0.0.1:8124/`就能够看到效果



'http'模块提供两种使用方式：

* 作为服务端使用时，创建一个 HTTP 服务器，监听 HTTP 客户端请求并返回响应
* 作为客户端使用时，发起一个HTTP客户端请求，获取服务端响应



如上面例子所示，首先需要使用`.createServer`方法创建一个服务器，然后调用`.listen`方法监听端口。之后，每当来了一个客户端请求，创建服务器时传入的回调函数就被调用一次。可以看出，这是一种事件机制



------

### HTTPS



>    **官方文档：**  http://nodejs.org/api/https.html

`https`模块与`http`模块极为类似，区别在于`https`模块需要额外处理 SSL 证书

在服务端模式下，创建一个 HTTPS 服务器的示例如下

```js
var options = {
        key: fs.readFileSync('./ssl/default.key'),
        cert: fs.readFileSync('./ssl/default.cer')
    };

var server = https.createServer(options, function (request, response) {
        // ...
    });
```

可以看到，与创建 HTTP 服务器相比，多了一个`options`对象，通过`key`和`cert`字段指定了HTTPS服务器使用的私钥和公钥

另外，NodeJS 支持 SNI 技术，可以根据 HTTPS 客户端请求使用的域名动态使用不同的证书，因此同一个 HTTPS 服务器可以使用多个域名提供服务。接着上例，可以使用以下方法为 HTTPS 服务器添加多组证书

```js
server.addContext('foo.com', {
    key: fs.readFileSync('./ssl/foo.com.key'),
    cert: fs.readFileSync('./ssl/foo.com.cer')
});

server.addContext('bar.com', {
    key: fs.readFileSync('./ssl/bar.com.key'),
    cert: fs.readFileSync('./ssl/bar.com.cer')
});
```



在客户端模式下，发起一个 HTTPS 客户端请求与`http`模块几乎相同，示例如下

```js
var options = {
        hostname: 'www.example.com',
        port: 443,
        path: '/',
        method: 'GET'
    };

var request = https.request(options, function (response) {});

request.end();
```

但如果目标服务器使用的 SSL 证书是自制的，不是从颁发机构购买的，默认情况下`https`模块会拒绝连接，提示说有证书安全问题。在`options`里加入`rejectUnauthorized: false`字段可以禁用对证书有效性的检查，从而允许`https`模块请求开发环境下使用自制证书的 HTTPS 服务器



------

### URL



>    **官方文档：**  http://nodejs.org/api/url.html

处理 HTTP 请求时`url`模块使用率超高，因为该模块允许解析 URL、生成 URL，以及拼接 URL

我们可以使用`.parse`方法来将一个 URL 字符串转换为 URL 对象，示例如下

```js
url.parse('http://user:pass@host.com:8080/p/a/t/h?query=string#hash');
```

传给`.parse`方法的不一定要是一个完整的 URL，例如在 HTTP 服务器回调函数中，`request.url`不包含协议头和域名，但同样可以用`.parse`方法解析

```js
http.createServer(function (request, response) {
    var tmp = request.url; // => "/foo/bar?a=b"
    url.parse(tmp);
}).listen(80);
```

`.parse`方法还支持第二个和第三个布尔类型可选参数。第二个参数等于`true`时，该方法返回的 URL 对象中，`query`字段不再是一个字符串，而是一个经过`querystring`模块转换后的参数对象。第三个参数等于`true`时，该方法可以正确解析不带协议头的 URL，例如`//www.example.com/foo/bar`



反过来，`format`方法允许将一个 URL 对象转换为 URL 字符串，示例如下

```js
url.format({
    protocol: 'http:',
    host: 'www.example.com',
    pathname: '/p/a/t/h',
    search: 'query=string'
});
/* =>
'http://www.example.com/p/a/t/h?query=string'
*/
```

另外，`.resolve`方法可以用于拼接 URL，示例如下

```js
url.resolve('http://www.example.com/foo/bar', '../baz');
```



------

## Express 框架

### 简介



express 是一个基于 Noed.js 平台的极简、灵活的 WEB 应用开发框架，官方网址：https://www.expressjs.com.cn/ 简单来说，express 是一个封装好的工具包，封装了很多功能，便于我们开发 WEB 应用（HTTP 服务）



------

### 安装



首先假定你已经安装了 [Node.js](https://nodejs.org/)，接下来为你的应用创建一个目录，然后进入此目录并将其作为当前工作目录。

```console
$ mkdir myapp
$ cd myapp
```

通过 `npm init` 命令为你的应用创建一个 `package.json` 文件

```console
$ npm init
```

此命令将要求你输入几个参数，例如此应用的名称和版本。 你可以直接按“回车”键接受大部分默认设置即可，下面这个除外：

```
entry point: (index.js)
```

键入 `app.js` 或者你所希望的名称，这是当前应用的入口文件。如果你希望采用默认的 `index.js` 文件名，只需按“回车”键即可。

接下来在 `myapp` 目录下安装 Express 并将其保存到依赖列表中。如下：

```console
$ npm install express --save
```

如果只是临时安装 Express，不想将它添加到依赖列表中，可执行如下命令：

```console
$ npm install express --no-save
```



------

### Hello World



```js
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```



该程序会启动一个服务器，并通过 3000 端口监听连接。如果请求指向根 URL (/) 或路由，该程序会以 "Hello World! 对于其他路径，它将以 404 Not Found 回应。

上面的示例实际上是一个正在运行的服务器： 继续点击显示的 URL。你会收到响应，页面上会显示实时日志，你所做的任何更改都会实时反映出来。这是由 RunKit 提供的，它提供了一个连接到完整 Node 环境的交互式 JavaScript 游戏场地，可在网络浏览器中运行



更多内容可查看官方 API 文档：https://www.expressjs.com.cn/starter/examples.html
