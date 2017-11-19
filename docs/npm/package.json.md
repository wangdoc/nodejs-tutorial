# package.json

## files 字段

`files`字段是一个数组，里面指定了一组文件。当模块发布到 NPM 网站时，这组文件会被包括。这个字段是可选的，如果没有指定内容，那么发布时所有文件都会被包括在内。如果`files`字段包含目录名，该目录里面的所有文件都会被计入。

```javascript
{
  "name": "@adam_baldwin/wombats",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "files": [
   "index.js"
  ],
  ...
}
```

`npm`不会发布`.gitignore`里面列出的文件和目录。项目的根目录或子目录里面，还可以放置一个`.npmignore`文件，该文件会覆盖`.gitignore`，里面指定的文件和目录不会被发布。

项目的根目录下，`files`字段优先级最高；子目录下，`.npmignore`优先。`files`字段指定的文件，不会被`.npmignore`或`.gitignore`排除。

以下文件，发布的时候总是会包含。

- package.json
- README
- CHANGES / CHANGELOG / HISTORY
- LICENSE / LICENCE
- NOTICE
- `main`字段里面的文件

`README`、`CHANGES`、`LICENSE`和`NOTICE`这四个文件名，可以采取任意的大小写组合。

以下文件，发布的时候总是会被排除。

- .git
- CVS
- .svn
- .hg
- .lock-wscript
- .wafpickle-N
- `.*.swp`
- .DS_Store
- `._*`
- npm-debug.log
- .npmrc
- node_modules
- config.gypi
- `*.orig`
- package-lock.json

基本上，npm 会发布`files`字段指定的文件和目录，以及那些总是会包含在内的文件（比如`package.json`），然后再除去那些被其他规则排除的文件和目录。

[npm-packlist](https://github.com/npm/npm-packlist) 模块会列出所有将要打包发布的文件和模块。`npm pack`命令则会将那些将要发布的内容打成一个`tgz`压缩包，放在项目的根目录下。
