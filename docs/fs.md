# fs 模块

## fs.createReadStream()

`fs.createReadStream`方法读取一个文件，以 stream 的形式返回。

```javascript
const readStream = fs.createReadStream(
  inputFilePath,
  { encoding: 'utf8', highWaterMark: 1024 }
);
```

该方法的第一个参数是文件的路径，第二个参数是一个配置对象。

配置对象的`encoding`属性，决定了`fs.createReadStream`方法的返回值。如果该属性为`null`，返回的是二进制的 buffer；如果为字符串（比如`utf8`），返回的是这种编码的字符串。

配置对象的`highWaterMark`属性指定了每次返回的 buffer 或字符串的最大体积（单位字节）。

stream 以事件的形式获取。

```javascript
readStream.on('data', (chunk) => {
  console.log('>>> '+chunk);
});
readStream.on('end', () => {
  console.log('### DONE ###');
});
```

Node v10 开始，Stream 有异步遍历器（asynchronous iteration）接口（即具有`Symbol.asyncIterator`属性），因此可以使用`for-await-of`读取。

```javascript
async function main(inputFilePath) {
  const readStream = fs.createReadStream(inputFilePath,
    { encoding: 'utf8', highWaterMark: 1024 });

  for await (const chunk of readStream) {
    console.log('>>> '+chunk);
  }
  console.log('### DONE ###');
}
```

## 参考链接

- [Using async iteration natively in Node.js](http://2ality.com/2018/04/async-iter-nodejs.html), by Axel Rauschmayer
