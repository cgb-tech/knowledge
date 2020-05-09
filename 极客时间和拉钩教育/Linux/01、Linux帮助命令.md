# Linux

```shell
man 1 ls
# 区分内部还是外部命令
type cd 
help cd
ls --help

info ls
```

## ls

```shell
ls -l 长格式显示文件
ls -a 显示隐藏文件
ls -r 逆序显示 文件名
ls -t 按照时间顺序显示
ls -R 递归显示
ls -h 以M的大小显示
ls -d 查看单独的文件
```

## su

```shell
切换用户
su - root
```

## mkdir

```shell
mkdir a b c
mkdir -p a/b/c/d
rmdir a
```

## cp

```shell
# 复制目录 + r
cp -r /a /b 
cp -v 显示复制过程
cp -p 保留原有的时间
cp -a 保留全部
```

## touch

```shell
touch /file
```

## 通配符

```shell
* 匹配多个字符
？ 匹配一个字符
[xyz] 匹配xyz任意一个字符
[a-z] 匹配一个范围
[!xyz] 或 [^xyz] 不匹配
```

## `tail`

> **head -5  /name**
>
> **查看文件结尾**
>
> `tail -f ` 显示信息同步更新

## wc

```she
more
less
```

## 压缩

```shell
# 打包
tar cf /tmp/etc-back.tar /etc
tar czf /tmp/etc-back.tar.gz /etc 用gzip进行压缩 z 这个快，但是压缩的比例小
tar cjf /tmp/etc-back.tar.gz.bz2 /etc 用bzip2进行压缩 j 慢，压缩比例大
```

## 解包

```shell
tar -zxvf
c 打包
x 解包
f 指定操作类型为文件
-C 指定存放目录
```

## vi

> **四种模式：**
>
> 1. **正常模式(Normal-mode)**
> 2. **插入模式(Insert-mode)**
> 3. **命令模式(Command-mode)**
> 4. **可视模式(Visual-mode)**

## vim

```shell
# 进入编辑模式
i 当前光标
I 当前光标最后一个
a 当前光标下一个
A 当前光标最前面
o 下一行
O 上一行
# 方向键
hjkl 
# 复制
yy 复制一行
y$ 复制从当前光标到结尾
2 yy 复制两行
dd 剪切一整行
d$ 剪切当前到结尾
# 撤销
u
# 重做
ctrl r
# 删除单个字符
x
# 替换
r
# 显示当前所在的行
:set nu
# 不显示当前行号
:set nonu 
# 移动到指定行
数字 shift G
g 到第一行
G 最后一行
^ 一行的开头
$ 一行的结尾
```

```shell
# 命令模式
:w /root/a.txt  	保存到指定目录
:! 					执行linux的命令
/ 					查找字符 `n` 下一个匹配的字符 `shift n` 上一个匹配的字符
:set nohlsearch 	去掉高亮显示
:s/old/new 			光标所在行替换字符
:%s/old/new 		全文查找替换（一个）
:%s/old/new/g 		全部替换
:3,5s/old/new/g 	3-5行全部替换
```

```shell
# 可视模式
v 					进入可视模式
ctrl v				块可视模式
shift v				行可视模式
shift i 			插入 连按两次 esc
```

> **vim /ect/vimrc** 修改vim的配置文件
>
> G
>
> set nu

## echo

```shell
# 写入（替换）内容
echo 123 > a.txt
```

