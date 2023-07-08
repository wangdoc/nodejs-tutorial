# npm tag

npm 允许为版本添加标签，方便用户安装特定版本。只要指定标签，就可以安装该标签下的最新版本。

发布模块时，如果不指定 tag，默认使用`latest`。安装时也是如此，不指定版本时，npm  默认安装`latest`标签对应的最新版本。

如果指定 tag，发布时会将该 tag 指向最新发布的版本。用户如果想安装该版本，需要在安装时指定 tag。

```bash
$ npm publish --tag beta
```

上面示例中，新发布版本的标签是`beta`。

下面的命令为已发布的版本指定标签。注意，这一步之前不需要先移除已经存在的标签。

```bash
# 语法
$ npm dist-tag add [PackageName]@[Version] [Tag]

# 例子
$ npm dist-tag add foo-package@0.0.0 latest
```

安装时指定标签。

```bash
$ npm install <package>@<tag>
# 或者
$ npm install --tag <tag>
```

下面的命令查看所有 tag 和对应的版本。

```bash
$ npm dist-tag ls
# 或者
$ npm view [模块名] dist-tags
```

删除已经发布的标签。

```bash
$ npm dist-tag rm [PackageName] [Tag]
```

