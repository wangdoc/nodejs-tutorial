# 脚本功能

## npm run

`npm`不仅可以用于模块管理，还可以用于执行脚本。`package.json`文件有一个`scripts`字段，可以用于指定脚本命令，供`npm`直接调用。

```javascript
{
  "name": "myproject",
  "devDependencies": {
    "jshint": "latest",
    "browserify": "latest",
    "mocha": "latest"
  },
  "scripts": {
    "lint": "jshint **.js",
    "test": "mocha test/"
  }
}
```

上面代码中，`scripts`字段指定了两项命令`lint`和`test`。命令行输入`npm run-script lint`或者`npm run lint`，就会执行`jshint **.js`，输入`npm run-script test`或者`npm run test`，就会执行`mocha test/`。`npm run`是`npm run-script`的缩写，一般都使用前者，但是后者可以更好地反应这个命令的本质。

`npm run`命令会自动在环境变量`$PATH`添加`node_modules/.bin`目录，所以`scripts`字段里面调用命令时不用加上路径，这就避免了全局安装NPM模块。

`npm run`如果不加任何参数，直接运行，会列出`package.json`里面所有可以执行的脚本命令。

npm内置了两个命令简写，`npm test`等同于执行`npm run test`，`npm start`等同于执行`npm run start`。

`npm run`会创建一个Shell，执行指定的命令，并临时将`node_modules/.bin`加入PATH变量，这意味着本地模块可以直接运行。

举例来说，你执行ESLint的安装命令。

```bash
$ npm i eslint --save-dev
```

运行上面的命令以后，会产生两个结果。首先，ESLint被安装到当前目录的`node_modules`子目录；其次，`node_modules/.bin`目录会生成一个符号链接`node_modules/.bin/eslint`，指向ESLint模块的可执行脚本。

然后，你就可以在`package.json`的`script`属性里面，不带路径的引用`eslint`这个脚本。

```javascript
{
  "name": "Test Project",
  "devDependencies": {
    "eslint": "^1.10.3"
  },
  "scripts": {
    "lint": "eslint ."
  }
}
```

等到运行`npm run lint`的时候，它会自动执行`./node_modules/.bin/eslint .`。

如果直接运行`npm run`不给出任何参数，就会列出`scripts`属性下所有命令。

```bash
$ npm run
Available scripts in the user-service package:
  lint
     jshint **.js
  test
    mocha test/
```

下面是另一个`package.json`文件的例子。

```javascript
"scripts": {
  "watch": "watchify client/main.js -o public/app.js -v",
  "build": "browserify client/main.js -o public/app.js",
  "start": "npm run watch & nodemon server.js",
  "test": "node test/all.js"
},
```

上面代码在`scripts`项，定义了四个别名，每个别名都有对应的脚本命令。

```bash
$ npm run watch
$ npm run build
$ npm run start
$ npm run test
```

其中，`start`和`test`属于特殊命令，可以省略`run`。

```bash
$ npm start
$ npm test
```

如果希望一个操作的输出，是另一个操作的输入，可以借用Linux系统的管道命令，将两个操作连在一起。

```javascript
"build-js": "browserify browser/main.js | uglifyjs -mc > static/bundle.js"
```

但是，更方便的写法是引用其他`npm run`命令。

```javascript
"build": "npm run build-js && npm run build-css"
```

上面的写法是先运行`npm run build-js`，然后再运行`npm run build-css`，两个命令中间用`&&`连接。如果希望两个命令同时平行执行，它们中间可以用`&`连接。

下面是一个流操作的例子。

```javascript
"devDependencies": {
  "autoprefixer": "latest",
  "cssmin": "latest"
},

"scripts": {
  "build:css": "autoprefixer -b 'last 2 versions' < assets/styles/main.css | cssmin > dist/main.css"
}
```

写在`scripts`属性中的命令，也可以在`node_modules/.bin`目录中直接写成bash脚本。下面是一个bash脚本。

```javascript
#!/bin/bash

cd site/main
browserify browser/main.js | uglifyjs -mc > static/bundle.js
```

假定上面的脚本文件名为build.sh，并且权限为可执行，就可以在scripts属性中引用该文件。

```javascript
"build-js": "bin/build.sh"
```

### 参数

`npm run`命令还可以添加参数。

```javascript
"scripts": {
  "test": "mocha test/"
}
```

上面代码指定`npm test`，实际运行`mocha test/`。如果要通过`npm test`命令，将参数传到mocha，则参数之前要加上两个连词线。

```bash
$ npm run test -- anothertest.js
# 等同于
$ mocha test/ anothertest.js
```

上面命令表示，mocha要运行所有`test`子目录的测试脚本，以及另外一个测试脚本`anothertest.js`。

`npm run`本身有一个参数`-s`，表示关闭npm本身的输出，只输出脚本产生的结果。

```bash
// 输出npm命令头
$ npm run test

// 不输出npm命令头
$ npm run -s test
```

