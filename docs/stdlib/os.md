# os 模块

`cpus`属性返回一个数组，每个成员对应一个 CPU 内核。下面代码可以获取本机的 CPU 内核数目。

```javascript
const { cpus }   = require('os');
const numWorkers = cpus().length;
```
