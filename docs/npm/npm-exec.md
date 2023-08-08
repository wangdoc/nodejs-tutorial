# npm exec 命令

`npm exec`用来执行某个 npm 模块的内部命令，不管该模块在本地还是在远程。它有一个别名`x`，即`npm exec`等同于`npm x`。

该命令与`npx`命令的作用类似，但是使用上有所不同。

`npx`会将所有参数原样传入模块内部。

```bash
$ npx foo@latest bar --param=@npmcli/foo
# 等同于
$ foo bar --param=@npmcli/foo
```

`npm exec`则需要使用`--`分隔符，指定所要执行的命令和它的参数。

```bash
$ npm exec -- foo@latest bar --param=@npmcli/foo
# 等同于
$ foo bar --param=@npmcli/foo
```

`npm exec`也可以用`--package`参数指定模块。

```bash
$ npm exec --package=foo -- bar --bar-argument
# 等同于
$ npx --package=foo bar --bar-argument
```

`--call`或`-c`参数用来指定执行的整个命令。

```bash
$ npm exec -c 'eslint && say "hooray, lint passed"'
# 等同于
$ npx -c 'eslint && say "hooray, lint passed"'
```