### scripts脚本命令最佳实践

`scripts`字段的脚本命令，有一些最佳实践，可以方便开发。首先，安装`npm-run-all`模块。

```bash
$ npm install npm-run-all --save-dev
```

这个模块用于运行多个`scripts`脚本命令。

```bash
# 继发执行
$ npm-run-all build:html build:js
# 等同于
$ npm run build:html && npm run build:js

# 并行执行
$ npm-run-all --parallel watch:html watch:js
# 等同于
$ npm run watch:html & npm run watch:js

# 混合执行
$ npm-run-all clean lint --parallel watch:html watch:js
# 等同于
$ npm-run-all clean lint
$ npm-run-all --parallel watch:html watch:js

# 通配符
$ npm-run-all --parallel watch:*
```

（1）start脚本命令

`start`脚本命令，用于启动应用程序。

```javascript
"start": "npm-run-all --parallel dev serve"
```

上面命令并行执行`dev`脚本命令和`serve`脚本命令，等同于下面的形式。

```bash
$ npm run dev & npm run serve
```

如果start脚本没有配置，`npm start`命令默认执行下面的脚本，前提是模块的根目录存在一个server.js文件。

```bash
$ node server.js
```

（2）dev脚本命令

`dev`脚本命令，规定开发阶段所要做的处理，比如构建网页资源。

```javascript
"dev": "npm-run-all dev:*"
```

上面命令用于继发执行所有`dev`的子命令。

```javascript
"predev:sass": "node-sass --source-map src/css/hoodie.css.map --output-style nested src/sass/base.scss src/css/hoodie.css"
```

上面命令将sass文件编译为css文件，并生成source map文件。

```javascript
"dev:sass": "node-sass --source-map src/css/hoodie.css.map --watch --output-style nested src/sass/base.scss src/css/hoodie.css"
```

上面命令会监视sass文件的变动，只要有变动，就自动将其编译为css文件。

```javascript
"dev:autoprefix": "postcss --use autoprefixer --autoprefixer.browsers \"> 5%\" --output src/css/hoodie.css src/css/hoodie.css"
```

上面命令为css文件加上浏览器前缀，限制条件是只考虑市场份额大于5%的浏览器。

（3）serve脚本命令

`serve`脚本命令用于启动服务。

```javascript
"serve": "live-server dist/ --port=9090"
```

