# 概述

`npm`有两种含义。

首先，npm 是一个网站，用来登记和管理 Node 的模块，网址为[npmjs.org](http://npmjs.org)。

其次，npm 是一个命令行软件，用来在用户的电脑上安装和管理 Node 模块。

## 安装

`npm`不需要单独安装。安装 Node 的时候，会默认一起安装`npm`。

但是，默认安装的`npm`可能不是最新版本。在 Node 安装成功后，最好用下面的命令，更新到最新版本。

```bash
$ npm install npm@latest -g
```

然后，运行下面的命令，查看一下 npm 的版本。

```bash
$ npm --version
# 等同于
$ npm -v
```

下面三个命令，也可以用来获取帮助。

```bash
# 查看 npm 命令列表
$ npm help

# 查看各个命令的简单用法
$ npm -l

# 查看 npm 的配置
$ npm config list -l
```

## npm init

`npm init`用来初始化生成一个新的`package.json`文件。它会向用户提问一系列问题，如果你觉得不用修改默认配置，一路回车就可以了。

如果使用了`-f`（代表force）、`-y`（代表yes），则跳过提问阶段，直接生成一个新的`package.json`文件。

```bash
$ npm init -y
```

## npm set

`npm set`用来设置环境变量。

```bash
$ npm set init-author-name 'Your name'
$ npm set init-author-email 'Your email'
$ npm set init-author-url 'http://yourdomain.com'
$ npm set init-license 'MIT'
```

上面命令等于为`npm init`设置了默认值，以后执行`npm init`的时候，`package.json`的作者姓名、邮件、主页、许可证字段就会自动写入预设的值。这些信息会存放在用户主目录的` ~/.npmrc`文件，使得用户不用每个项目都输入。如果某个项目有不同的设置，可以针对该项目运行`npm config`。

```bash
$ npm set save-exact true
```

上面命令设置加入模块时，`package.json`将记录模块的确切版本，而不是一个可选的版本范围。

## npm config

```bash
$ npm config set prefix $dir
```

上面的命令将指定的`$dir`目录，设为模块的全局安装目录。如果当前有这个目录的写权限，那么运行`npm install`的时候，就不再需要`sudo`命令授权了。

```bash
$ npm config set save-prefix '~'
```

上面的命令使得`npm install --save`和`npm install --save-dev`安装新模块时，允许的版本范围从克拉符号（`^`）改成波浪号（`~`），即从允许小版本升级，变成只允许补丁包的升级。

```bash
$ npm config set init.author.name $name
$ npm config set init.author.email $email
```

上面命令指定使用`npm init`时，生成的`package.json`文件的字段默认值。

## npm info

`npm info`命令可以查看每个模块的具体信息。比如，查看underscore模块的信息。

```bash
$ npm info underscore
{ name: 'underscore',
  description: 'JavaScript\'s functional programming helper library.',
  'dist-tags': { latest: '1.5.2', stable: '1.5.2' },
  repository:
   { type: 'git',
     url: 'git://github.com/jashkenas/underscore.git' },
  homepage: 'http://underscorejs.org',
  main: 'underscore.js',
  version: '1.5.2',
  devDependencies: { phantomjs: '1.9.0-1' },
  licenses:
   { type: 'MIT',
     url: 'https://raw.github.com/jashkenas/underscore/master/LICENSE' },
  files:
   [ 'underscore.js',
     'underscore-min.js',
     'LICENSE' ],
  readmeFilename: 'README.md'}
```

上面命令返回一个JavaScript对象，包含了underscore模块的详细信息。这个对象的每个成员，都可以直接从info命令查询。

```bash
$ npm info underscore description
JavaScript's functional programming helper library.

$ npm info underscore homepage
http://underscorejs.org

$ npm info underscore version
1.5.2
```

## npm search

`npm search`命令用于搜索npm仓库，它后面可以跟字符串，也可以跟正则表达式。

```bash
$ npm search <搜索词>
```

下面是一个例子。

```bash
$ npm search node-gyp
// NAME                  DESCRIPTION
// autogypi              Autogypi handles dependencies for node-gyp projects.
// grunt-node-gyp        Run node-gyp commands from Grunt.
// gyp-io                Temporary solution to let node-gyp run `rebuild` under…
// ...
```

## npm list

`npm list`命令以树型结构列出当前项目安装的所有模块，以及它们依赖的模块。

```bash
$ npm list
```

加上global参数，会列出全局安装的模块。

```bash
$ npm list -global
```

`npm list`命令也可以列出单个模块。

```bash
$ npm list underscore
```

## npm link

开发NPM模块的时候，有时我们会希望，边开发边试用，比如本地调试的时候，`require('myModule')`会自动加载本机开发中的模块。Node规定，使用一个模块时，需要将其安装到全局的或项目的`node_modules`目录之中。对于开发中的模块，解决方法就是在全局的`node_modules`目录之中，生成一个符号链接，指向模块的本地目录。

`npm link`就能起到这个作用，会自动建立这个符号链接。

请设想这样一个场景，你开发了一个模块`myModule`，目录为`src/myModule`，你自己的项目`myProject`要用到这个模块，项目目录为`src/myProject`。首先，在模块目录（`src/myModule`）下运行`npm link`命令。

```bash
src/myModule$ npm link
```

上面的命令会在NPM的全局模块目录内，生成一个符号链接文件，该文件的名字就是`package.json`文件中指定的模块名。

```bash
/path/to/global/node_modules/myModule -> src/myModule
```

这个时候，已经可以全局调用`myModule`模块了。但是，如果我们要让这个模块安装在项目内，还要进行下面的步骤。

切换到项目目录，再次运行`npm link`命令，并指定模块名。

```bash
src/myProject$ npm link myModule
```

上面命令等同于生成了本地模块的符号链接。

```bash
src/myProject/node_modules/myModule -> /path/to/global/node_modules/myModule
```

然后，就可以在你的项目中，加载该模块了。

```javascript
var myModule = require('myModule');
```

这样一来，`myModule`的任何变化，都可以直接反映在`myProject`项目之中。但是，这样也出现了风险，任何在`myProject`目录中对`myModule`的修改，都会反映到模块的源码中。

如果你的项目不再需要该模块，可以在项目目录内使用`npm unlink`命令，删除符号链接。

```bash
src/myProject$ npm unlink myModule
```

## npm bin

`npm bin`命令显示相对于当前目录的，Node模块的可执行脚本所在的目录（即`.bin`目录）。

```bash
# 项目根目录下执行
$ npm bin
./node_modules/.bin
```

## npm adduser

`npm adduser`用于在npmjs.com注册一个用户。

```bash
$ npm adduser
Username: YOUR_USER_NAME
Password: YOUR_PASSWORD
Email: YOUR_EMAIL@domain.com
```

## npm publish

`npm publish`用于将当前模块发布到`npmjs.com`。执行之前，需要向`npmjs.com`申请用户名。

```bash
$ npm adduser
```

如果已经注册过，就使用下面的命令登录。

```bash
$ npm login
```

登录以后，就可以使用`npm publish`命令发布。

```bash
$ npm publish
```

如果当前模块是一个beta版，比如`1.3.1-beta.3`，那么发布的时候需要使用`tag`参数，将其发布到指定标签，默认的发布标签是`latest`。

```bash
$ npm publish --tag beta
```

如果发布私有模块，模块初始化的时候，需要加上`scope`参数。只有npm的付费用户才能发布私有模块。

```bash
$ npm init --scope=<yourscope>
```

如果你的模块是用ES6写的，那么发布的时候，最好转成ES5。首先，需要安装Babel。

```javascript
$ npm install --save-dev babel-cli@6 babel-preset-es2015@6
```

然后，在`package.json`里面写入`build`脚本。

```javascript
"scripts": {
  "build": "babel source --presets babel-preset-es2015 --out-dir distribution",
  "prepublish": "npm run build"
}
```

运行上面的脚本，会将`source`目录里面的ES6源码文件，转为`distribution`目录里面的ES5源码文件。然后，在项目根目录下面创建两个文件`.npmignore`和`.gitignore`，分别写入以下内容。

```javascrip
// .npmignore
source

// .gitignore
node_modules
distribution
```

## npm deprecate

如果想废弃某个版本的模块，可以使用`npm deprecate`命令。

```bash
$ npm deprecate my-thing@"< 0.2.3" "critical bug fixed in v0.2.3"
```

运行上面的命令以后，小于`0.2.3`版本的模块的`package.json`都会写入一行警告，用户安装这些版本时，这行警告就会在命令行显示。

## npm owner

模块的维护者可以发布新版本。`npm owner`命令用于管理模块的维护者。

```bash
# 列出指定模块的维护者
$ npm owner ls <package name>

# 新增维护者
$ npm owner add <user> <package name>

# 删除维护者
$ npm owner rm <user> <package name>
```

## 其他命令

### npm home，npm repo

`npm home`命令可以打开一个模块的主页，`npm repo`命令则是打开一个模块的代码仓库。

```bash
$ npm home $package
$ npm repo $package
```

这两个命令不需要模块先安装。

### npm outdated

`npm outdated`命令检查当前项目所依赖的模块，是否已经有新版本。

```bash
$ npm outdated

Package        Current  Wanted  Latest  Location
normalize.css    4.0.0   4.2.0   5.0.0  photo-gallery
```

它会输出当前版本（current version）、应当安装的版本（wanted version）和最新发布的版本（latest version）。

### npm prune

`npm prune`检查当前项目的`node_modules`目录中，是否有`package.json`里面没有提到的模块，然后将所有这些模块输出在命令行。

```bash
$ npm prune
```

### npm shrinkwrap

`npm shrinkwrap`的作用是锁定当前项目的以来模块的版本。

```bash
$ npm shrinkwrap
```

运行该命令后，会在当前项目的根目录下生成一个`npm-shrinkwrap.json`文件，内容是`node_modules`目录下所有已经安装模块的版本。

下次运行`npm install`命令时，`npm`发现当前目录下有`npm-shrinkwrap.json`文件，就会只安装里面提到的模块，且版本也会保持一致。

## 参考链接

- James Halliday, [task automation with npm run](http://substack.net/task_automation_with_npm_run): npm run命令（package.json文件的script属性）的用法
- Keith Cirkel, [How to Use npm as a Build Tool](http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/)
- justjs, [npm link: developing your own npm modules without tears](http://justjs.com/posts/npm-link-developing-your-own-npm-modules-without-tears)
- hoodie-css, [Development Environment Help](https://github.com/hoodiehq/hoodie-css/blob/feature/build-automation/DEVELOPMENT.md)
- Stephan Bönnemann, [How to make use of npm’s package distribution tags to create release channels](https://medium.com/greenkeeper-blog/one-simple-trick-for-javascript-package-maintainers-to-avoid-breaking-their-user-s-software-and-to-6edf06dc5617#.5omqgsg45)
- Alex Booker, [How to Build and Publish ES6 npm Modules Today, with Babel](https://booker.codes/how-to-build-and-publish-es6-npm-modules-today-with-babel/)
