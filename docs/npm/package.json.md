# package.json

## files 字段

`files`字段是一个数组，里面指定了一组文件。当模块发布到 NPM 网站时，这组文件会被包括。这个字段是可选的，如果没有指定内容，那么发布时所有文件都会被包括在内。如果`files`字段包含目录名，该目录里面的所有文件都会被计入。

项目的根目录或子目录里面，可以放置一个`.npmignore`文件，该文件里面指定的文件不会被发布。项目的根目录下，`files`字段优先；子目录下，该文件优先。如果只有`.gitignore`文件，但是没有`.npmignore`，那么`.gitignore`会被当作`.npmignore`。

`files`字段指定的文件，不会被`.npmignore`或`.gitignore`排除。

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
- .*.swp
- .DS_Store
- ._*
- npm-debug.log
- .npmrc
- node_modules
- config.gypi
- *.orig
- package-lock.json
