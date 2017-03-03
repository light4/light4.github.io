# 为 git 配置一个 socks 代理


2016年 1月28日 星期四 17时28分31秒 CST

```bash
$ cat ~/.ssh/config
```

```
Host bitbucket.org
    User git
    ProxyCommand nc -x localhost:1080 %h %p
```
