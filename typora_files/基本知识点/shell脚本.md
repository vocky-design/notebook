[shell 脚本基本语法_dabao的技术博客_51CTO博客](https://blog.51cto.com/u_332532/1891539)

$0
相当于C语言main函数的argv[0]
$1、$2...
这些称为位置参数（PositionalParameter），相当于C语言main函数的argv[1]、argv[2]...
$#
相当于C语言main函数的argc - 1，注意这里的#后面不表示注释
$@ $*
表示参数列表"$1" "$2"...，例如可以用在for循环中的in后面。
$?
上一条命令的Exit Status
$$

当前Shell的进程号



Shell提供了一些用于调试脚本的选项，如下所示：

-n	读一遍脚本中的命令但不执行，用于检查脚本中的语法错误

-v	一边执行脚本，一边将执行过的脚本命令打印到标准错误输出

-x	提供跟踪执行信息，将执行的每一条命令和结果依次打印出来



使用这些选项有三种方法(注意:避免几种调试选项混用)

- 1.在命令行提供参数：`sh -x script.sh` 或者 `bash -n script.sh`
- 2.脚本开头提供参数：`#!/bin/sh -x` 或者 `#!/bin/bash -x`
- 3.在脚本中用set命令启用或者禁用参数，其中`set -x`表示启用，`set +x`表示禁用



shell脚本中有如下几种变量。

- 普通的用户自定义变量，如上面的myname
- 系统预定义好的环境变量，如PATH
- 命令行变量，如 $#、$*。



[shell脚本概述 - opscool - 博客园 (cnblogs.com)](https://www.cnblogs.com/yancool/p/16503862.html)

7、清除变量

unset a



8、获取用户输入

read -p a



11、条件判断，常用的参数有：

-e　　是否存在 

-d　　是目录

-f　　是文件

-r　　可读

-w　　可写

-x　　可执行

-s　　非空文件

=　　两字符串相等

！=　　两字符串不相等

-z　　字符串为空

-n　　字符串不为空



15、case判断，格式如下

case $a in

　　"内容")

　　...

　　;;

　　"内容")

　　...

　　;;

　　*)

　　...

easc



16、for循环，例如输出1~10

for ((i=1;i<=10;i++))

do

echo $i

done



环境变量 BASH_SOURCE

BASH_SOURCE[0] 等价于 BASH_SOURCE ,取得当前执行的 shell 文件所在的路径及文件名

dirname  去除文件名中的非目录部分，仅显示与目录有关的部分

```shell
#！/bin/bash

echo "${BASH_SOURCE[0]}"

echo "${BASH_SOURCE}"

echo "$(dirname "${BASH_SOURCE[0]}")"

echo "$(cd "${ dirname BASH_SOURCE[0]}" && pwd)"
```

```
./abc/test.sh

./abc/test.sh

./abc/

/home/abc
```



函数realpath

realpath 用于获取指定目录或文件的绝对路径

- -L, --logical
  - 在软链接之前解析父目录 ..
- -P, --physical
  - 解析软链接，**默认动作**
- --relative-to=DIR
  - 相对于目录 DIR 的路径

1. 打印指定文件的绝对路径

   ```shell
   $$ realpath ./hello.tgz
   /data/test/src/hello.tgz
   ```

2. 显示软链接指向的目标文件的绝对路径

   ```shell
   $$ realpath ./hello.sln
   /data/test/src/hello.tgz
   ```

3. 打印某个文件相对于另外一个目录的路径

   ```shell
   $$ realpath --relative-to=./src ./foo
   ../foo
   ```

   

alias功能

[alias命令_Linux alias命令：给命令定义别名 (biancheng.net)](http://c.biancheng.net/linux/alias.html)

if 条件判断

[Shell if 条件判断 - 小白一生 - 博客园 (cnblogs.com)](https://www.cnblogs.com/liudianer/p/12071476.html)

`-L FILE` 如果FILE存在且是一个符号链接则为真