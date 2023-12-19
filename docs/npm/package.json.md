# package.json

## 简介

npm 模块就是一个包含 package.json 文件的目录。

npm 模块分成 CommonJS 和 ES 两种格式。以下情况会以 ES 格式处理模块。

- 脚本后缀名为`.mjs`。
- 脚本后缀名为`.js`，并且 package.json 文件的`type`字段的值为`module`。
- `node`运行时带有`--input-type=module`，这时通过`--eval`或`--print`参数传入的代码字符串会被当作 ES 格式处理。
- 脚本包含一些 ES 才有的语法特征，比如`import`语句、`export`语句、`import.meta`等。

以下情况会以 CommonJS 格式处理模块。

- 脚本后缀名为`.cjs`。
- 脚本后缀名为`.js`，并且 package.json 文件的`type`字段的值为`commonjs`。
- `node`运行时带有`--input-type=commonjs`，这时通过`--eval`或`--print`参数传入的代码字符串会被当作 CommonJS 格式处理。

其他情况下，Node.js 目前会以 CommonJS 格式处理模块，不过以后可能会改成 ES 格式。

## files 字段

`files`字段是一个数组，里面指定了一组文件。当模块发布到 NPM 网站时，这组文件会被包括。这个字段是可选的，如果没有指定内容，那么发布时所有文件都会被包括在内。如果`files`字段包含目录名，该目录里面的所有文件都会被计入。

```javascript
{
  "name": "@adam_baldwin/wombats",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "files": [
   "index.js"
  ],
  ...
}
```

`npm`不会发布`.gitignore`里面列出的文件和目录。项目的根目录或子目录里面，还可以放置一个`.npmignore`文件，该文件会覆盖`.gitignore`，里面指定的文件和目录不会被发布。

项目的根目录下，`files`字段优先级最高；子目录下，`.npmignore`优先。`files`字段指定的文件，不会被`.npmignore`或`.gitignore`排除。

以下文件，发布的时候总是会包含。

- package.json
- README
- CHANGES / CHANGELOG / HISTORY
- LICENSE / LICENCE
- NOTICE
- `main`字段里面的文件

`README`、`CHANGES`、`LICENSE`和`NOTICE`这四个文件名，可以采取任意的大小写组合。

以下文件，发布的时候总是会被排除。

- .git
- CVS
- .svn
- .hg
- .lock-wscript
- .wafpickle-N
- `.*.swp`
- .DS_Store
- `._*`
- npm-debug.log
- .npmrc
- node_modules
- config.gypi
- `*.orig`
- package-lock.json

基本上，npm 会发布`files`字段指定的文件和目录，以及那些总是会包含在内的文件（比如`package.json`），然后再除去那些被其他规则排除的文件和目录。

