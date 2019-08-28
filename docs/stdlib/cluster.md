# cluster 模块

`cluster`模块用于组建 Node.js 应用的集群。

`cluster.isMaster`属性表示当前进程是否为主进程。

```javascript
const cluster  = require('cluster');
const isMaster = cluster.isMaster;
```

`cluster.fork()`方法复制当前进程。

```javascript
const cluster    = require('cluster');
const { cpus }   = require('os');
const numWorkers = cpus().length;
const isMaster   = cluster.isMaster;

if (isMaster) {
  process.stdout.write('master process');
  const workers = [];
  for(let i = 0; i < numWorkers; i++) {
    workers.push(cluster.fork());
  }
} else {
  process.stdout.write('worker process');
}
```

上面代码按照 CPU 内核的数目新建 Worker 进程。

主线程可以负责监听整个集群的运行情况。我们使用`cluster.on()`方法监听集群的事件。`online`事件在 Worker 进程上线时触发，`exit`事件在 Worker 进程下线时触发。

```javascript
if (isMaster) {
  log(`Forking ${numWorkers} workers`);
  const workers = [];
  for(let i = 0; i < numWorkers; i++) {
    workers.push(cluster.fork());
  }

  cluster.on('online', (worker) => log(`Worker ${worker.process.pid} is online`));
  cluster.on('exit', (worker, exitCode) => {
    log(`Worker ${worker.process.id} exited with code ${exitCode}`);
    log(`Starting a new worker`);
    cluster.fork();
  })

}
```

上面代码中，主进程如果发现一个 Worker 进程下线，就再新建一个 Worker 进程。

至于每个 Worker 进程可以指定运行的任务。

```javascript
if (isMaster) {
  // ...
} else {
  // 应用的内容
}
```

## 参考链接

- [How to scale your Node.js server using clustering](https://www.jsmonday.dev/articles/24/how-to-scale-your-node-js-server-using-clustering), Michele Riva
