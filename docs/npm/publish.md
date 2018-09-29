# 发布

## 发布标签

npm 支持为版本打上标签，这叫做发布标签（dist-tag）。如果不指定发布标签，默认就是`latest`。用户下载模块时，默认安装的就是`latest`标签指向的版本。

新发布的版本，如果不希望用户默认安装，就需要自己指定标签。举例来说，某个模块的最新版本是4.6.12，但是有些用户还在使用老版本3.2.13。现在，你修正了一些老版本的 bug，发了一个新版本3.2.14。如果不指定发布标签，3.2.14的发布标签就是`latest`，因为它是最新发布的。

这导致的后果就是，用户执行下面的命令，进行默认安装时，会出现非预期的结果。

```bash
$ npm install <package>
```

执行上面命令时，用户会默认安装3.2.14，而不是4.6.12。因为`latest`标签指向3.2.14。

解决方法就是，发布3.2.14的时候，为它打上一个发布标签。这样，3.2.14就不会占用`latest`标签。

```bash
$ npm publish --tag=previous
```

执行上面的命令后，3.2.14的发布标签就是`previous`。

安装时，必须指定这个标签，才能安装到3.2.14。

```bash
$ npm install <package>@previous
# 或者
$ npm install <package> --tag previous
```

上面的命令的两种语法都可以指定标签名。由于`latest`是默认标签，所以可以省略。

```bash
$ npm install <package>
# 等同于
$ npm install <package>@latest
```

一种常见的做法是，发布下一个大版本时，指定它的发布标签为`next`。

```bash
# 发布
$ npm publish --tag=next

# 安装
$ npm publish <package>@next
```

这样的话，用户默认安装的还是主流版本，但是愿意尝鲜的用户，可以使用新版本。

等到新版本足够可靠以后，再把`latest`标签指定到新版本。

```bash
$ npm dist-tag add <package>@5.0.1 latest
```

如果希望把默认的发布标签改掉，不再是`latest`，可以写在`package.json`里面。

```bash
{
  …
  "publishConfig": {
    "tag": "next"
  }
}
```

上面的设置，可以使得发布新版本时，发布标签默认为`next`。

常用的发布标签有`stable`、`beta`、`dev`等等。

## npm dist-tag 命令

`npm dist-tag`命令用来管理发布标签。

`npm dist-tag ls`用来列出所有的发布标签。如果不指定模块名，那么默认为当前模块。

```bash
$ npm dist-tag ls [<pkg>]
```

`npm dist-tag add`用来为一个版本指定发布标签。

```bash
$ npm dist-tag add <pkg>@<version> [<tag>]
```

`npm dist-tag rm`用来移除一个发布标签。

```bash
$ npm dist-tag rm <pkg> <tag>
```

## 参考链接

- [One simple trick for JavaScript package maintainers](https://blog.greenkeeper.io/one-simple-trick-for-javascript-package-maintainers-to-avoid-breaking-their-user-s-software-and-to-6edf06dc5617), by Stephan Bönnemann
