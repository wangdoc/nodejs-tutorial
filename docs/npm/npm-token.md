# npm token

`npm token`命令用来管理认证令牌。

`npm token list`命令列出所有激活的认证令牌。

```bash
$ npm token list
```

上面命令的返回结果，以表格形式显示。如果加上`--json`参数，则返回 JSON 格式；加上`--parseable`参数，返回 Tab 键分隔的数据。

`npm token create`命令生成一个新的认证令牌。

```bash
$ npm token create
```

`--read-only`参数用来生成只读令牌，默认是读写令牌。`--cidr=<cidr-ranges>`参数用来指定令牌生效的 IP 地址范围。

`npm token revoke`命令收回令牌。

```bash
$ npm token revoke <token|id>
```

