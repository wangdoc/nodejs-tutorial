# npm init 命令

`npm init`命令的作用，是生成`package.json`文件。它的别名是`create`。

新建一个目录，作为模块的开发目录。进入该目录，执行`npm init`，屏幕上会依次出现一些问题，要求用户回答。用户回答以后，就会生成`package.json`文件。

```bash
$ npm init
```

如果觉得回答问题太麻烦，想使用`package.json`的默认值，那就使用`--yes`或`-y`参数。

```bash
$ npm init --yes
# 或者
$ npm init -y
```

如果想设置`package.json`的一些默认值（作者、Email、许可证），需要提前用`npm set`命令设置。

```bash
$ npm set init-author-email "example-user@example.com"
$ npm set init-author-name "example_user"
$ npm set init-license "MIT"
```

`npm init`也可以格式化创建项目。

```bash
$ npm init foo
# 等同于
$ npm exec create-foo
```

上面的`npm init foo`这条命令，会去执行`create-foo`这个模块（`package.json`的`bin`属性）。

举例来说，如果要执行`create-react-app`创建一个 React 项目，可以执行下面的命令。

```bash
$ npm init react-app ./my-react-app
```

