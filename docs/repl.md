# Node 的 REPL 环境

## 简介

REPL 是 read-eval-print-loop 的缩写，表示命令行下的 Node 引擎的一个互动式对话环境。用户在其中输入命令，就可以立刻看到结果。read 表示读取用户的输入，eval 表示执行，print 表示输出运行的结果，loop 表示重复执行这个过程。

命令行下输入`node`，就可以进入 Node 的 REPL 环境。

```bash
$ node
> 
```

REPL 环境的提示符是一个大于号（`>`）。

退出 REPL，可以在行首按下 Ctrl + d，或者连续两次按下 Ctrl + c。

REPL 环境与 Node 脚本的执行环境基本相似，只有一些很小的差异。比如，REPL 环境不是通过脚本触发的，所以没有`__dirname`和`__filename`这两个内置变量。

REPL 会自动加载 Node 的核心模块，比如 fs、http、os、path等，不必`require`就可以直接使用。 

```bash
$ node 
> fs.read
[Function]
```

上面代码中，REPL 环境可以直接使用`fs.read`方法，不必先加载`fs`模块。

`node`命令的`-e`参数，实际上就是在 REPL 环境运行代码。

```bash
$ node -e "console.log(os.platform())"
darwin
```

## `_`变量

REPL 环境下，有一个内置变量`_`，上一个表达式的值就存放在这个变量之中。

```bash
> require('./example');
{ some: 'some text' }
> _.some
'some text'
```

上面代码中，`require()`方法加载了一个脚本，这行表达式的值就自动存放在`_`里面。

REPL 允许用户对`_`变量赋值。

```bash
> _ = 'something'
'something'
> _
'something'
```

上面代码中，我们将`something`赋值给`_`，这时`_`就是一个普通变量了。

## .editor 命令

REPL 环境下，按下回车键，就会提交并执行当前的输入。这对输入多行的代码非常不方便，有两个办法可以输入多行代码。一个是按 Shift + 回车键，另一个是使用`.editor`命令，键入编辑模式。

```javascript
> .editor
function sum(a, b) {
  return a + b;
}

sum(5, 4);
```

输入`.editor`命令后，就可以输入多行文本。输入完成后，按下 Ctrl + d 就会执行这些代码；按下 Ctrl + c 就会取消本次输入，回到输入`.editor`之前的状态。

## 特殊命令

除了`.editor`命令以外，REPL 还提供其他一些命令。

- .break：按下 Shift + 回车进入多行文本输入的过程中，输入`.break`命令会取消本次输入，相当于按下 Ctrl + c。
- .clear：重置 REPL 上下文为空，并清除当前输入的多行文本。
- .exit：关闭当前的 I/O 读写，退出 REPL 环境。
- .help：显示 REPL 环境的特殊命令列表。
- .save：将当前的 REPL 对换保存成一个文件，比如`.save ./file/to/save.js`。
- .load：加载一个文件进入当前的 REPL 对话，比如`.load ./file/to/load.js`。

## repl 模块

Node 提供`repl`模块，可以在脚本中唤起 REPL 环境。

```javascript
// repl.js
const repl = require('repl');

// Our own prompt
repl.start('Code:: ');
```

上面代码中，`repl.start()`方法用于唤起 REPL 环境，该方法的参数是自定义的 REPL 环境提示符。执行上面的脚本，就会出现这个提示符。

```bash
$ node repl.js
Code::
```

这个模块允许开发者向 REPL 环境注入变量，因此可以做很多事情。

```javascript
// repl.js
const repl = require('repl');
const r = repl.start('Code:: ');
r.context.sum = (...args) => args.reduce((a, b) => a + b, 0);
```

上面代码向 REPL 环境输入了一个`sum`函数。

```bash
$ node repl.js
Code:: sum(1, 2, 3)
6
Code:: .exit
```

## 参考链接

- [Node.js REPL in Depth](https://blog.bloomca.me/2018/07/09/nodejs-repl-in-depth.html), by Seva Zaikov
