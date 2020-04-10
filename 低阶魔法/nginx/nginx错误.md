## 以下是针对nginx发生错误的处理方案（将会持续更新）

### 遇到 `nginx: [error] invalid PID number "" in "/var/run/nginx.pid"`

```nginx
在重启云主机（系统）之后，执行 nginx -t 是OK的，然而在执行 nginx -s reload 的时候报错

nginx: [error] invalid PID number "" in "/var/run/nginx.pid"

需要先执行

nginx -c /etc/nginx/nginx.conf

nginx.conf文件的路径可以从nginx -t的返回中找到。

nginx -s reload
```

借鉴博客 ： [乡村猫](https://www.cnblogs.com/yuqianwen/p/4285686.html)

