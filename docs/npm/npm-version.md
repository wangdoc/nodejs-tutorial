# npm version

`npm version`用来指定模块的版本，然后会将新的版本号写入`package.json`和`package-lock.json`。

它的命令行用法如下。

```bash
npm version [
  <newversion> |
  major | minor | patch | premajor | preminor | prepatch | prerelease |
  from-git
]
```

上面可以归纳为三种用法。

（1）`<newversion>`：自己指定版本号。

（2）七个版本关键字：patch，minor，major，prepatch，preminor，premajor，prerelease。这时原有版本号，会在相应的位置增加1。

`major`：规则如下。

（1）如果没有预发布号，则增加主版本号，并将次版本号和预发布号设为`0`。

```bash
# 版本号从 3.1.0 变为 4.0.0
$ npm version major
```

（2）如果有预发布号，且次版本号和补丁号都为`0`，则不升级主版本号，只去掉预发布号。

```bash
# 版本号从 4.0.0 变为 5.0.0-0
$ npm version premajor

# 版本号从 5.0.0-0 变为 5.0.0
$ npm version major
```

（3）如果有预发布号，且次版本号和补丁号都不为`0`，则增加主版本号，将次版本号和补丁号都置为`0`，并去掉预发布号。

```bash
# 版本号从 5.0.0-0 变为 5.1.0-0
$ npm version preminor : 5.0.0-0–> 5.1.0-0

# 版本号从 5.1.0-0 变为 6.0.0
$ npm version major
```

`minor`：规则如下。

（1）如果没有预发布号，则增加次版本号，并将补丁号设为`0`。

```bash
# 版本号从 2.0.1 变为 2.1.0
$ npm version minor
```

（2）如果有预发布号，且补丁号为`0`，则去掉预发布号，其他不变。

```bash
# 版本号从 2.1.0 变为 3.0.0-0
$ npm version premajor

# 版本号从 3.0.0-0 变为 3.0.0
$ npm version minor
```

（3）如果有预发布号，且补丁号不为`0`，则去掉预发布号，增加次版本号，补丁号设为`0`。

```bash
# 版本号从 3.0.0 变为 3.0.1-0
$ npm version prepatch

# 版本号从 3.0.1-0 变为 3.1.0
$ npm version minor
```

`patch`：如果预发布号，则去掉预发布后，其他保持不变；如果没有预发布号，则升级补丁号。

```bash
# 版本号从 2.0.0-0 变为 2.0.0
$ npm version patch

# 版本号从 2.0.0 变为 2.0.1
$ npm version patch
```

`premajor`：增加主版本号，将次版本号和补丁号都设为`0`，增加预发布号为`0`。

```bash
# 版本号从 1.1.0-0 变为 2.0.0-0
$ npm version premajor
```

`preminor`：增加次版本号，补丁号设为`0`，预发布号设为`0`。

```bash
# 版本号从 1.0.2-0 变为 1.1.0-0
$ npm version preminor
```

`prepatch`：增加补丁号，同时预发布号设为`0`。

```bash
# 版本号从 1.0.1-1 变为 1.0.2-0
$ npm version prepatch
```

`prerelease`：如果没有预发布号，则增加补丁号，同时预发布号设为0；如果有预发布号，则预发布号增加1。

```bash
# 版本号从 1.0.0 变为 1.0.1-0
$ npm version prerelease

# 版本号从 1.0.1-0 变为 1.0.1-1
$ npm version prerelease
```

（3）`from-git`：使用最新的 Git 标签，将其作为 npm 版本。

这个命令如果是在一个 Git 仓库里面运行，它会创造一个新的提交和标签。如果不希望生成标签，可以使用命令行参数`--no-git-tag-version`。

命令行参数`-m`或者`--message`可以指定提交信息。提交信息里面的`%s`参数会被替换成新的版本号。

```bash
$ npm version patch -m "Upgrade to %s for reasons"
```

## --pre-id

`npm version`命令的`--pre-id`参数，可以指定预发布号的前缀。

```bash
$ npm version prerelease --pre-id rc
```

上面的命令会产生诸如`1.0.0-rc.0`的版本号。

## 参考链接

- [npm version 常用命令及用法示例](https://blog.csdn.net/weixin_40817115/article/details/90384398)