[npm-packlist](https://github.com/npm/npm-packlist) 模块会列出所有将要打包发布的文件和模块。`npm pack`命令则会将那些将要发布的内容打成一个`tgz`压缩包，放在项目的根目录下。

## 字段

### main

`main`字段指定模块的入口文件。比如，当前模块叫做`foo`，那么`require('foo')`时，加载哪个文件，由该字段指定。

`main`字段的值应该是一个相对路径，相对于模块的根目录进行计算。

如果没有指定`main`字段，那么默认的入口文件是模块根目录的`index.js`。

### exports

`exports`字段也是指定模块的入口文件，它比`main`字段更强大。一般来说，优先使用`exports`字段，只有对 Node.js 10 和更低的版本，才推荐使用`main`字段。如果同时定义了`exports`和`main`两个字段，前者的优先级更高。

另外，`exports`字段只针对模块的消费者，并不在模块内部使用。

`exports`字段可以指定模块的唯一入口。

```javascript
{
  "exports": "./index.js"
}
```

上面字段中，`exports`指定了唯一入口，这意味着只能从模块名加载（`require('pkg')`），而不能从子路径加载，比如`require('pkg/subpath.js')`会报错。

为了保证模块的兼容性，常常同时定义`main`和`exports`。

```javascript
{
  "main": "./index.js",
  "exports": "./index.js"
}
```

如果当前模块有多个入口，可以在`exports`字段里面一一指定。

```javascript
{
  "exports": {
    ".": "./index.js",
    "./submodule.js": "./src/submodule.js"
  }
}
```

上面示例中，模块有两个入口，`.`表示通过模块名加载，`./submodule.js`表示通过模块的子路径加载，写成`import submodule from 'es-module-package/submodule.js';`。

这时，`exports`字段的值是一个对象。前面，`exports`的值为字符串，其实是简写形式。

```javascript
{
  "exports": "./index.js"
}
// 等同于
{
  "exports": {
    ".": "./index.js"
  }
}
```

一般来说，对于同一个入口，模块作者最好同时指定有`.js`后缀名和没有`.js`后缀名两种情况。

```javascript
{
  "name": "my-package",
  "exports": {
    ".": "./lib/index.js",
    "./lib": "./lib/index.js",
    "./lib/index": "./lib/index.js",
    "./lib/index.js": "./lib/index.js",
    "./feature": "./feature/index.js",
    "./feature/index": "./feature/index.js",
    "./feature/index.js": "./feature/index.js",
    "./package.json": "./package.json"
  }
}
```

上面示例中，就同时指定了`./lib/index`和`./lib/index.js`两种入口形式。

`exports`字段也可以使用通配符。

```javascript
{
  "name": "my-package",
  "exports": {
    ".": "./lib/index.js",
    "./lib": "./lib/index.js",
    "./lib/*": "./lib/*.js",
    "./lib/*.js": "./lib/*.js",
    "./feature": "./feature/index.js",
    "./feature/*": "./feature/*.js",
    "./feature/*.js": "./feature/*.js",
    "./package.json": "./package.json"
  }
} 
```

上面示例中，`exports`字段里面使用了通配符，指定了`lib`目录和`feature`目录下的所有入口对应的脚本文件。注意，入口包括`.js`后缀名和没有后缀名两种情况。另外，通配符`*`包括了多层目录的情况。

由于`exports`允许逐一指定不同入口对应的脚本文件，如果以后修改了脚本名称，可以只修改`exports`字段，不改变入口。另外，`exports`也排除某些内部脚本被加载。

```javascript
{
  "name": "my-package",
  "exports": {
    ".": "./lib/index.js",
    "./feature/*.js": "./feature/*.js",
    "./feature/internal/*": null
  }
}
```

上面示例中，`exports`字段共有三个映射，其中第二个映射是指定加载`featrue`子目录下的脚本，而第三个映射则是排除`feature`目录的子目录`internal/*`下的脚本。

`exports`还支持条件加载，即不同的情况加载不同的脚本。

```javascript
{
  "exports": {
    "import": "./index-module.js",
    "require": "./index-require.cjs"
  },
  "type": "module"
}
```

上面示例中，`import`指定 ES 格式加载时，入口文件为`./index-module.js`，CommonJS 格式加载时（`require`命令），入口文件为`./index-require.cjs`。

`exports`支持以下的条件关键词。

- node-addons：Node.js 的扩展
- node：Node.js 环境
- import：ES 格式加载时，特指使用`import`或`import()`加载时。
- require：CommonJS 格式加载时。
- default：其他条件都不匹配时的默认入口文件，总是放在最后。

下面是一个例子。

```javascript
{
  "exports": {
    ".": "./index.js",
    "./feature.js": {
      "node": "./feature-node.js",
      "default": "./feature.js"
    }
  }
}
```

`exports`还支持嵌套条件。

```javascript
{
  "exports": {
    "node": {
      "import": "./feature-node.mjs",
      "require": "./feature-node.cjs"
    },
    "default": "./feature.mjs"
  }
}
```

注意，`exports`的映射不允许指向外部模块。

### imports 

`imports`字段用于模块的内部，为入口文件指定别名。它的路径总是从`#`开始，避免与实际路径相混淆。

```javascript
{
  "imports": {
    "#dep": {
      "node": "dep-node-native",
      "default": "./dep-polyfill.js"
    }
  },
  "dependencies": {
    "dep-node-native": "^1.0.0"
  }
}
```

上面示例中，`imports`字段的意思是，加载`#dep`这个别名时（`import dep from "#def";`），对于  Node.js 加载外部模块`dep-node-native`，对于构建工具则会加载本地文件`./dep-polyfill.js`。

