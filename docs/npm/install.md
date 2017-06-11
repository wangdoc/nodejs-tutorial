# 安装

## npm install

### 基本用法

`npm install`命令用于安装模块。`npm i`是该命令的别名。

```bash
$ npm install lodash
```

上面命令在当前目录中安装了`lodash`模块。

默认安装的是最新版本（即`latest`标签指向的版本），但是你可以 semver 表达式指定安装的版本。

```bash
# 等同于 npm install lodash
$ npm install lodash@latest

# 指定确定的版本
$ npm install lodash@4.17.4

# 指定版本范围
$ npm install sax@">=4.15.0 <4.18.0"

# 指定大版本
$ npm install lodash@^4.0.0
```

上面最后一行命令，指定安装最新的`4.x`版。

默认情况下，`npm install`不会修改`package.json`。`--save`或`-S`参数，将模块写入`package.json`的`dependencies`字段，`--save-dev`或`-D`，将模块加入`package.json`的`devDependencies`字段。

```bash
# 将模块写入 package.json 的 dependencies 字段
$ npm install lodash --save
$ npm install lodash -S

# 将模块写入 package.json 的 devDependencies 字段
$ npm install lodash --save-dev
$ npm install lodash -D
```

上面命令将模块写入`package.json`的时候，如果`npm install`没有指定版本，npm 会在安装的版本号前面添加`^`。比如，假定 lodash 的最新版是4.17.4，那么执行`npm install -S lodash`以后，`package.json`将写入的版本范围如下。

```javascript
  "dependencies": {
    "lodash": "^4.17.4",
    ...
  },
```

上面的`^4.17.4`，表示兼容 4.17.4 以后的 4.x 版。

如果不希望出现这种默认行为，可以使用`--save-exact`指定只将当前确定的版本号，写入`package.json`。

```bash
$ npm install lodash --save --save-exact
# 或者
$ npm install lodash --save-dev --save-exact
```

执行上面的命令以后，`package.json`里面的版本号将是固定的。

```javascript
  "dependencies": {
    "lodash": "4.17.4",
    ...
  },
```

注意，`--save-exact`单独使用是无效的，必须与`--save`或`--save-dev`一起使用。

`npm install`也支持直接输入 Github 代码库地址。

```bash
$ npm install git://github.com/package/path.git
$ npm install git://github.com/package/path.git#0.1.0
```

### 全局安装

npm 还可以将模块安装在全局，供所有项目使用。注意，一般情况下，全局安装只适用于一些命令行工具。

全局安装，要使用`--global`或`-g`参数。此时，加上`--save`、`--save-exact`、`--save-dev`都是无效的。

```bash
$ npm install create-react-app --global
# 或者
$ npm install create-react-app -g
```

上面命令全局安装`create-react-app`模块。

### 安装机制与重装

本地安装后，模块将存放在当前项目的`node_modules`子目录，然后只有在项目目录之中，才能调用这个模块。全局安装后，模块将存放在全局目录之中（通常是`/usr/local/lib/`），所有项目都可以调用这个模块，但是不应该这样做。

安装之前，`npm install`会先检查，`node_modules`目录之中是否已经存在指定模块。如果存在，就不再重新安装了，即使远程仓库已经有了一个新版本，也是如此。

如果你希望，一个模块不管是否安装过，npm 都要强制重新安装，可以使用`-f`或`--force`参数。

```bash
$ npm install lodash --force
```

如果所有模块都要强制重新安装，那就删除`node_modules`目录，重新执行`npm install`。

```bash
$ rm -rf node_modules
$ npm install
```

### 安装项目依赖

不使用任何参数时，只使用`npm install`，会默认安装`package.json`里面的`dependencies`字段和`devDependencies`字段列出的所有模块。

```bash
$ npm install
```

如果使用`--production`参数，可以只安装`dependencies`字段的模块。