上面命令启动服务，用的是[live-server](http://npmjs.com/package/live-server)模块，将服务启动在9090端口，展示`dist`子目录。

`live-server`模块有三个功能。

- 启动一个HTTP服务器，展示指定目录的`index.html`文件，通过该文件加载各种网络资源，这是`file://`协议做不到的。
- 添加自动刷新功能。只要指定目录之中，文件有任何变化，它就会刷新页面。
- `npm run serve`命令执行以后，自动打开浏览器。、

以前，上面三个功能需要三个模块来完成：`http-server`、`live-reload`和`opener`，现在只要`live-server`一个模块就够了。

（4）test脚本命令

`test`脚本命令用于执行测试。

```javascript
"test": "npm-run-all test:*",
"test:lint": "sass-lint --verbose --config .sass-lint.yml src/sass/*"
```

上面命令规定，执行测试时，运行`lint`脚本，检查脚本之中的语法错误。

（5）prod脚本命令

`prod`脚本命令，规定进入生产环境时需要做的处理。

```javascript
"prod": "npm-run-all prod:*",
"prod:sass": "node-sass --output-style compressed src/sass/base.scss src/css/prod/hoodie.min.css",
"prod:autoprefix": "postcss --use autoprefixer --autoprefixer.browsers "> 5%" --output src/css/prod/hoodie.min.css src/css/prod/hoodie.min.css"
```

上面命令将sass文件转为css文件，并加上浏览器前缀。

（6）help脚本命令

`help`脚本命令用于展示帮助信息。

```javascript
"help": "markdown-chalk --input DEVELOPMENT.md"
```

上面命令之中，`markdown-chalk`模块用于将指定的markdown文件，转为彩色文本显示在终端之中。

（7）docs脚本命令

`docs`脚本命令用于生成文档。

```javascript
"docs": "kss-node --source src/sass --homepage ../../styleguide.md"
```

上面命令使用`kss-node`模块，提供源码的注释生成markdown格式的文档。

### pre- 和 post- 脚本

`npm run`为每条命令提供了`pre-`和`post-`两个钩子（hook）。以`npm run lint`为例，执行这条命令之前，npm会先查看有没有定义prelint和postlint两个钩子，如果有的话，就会先执行`npm run prelint`，然后执行`npm run lint`，最后执行`npm run postlint`。

```javascript
{
  "name": "myproject",
  "devDependencies": {
    "eslint": "latest"
    "karma": "latest"
  },
  "scripts": {
    "lint": "eslint --cache --ext .js --ext .jsx src",
    "test": "karma start --log-leve=error karma.config.js --single-run=true",
    "pretest": "npm run lint",
    "posttest": "echo 'Finished running tests'"
  }
}
```

上面代码是一个`package.json`文件的例子。如果执行`npm test`，会按下面的顺序执行相应的命令。

1. `pretest`
1. `test`
1. `posttest`

如果执行过程出错，就不会执行排在后面的脚本，即如果prelint脚本执行出错，就不会接着执行lint和postlint脚本。

下面是一个例子。

```javascript
{
  "test": "karma start",
  "test:lint": "eslint . --ext .js --ext .jsx",
  "pretest": "npm run test:lint"
}
```

上面代码中，在运行`npm run test`之前，会自动检查代码，即运行`npm run test:lint`命令。

下面是一些常见的`pre-`和`post-`脚本。

- `prepublish`：发布一个模块前执行。
- `postpublish`：发布一个模块后执行。
- `preinstall`：用户执行`npm install`命令时，先执行该脚本。
- `postinstall`：用户执行`npm install`命令时，安装结束后执行该脚本，通常用于将下载的源码编译成用户需要的格式，比如有些模块需要在用户机器上跟本地的C++模块一起编译。
- `preuninstall`：卸载一个模块前执行。
- `postuninstall`：卸载一个模块后执行。
- `preversion`：更改模块版本前执行。
- `postversion`：更改模块版本后执行。
- `pretest`：运行`npm test`命令前执行。
- `posttest`：运行`npm test`命令后执行。
- `prestop`：运行`npm stop`命令前执行。
- `poststop`：运行`npm stop`命令后执行。
- `prestart`：运行`npm start`命令前执行。
- `poststart`：运行`npm start`命令后执行。
- `prerestart`：运行`npm restart`命令前执行。
- `postrestart`：运行`npm restart`命令后执行。

对于最后一个`npm restart`命令，如果没有设置`restart`脚本，`prerestart`和`postrestart`会依次执行stop和start脚本。

另外，不能在`pre`脚本之前再加`pre`，即`prepretest`脚本不起作用。

注意，即使Npm可以自动运行`pre`和`post`脚本，也可以手动执行它们。

```bash
$ npm run prepublish
```

下面是`post install`的例子。

```javascript
{
  "postinstall": "node lib/post_install.js"
}
```

上面的这个命令，主要用于处理从Git仓库拉下来的源码。比如，有些源码是用TypeScript写的，可能需要转换一下。

下面是`publish`钩子的一个例子。

```javascript
{
  "dist:modules": "babel ./src --out-dir ./dist-modules",
  "gh-pages": "webpack",
  "gh-pages:deploy": "gh-pages -d gh-pages",
  "prepublish": "npm run dist:modules",
  "postpublish": "npm run gh-pages && npm run gh-pages:deploy"
}
```

上面命令在运行`npm run publish`时，会先执行Babel编译，然后调用Webpack构建，最后发到Github Pages上面。

以上都是npm相关操作的钩子，如果安装某些模块，还能支持Git相关的钩子。下面以[husky](https://github.com/typicode/husky)模块为例。

```bash
$ npm install husky --save-dev
```

安装以后，就能在`package.json`添加`precommit`、`prepush`等钩子。

```javascript
{
    "scripts": {
        "lint": "eslint yourJsFiles.js",
        "precommit": "npm run test && npm run lint",
        "prepush": "npm run test && npm run lint",
        "...": "..."
    }
}
```

类似作用的模块还有`pre-commit`、`precommit-hook`等。

### 内部变量

scripts字段可以使用一些内部变量，主要是package.json的各种字段。

比如，package.json的内容是`{"name":"foo", "version":"1.2.5"}`，那么变量`npm_package_name`的值是foo，变量`npm_package_version`的值是1.2.5。

```javascript
{
  "scripts":{
    "bundle": "mkdir -p build/$npm_package_version/"
  }
}
```

运行`npm run bundle`以后，将会生成`build/1.2.5/`子目录。

`config`字段也可以用于设置内部字段。

```javascript
  "name": "fooproject",
  "config": {
    "reporter": "xunit"
  },
  "scripts": {
    "test": "mocha test/ --reporter $npm_package_config_reporter"
  }
```

上面代码中，变量`npm_package_config_reporter`对应的就是reporter。

### 通配符

npm 的通配符的规则如下。

- `*` 匹配0个或多个字符
- `?` 匹配1个字符
- `[...]` 匹配某个范围的字符。如果该范围的第一个字符是`!`或`^`，则匹配不在该范围的字符。
- `!(pattern|pattern|pattern)` 匹配任何不符合给定的模式
- `?(pattern|pattern|pattern)` 匹配0个或1个给定的模式
- `+(pattern|pattern|pattern)` 匹配1个或多个给定的模式
- `*(a|b|c)` 匹配0个或多个给定的模式
- `@(pattern|pat*|pat?erN)` 只匹配给定模式之一
- `**` 如果出现在路径部分，表示0个或多个子目录。

