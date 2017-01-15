# 安装

## npm install

`npm install`命令用于安装模块。

```bash
$ npm install lodash
```

上面命令在当前目录中安装了`lodash`模块。

默认安装的是最新版本（即`latest`标签指向的版本），但是你可以使用`@`指定安装的版本。

```bash
$ npm install lodash@4.17.4
```

`^`用来指定大版本。

```bash
$ npm install lodash@^4.0.0
```

上面命令指定安装最新的`4.x.x`版。

默认情况下，`npm install`不会修改`package.json`。`--save`或`-S`参数，将模块加入`package.json`的`dependencies`字段，`--save-dev`或`-D`，将模块加入`package.json`的`devDependencies`字段。

写入`package.json`的时候，如果`npm install`没有指定版本，npm 会在安装的版本号前面添加`^`。比如，假定 lodash 的最新版是4.17.4，那么执行`npm install -S lodash`以后，`package.json`将写入的版本范围如下。

```javascript
  "dependencies": {
    "lodash": "^4.17.4",
    ...
  },
```

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

使用`--global`或`-g`参数时，模块将全局安装。此时，加上`--save`、`--save-exact`、`--save-dev`都是无效的。