```bash
$ npm install --production
# 等同于
$ NODE_ENV=production npm install
```

## 避免安装权限

默认情况下，npm 全局模块都安装在系统目录（比如`/usr/local/lib/`），普通用户没有写入权限，需要用到`sudo`命令。这不是很方便，我们可以在没有 root 权限的情况下，安装全局模块。

首先，在主目录下新建配置文件`.npmrc`，在该文件中将`prefix`变量定义到一个你的个人目录下面（假定该目录是`~/my-npm-modules`）。

```bash
prefix = /home/yourUsername/my-npm-modules
```

此后，全局安装的模块都会安装在这个子目录中，npm也会到`~/my-npm-modules/bin`目录去寻找命令。

最后，将这个路径在`.bash_profile`文件（或`.bashrc`文件）中加入`PATH`变量。

```bash
export PATH=~/my-npm-modules/bin:$PATH
```

## npm update

`npm update`命令可以更新本地安装的模块到最新版本（符合 semver 的设置），如果该模块没有安装，则会安装该模块。`npm up`和`npm upgrade`是该命令的缩写。

```bash
# 升级当前项目的某个模块
$ npm update lodash

# 升级全局安装的某个模块
$ npm update -g lodash
```

不使用任何参数时，将更新当前项目的所有`dependencies`字段里面的模块。如果有模块没有安装，也将一起安装。

```bash
$ npm update
```

`--dev`参数会连带安装和更新`devDependencies`字段里面的模块。

```bash
$ npm update --dev
```

更新时，会先到远程仓库查询最新版本，然后查询本地版本。如果本地版本不存在，或者远程版本较新，就会安装最新版本。

使用`-S`或`--save`参数，可以在安装的同时，更新`package.json`里面模块的版本号。

```javascript
// 更新之前的package.json
dependencies: {
  dep1: "^1.1.1"
}

// 更新之后的package.json
dependencies: {
  dep1: "^1.2.2"
}
```

注意，从 npm v2.6.1 开始，`npm update`只更新顶层模块，而不更新依赖的依赖，以前版本是递归更新的。如果想取到老版本的效果，要使用下面的命令。

```bash
$ npm --depth 9999 update
```

注意，如果已经安装的模块版本比 semver 指定的版本更加新时，`npm update`有降级效果。

## npm uninstall

`npm uninstall`命令，用来卸载已安装的模块。`npm remove`、`npm rm`、`npm r`、`npm un`和`npm unlink`，都是该命令的别名。

```bash
# 卸载项目模块
$ npm uninstall lodash

# 卸载全局模块
$ npm uninstall lodash --global
$ npm uninstall lodash -g
```

使用`--save`参数（或`-S`），该模块将会从`package.json`的`dependencies`字段中移除。使用`--save-dev`参数（或`-D`）时，该模块将会从`package.json`的`devDependencies`字段中移除。

```bash
$ npm uninstall lodash --save
$ npm uninstall lodash --save-dev
```

## package-lock.json

从 npm 5.0 版本开始，npm 模块默认会锁版本，生成`package-lock.json`文件。下面是一个例子。

```javascript
{
  "name": "react-example",
  "version": "1.0.0",
  "lockfileVersion": 1,
  "dependencies": {
    "has-flag": {
      "version": "1.0.0",
      "resolved": "https://registry.npmjs.org/has-flag/-/has-flag-1.0.0.tgz",
      "integrity": "sha1-nZ55MWXOAXoA8AQYxD+UKnsdEfo="
    },
    "supports-color": {
      "version": "3.2.3",
      "resolved": "https://registry.npmjs.org/supports-color/-/supports-color-3.2.3.tgz",
      "integrity": "sha1-ZawFBLOVQXHYpklGsq48u4pfVPY="
    }
  }
}
```

这个文件不仅指定了每个模块的精确版本，而且还指定了`node_modules`目录的结构（即哪些模块要安装在目录的顶层）。

