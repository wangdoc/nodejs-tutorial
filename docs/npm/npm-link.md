# npm link 命令

有时候，我们在本地修改了一些模块，想先测试这些修改是否有效。那么，怎么才能让依赖于该模块的应用，能够加载这些本地模块呢？

一种方法是使用`npm install`的`--save`参数。

```bash
$ npm install --no-save <模块的本地路径h>
```

上面的命令会从本地目录安装指定模块，但是不写入`package.json`。这样就可以让应用加载本地模块。

另一种则是使用`npm link`命令，在`node_modules`目录里面建立一个符号链接，链接到本地模块。

它分成两步，第一步先在本地模块的目录里，执行`npm link`。

```bash
$ npm link
```

第二步是到你的应用目录，执行`npm link <本地模块名>`，在`node_modules`目录里面产生本地模块的符号链接。

```bash
$ npm link <本地模块名>
```

也可以将上面两步合二为一，在应用目录里面，直接链接本地模块的路径。

```bash
$ npm link <本地模块的路径>
```

等到测试完毕，再用`npm unlink`命令，先在应用目录删除符号链接。

```bash
$ npm unlink --no-save <本地模块名>
```

再到本地模块的目录里面，执行`npm unlink`。

```bash
$ npm unlink
```

