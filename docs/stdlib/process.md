# process 对象

`process`对象是 Node 原生提供的对象，表示当前运行的 Node 进程。它不用引入模块，可以直接使用。

## process.argv

`process.argv`是一个数组，表示启动脚本时的命令行参数。

它的前两项是固定的。

- 第一项是 Node 可执行文件的路径
- 第二项是 JavaScript 脚本的路径

后面的数组成员都是命令行参数。

```bash
$ node index.js --watch
```

上面这个命令执行后，在`index.js`脚本里面，`process.argv`数组共有三项。

- `process.argv[0]`：/path/to/node
- `process.argv[1]`：/path/to/index.js
- `process.argv[2]`：--watch

如果只需要命令行参数，可以用解构赋值获取。

```javascript
const [ , , ...args ] = process.argv;
console.log(args[0]);   // "--watch"
```

上面代码，`args`数组就是通过解构赋值，拿到的所有命令行参数。

## 参考链接

- [Extracting command line arguments from Node.js using destructuring](https://humanwhocodes.com/blog/2018/10/extracting-command-line-arguments-nodejs/), Nicholas C. Zakas
