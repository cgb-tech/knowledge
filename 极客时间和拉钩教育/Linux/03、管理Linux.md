# 管理Linux

## 一、网络管理

> - 网络状态查看
> - 网络配置
> - 路由命令
> - 网络故障排除
> - 网络服务管理
> - 常用网络配置文件

### 1、网络状态查看工具

> 1. **`net-tools`**
>    - **ifconfig**
>    - **route**
>    - **netstat**
> 2. `iproute2`
>    - **ip**
>    - **ss**

### 2、网络配置

> - 网卡命名规则受 `biosdevname`和`net.ifnames`两个参数影响
>
> - 编辑 `/etc/default/grup`文件，增加`biosdevname=0` `net.ifnames=0`
>
> - 更新 `grup`
>
>   - grub2-mkconfig -o / boot/ grub2/grub.cfg
>
> - 重启
>
>   - reboot
>
>   - |       | biosdevname | net.ifnames | 网卡名 |
>     | :---: | :---------: | :---------: | :----: |
>     | 默认  |      0      |      1      | ens33  |
>     | 组合1 |      1      |      0      |  em1   |
>     | 组合2 |      0      |      0      |  eth0  |
>
> - 网线连接状态
>
>   - mii-tool eth0

### 3、路由命令
```shell
ifconfig <接口> <IP地址> [netmask 子网掩码]
ifup <接口>
ifdown <接口>
route add default gw <网关ip>
route add -host <指定ip> gw <网关ip>
route add -net <指定网段> netmask <子网掩码> gw <网关ip>
```

![ip](http://codedorado.oss-cn-beijing.aliyuncs.com/images/20200506/143345558.png)

### 4、网络故障排除命令

```shell
ping
traceroute
mtr
nslookup
telnet
tcpdump
netstat
ss
```

```shell
traceroute -w 1
telnet www.baidu.com 80
# n 以ip显示，w写入一个文件
tcpdump -i any -n port 80 and host 10.0.0.1 -w /tmp/files
# t tcp方式, p 进程 ,l listen
netstat -ntpl
```

### 5、网络服务管理













































