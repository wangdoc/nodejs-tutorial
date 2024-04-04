# npm create 命令

npm create 是 npm init 命令的一个别名。

不过，如果执行`npm create xyz`，实际上执行的是`npx create-xyz`。它会暂时性安装`create-xyz`，然后执行它的`package.json`里面定义的脚本命令。

下面是一些例子。

- `npm create svelte@latest`
- `npm create vue@latest`
- `npm init react-app my-app`
