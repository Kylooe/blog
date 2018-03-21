- link和@import

  都是用于从外部引用CSS。

  `@import`只可以加载CSS，并且需要页面完全载入后才可加载；而使用`link`可以在页面载入的同时并行加载CSS。

  `@import`是CSS 2.1特性，对于过低版本的浏览器有兼容性问题；`link`是元素，可以通过JavaScript控制。

- Node

  - File System

    node对于前端而言很大的一个帮助在于可以对文件进行操作，通过内置的`fs`模块可以做到读写文件的属性或内容、操作底层文件。

    所有操作文件的API都通过callback传递结果，例如`fs.readFile(src, function(err, data) { … })`，基本上所有API的callback都有两个参数，第一是异常对象，第二个是要正常处理的数据。因此这些API都是异步的，但所有API都有对应的**同步**方法，通常是在API名的末尾多了一个**Sync**，如readFile对应的则是`fs.readFileSync`，其返回的结果自然就是data了。

  - Path

    操作文件则需要涉及文件路径，标准化路径在Windows系统下是`\`，而在Linux系统下是`/`，如果统一为`/`要通过正则替换一下`replace(/\\/g, '/')`。

    要获取文件扩展名，可通过`extname`方法：`path.extname('path/path/some.js')`，输出结果为`".js"`。

  - 遍历

    操作文件经常需要对目录进行遍历，如在指定目录下处理所有css文件或js文件，遍历树状结构时通常使用深度优先+先序遍历，也就是从顶端节点开始，到达某个节点则算遍历过此节点，后续先向下遍历其子节点而非同级的节点。

    使用同步方法进行遍历：

    ```js
    function travelSync(dir, callback) {
        fs.readdirSync(dir).forEach(function(pathNode) {
            var pathName = path.join(dir, pathNode);
            if(fs.statSync(pathName).isDirectory()) travelSync(pathName, callback);
            else callback(pathName)
        })
    }
    ```

    其中`path.join()`方法将传入的多个路径拼接为标准路径，同时在不同系统下使用正确的路径分隔符。

    `fs.statSync()`返回`fs.Stats`实例，`isDirectory()`方法可以判断传入的路径是否为一个fs目录。

  - 编码

    操作文件会涉及到文本编码的问题，Unicode编码方式为`UTF8`的字符串才能被JS正常处理。文本文件的前2～3个字节有时会包含BOM，即文件编码的标记，处理文本文件时需要先去除BOM否则可能导致JS语法错误。例如UTF8的BOM为[EF BB BF]：

    ```js
    function readTextContent(dir) {
        var bin = fs.readFileSync(dir);
        if(bin[0]===0xEF && bin[1]===0xBB && bin[2]===0xBF) bin = bin.slice(3);
        return bin.toString('utf-8');
    }
    ```

    即可实现对UTF8编码文本的识别和BOM去除。

  - HTTP

    Node.js设计的初衷是编写高性能Web服务器，使用内置的http模块即可简单实现一个简易HTTP服务器：

    ```js
    var http = require('http');
    http.createServer(function(request, response) {
        response.writeHead(200, {
            'Content-Type': 'text/plain',
            'x-test': 'test info'
        });
        response.end('Hello World!');
    }).listen(9999);
    ```

    监听9999端口，同时返回了状态码为200、响应头`Content-Type`为text/plain、自定义响应头信息`x-test`为`test info`的响应。访问<u>127.0.0.1:9999</u>可以看到页面上打印了一句`Hello World!`。

    http模块有两种使用方式：

    + 作为服务端使用

      作为服务端时可以创建HTTP服务器，监听HTTP请求并返回响应。通过`.createServer()`创建服务器后，调用`.listen()`监听端口，每当收到客户端请求，处理request和response的callback就被调用一次。

    + 作为客户端使用

    HTTP请求和响应本质都是一个数据流，并且都是由headers和body组成，HTTP请求发送到服务器时可以认为是单字节以Stream发送，可以把request对象当作只读Stream来访问request body，同时也可以使用Stream和Buffer相关的方法操作request和response。

- Webpack require

  webpack的require是无序引入。全局require两个文件，其中一个文件同时也依赖于另一个文件的话，应该在其内部再次require此文件。

- 递归

  计算N阶问题时往往使用递归算法，通过不断缩小问题规模以解决问题。最典型的台阶问题，一次上一阶或二阶，共n阶，求跳法总和。方法实际就是Fibonacci序列，如果n=1则只有1种方法，n=2为两种，n>2时若第一次上1阶则剩下的方法为$f(n-1)$、第一次上2阶则剩下的方法为$f(n-2)$，因此总数为$f(n-1)+f(n-2)$，即有：

  ```js
  function Fibonacci(n) {
      if(n === 1) return 1;
      else if(n === 2) return 2;
      else return Fibonacci(n - 1) + Fibonacci(n - 2);
  }
  ```

  但递归的问题在于每递归一次则产生一次函数调用，因此性能差于循环算法。