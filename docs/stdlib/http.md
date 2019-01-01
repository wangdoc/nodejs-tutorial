# http 模块

`http`模块用于 HTTP 通信。

## http.Server

`http.Server`属性指向一个类，表示 Web 服务器实例。

这个类继承了`net.Server`，而`net.Server`继承了 EventEmitter 接口，因此可以使用`server.on()`方法监听事情。最重要的一个事件是`request`，表示收到 HTTP 请求。

```javascript
server.on('request', (request, response) => {
  response.end('Hello, world!');
});
```

`server.listen()`方法用于启动 Web 服务。这个方法需要指定监听的端口，以及一个回调函数（启动后要做什么）。

```javascript
server.listen(PORT, () => {
  console.log(`starting server at port ${PORT}`);
});
```

## http.createServer()

`http.createServer()`方法用于创建一个 Web 服务器，它的返回值就是一个`http.Server`实例。

```javascript
const { createServer } = require('http');

// 指定端口
const PORT = process.env.PORT || 8080;
const server = createServer();

server.on('request', (request, response) => {
  response.end('Hello, world!');
});

server.listen(PORT, () => {
  console.log(`starting server at port ${PORT}`);
});
```

`request`事件的回调函数，可以作为`createServer()`方法的参数。因此，上面的代码也可以写成下面的样子。

```javascript
const { createServer } = require('http');

const PORT = process.env.PORT || 8080;

const server = createServer((request, response) => {
  response.end('Hello, world!');
}).listen(PORT, () => {
  console.log(`starting server at port ${PORT}`);
});
```

## response 对象

HTTP 请求和回应都是数据流（stream），`request`是只读数据流，`response`是可写数据流。

`response.write()`方法表示依次向 HTTP 回应写入内容。

`response.end()`方法表示写入数据以后，关闭`response`数据流。

```javascript
const { createServer } = require('http');

createServer((request, response) => {
  // 等同于 response.end('Hello, world!')
  response.write('Hello');
  response.write(', ');
  response.write('World!');
  response.end();
}).listen(8080);
```

注意，`response.end()`是必需的，而且必须写在最后，否则 Node 不会关闭请求。

如果要向客户端发送文件，也可以写成数据流。

```javascript
const { createReadStream } = require('fs');
const { createServer } = require('http');

createServer((request, response) => {
  createReadStream(__filename).pipe(response);
}).listen(8080);
```

上面代码会将这个脚本代码，发送给客户端。这时不用写`response.end()`方法，因为`pipe()`方法会在发送结束后，自动关闭数据流。

`response.setHeader()`方法用于设置返回的头信息。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  res.setHeader('content-type', 'application/json');
  res.end(JSON.stringify({ foo: 'bar' }));
}).listen(8080);
```

下面是设置 Cookie 的例子。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  res.setHeader(
    'Set-Cookie',
    ['myCookie=myValue'],
    ['mySecondCookie=mySecondValue']
  );
  res.end(`Your cookies are: ${req.headers.cookie}`);
}).listen(8080);
```

`response.writeHead()`方法与`response.setHeader()`类似，但是优先级更高。它可以设置返回的 HTTP 状态码，第一个参数是状态码，第二个参数是状态说明。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  res.writeHead(204, 'My Custom Message');
  res.end();
}).listen(8080);
```

## request 对象

`request.headers`属性返回一个对象，包含了 HTTP 请求的头信息。该对象的键名是头信息的字段名，键值是头信息的字段值。

```javascript
const { createServer } = require("http");

createServer((request, response) => {
  const languages = request.headers['accept-language'];
  response.end(languages);
}).listen(8080);
```

上面代码返回 HTTP 请求的头信息`accept-language`。

`request.url`属性返回浏览器请求的路径，可以基于这个属性做一个简单的路由。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  switch (req.url) {
    case '/':
      res.end('You are on the main page!');
      break;
    case '/about':
      res.end('You are on about page!');
      break;
    default:
      res.statusCode = 404;
      res.end('Page not found!');
  }
}).listen(8080);
```

获取查询字符串，可以用 Node 内置的`url`模块解析`request.url`属性。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  const { query } = require('url').parse(req.url, true);
  if (query.name) {
    res.end(`You requested parameter name with value ${query.name}`);
  } else {
    res.end('Hello!');
  }
}).listen(8080);
```

`request.method`属性返回浏览器请求的方法。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  if (req.method === 'GET') {
    return res.end('List of data');
  } else if (req.method === 'POST') {
    return res.end('success');
  } else {
    res.statusCode(400);
    return res.end('Unsupported method');
  }
}).listen(8080);
```

POST 请求发送的数据体是一个数据流（stream），可以用`request.on()`方法监听`data`事件，累加数据流的每一部分；监听`end`事件，得到数据流接收结束的通知。

```javascript
const { createServer } = require('http');

createServer((req, res) => {
  if (req.method === 'POST') {
    let data = '';
    req.on('data', chunk => {
      data += chunk;
    });

    req.on('end', () => {
      try {
        const requestData = JSON.parse(data);
        requestData.ourMessage = 'success';
        res.setHeader('Content-Type', 'application/json');
        res.end(JSON.stringify(requestData));
      } catch (e) {
        res.statusCode = 400;
        res.end('Invalid JSON');
      }
    });
  } else {
    res.statusCode = 400;
    res.end('Unsupported method, please POST a JSON object');
  }
}).listen(8080);
```

## 参考链接

- [Node.js Fundamentals: Web Server Without Dependencies](https://blog.bloomca.me/2018/12/22/writing-a-web-server-node.html), Seva Zaikov
