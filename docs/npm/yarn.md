# Yarn 的用法

## 简介

Yarn 是 Facebook 联合其他大公司推出的模块管理器。相比 npm，它有两个显著特点。

（1）安装速度较快。

Yarn 采用平行安装模式，而 npm 采用的是线性模式，只有前一个模块安装完，才会安装下一个。

（2）默认开启“版本锁定”功能

Yarn 希望安装依赖时，所有依赖的版本在不同机器都保持相同。为了达到这个目的，第一次安装依赖时，它默认生成一个锁定文件`yarn.lock`，将这个文件放到代码库之中，下次安装时就能保证，总是安装相同版本的依赖。这与`npm shrinkwrap`命令生成的`npm-shrinkwrap.json`的作用相似，只不过 Yarn 默认就可以生成这个文件。

## 全局参数 global

如果要全局执行一个命令，必须加上`global`参数。目前，`add`、`bin`、`ls`和`remove`四个命令，支持`global`参数。

```bash
$ yarn global add create-react-app --prefix /usr/local
```

## yarn install

`yarn install`命令用于安装一个模块。如果`yarn.lock`文件存在，会优先读取该文件，按照该文件指定的版本安装。

使用`--production`参数或环境变量`NODE_ENV`等于`production`，将不会安装`devDependencies`字段指定的模块。

```bash
$ yarn install --production
```

如果使用`--no-lockfile`参数，`yarn install`将不会读取或生成`yarn.lock`。

```bash
$ yarn install --no-lockfile
```

## yarn add

`yarn add`命令允许新增安装一个模块。它默认会将该模块加入`package.json`文件的`dependencies`字段。如果想加入`devDependencies`字段，要使用`--dev`参数。

```bash
$ yarn add package-name
$ yarn add package-name@1.2.3
$ yarn add package-name@tag
```

## yarn licenses

`yarn licenses`命令有两个子命令。

`yarn licenses ls`命令列出所有模块的许可证。

`yarn licenses generate-disclaimer`命令将所有模块的许可证的条款，全部显示出来。

## yarn why

`yarn why`命令列出之所以安装某个模块的原因，即为什么安装了它。

```bash
$ yarn why jest
```

你也可以用它分析某个目录或者某个文件。

```bash
$ yarn why node_modules/once
$ yarn why node_modules/once/once.js
```

## yarn upgrade

`yarn upgrade`命令会按照`package.json`里面指定的版本范围，更新依赖版本，重新生成`yarn.lock`。

```bash
$ yarn upgrade
```

如果单独升级某个模块，`yarn upgrade`会将它升级到`latest`标签指定的版本，然后改写`package.json`。这意味着该命令可能会将一个`1.x`版本的模块，升级到`2.x`。

```bash
$ yarn upgrade d3-scale
```

更新时指定版本范围或标签，也是允许的。

```bash
$ yarn upgrade d3-scale@1.0.2
$ yarn upgrade react@next
```

## yarn generate-lock-entry

`yarn generate-lock-entry`命令依照`package.json`文件，生成`yarn.lock`文件。

```bash
$ yarn generate-lock-entry
```

## yarn.lock 文件

`yarn.lock`是一个锁文件，用来记录当前项目的依赖模块的精确版本。只要项目的根目录有这个文件，下次安装依赖的时候，总是会安装一模一样的`node_modules`目录，这个特点称为决定性（determinism）。

如果当前项目没有这个文件，那么第一次运行`yarn install`或者`yarn add [模块名]`命令的时候，就会生成这个文件。以后，再运行`yarn add`命令，会更新这个文件。

举例来说，`yarn add supports-color`命令会产生下面的`yarn.lock`文件。

```javascript
has-flag@^1.0.0:
  version "1.0.0"
  resolved "https://registry.yarnpkg.com/has-flag/-/has-flag-1.0.0.tgz#9d9e793165ce017a00f00418c43f942a7b1d11fa"

supports-color@^3.2.3:
  version "3.2.3"
  resolved "https://registry.yarnpkg.com/supports-color/-/supports-color-3.2.3.tgz#65ac0504b3954171d8a64946b2ae3cbb8a5f54f6"
  dependencies:
    has-flag "^1.0.0"
```

上面代码中，模块之间使用空行分隔。每个模块会指明当前安装的精确版本（`version`字段）和下载地址（`resovled`字段），以及依赖的模块（`dependencies`字段）。

注意，从`yarn.lock`文件看不出来，哪个模块会安装在`node_modules`目录的顶层，必须结合`package.json`才能看出来，具体的算法由 Yarn 决定。这也意味着，不同版本的 Yarn 处理同样的`yarn.lock`文件，可能会得到不一样的`node_modules`目录，但是每个模块的版本肯定都是相同的。只有相同版本的 Yarn，才能保证一定会得到相同的`node_modules`目录。

## 参考链接

- [Yarn determinism](https://yarnpkg.com/blog/2017/05/31/determinism/), by Sebastian McKenzie
- [Running Yarn offline](https://yarnpkg.com/blog/2016/11/24/offline-mirror/), by Konstantin Raev
- [Yarn CLI](https://yarnpkg.com/zh-Hans/docs/cli/)
