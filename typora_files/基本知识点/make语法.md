$@		要生成的目标

$^		 全部的依赖文件

$<		 第一个依赖文件



赋值方式：

- 递归赋值 	   =
- 未定义赋值    ？=
- 直接赋值       ：=
- 追加赋值       +=



$(filter PATTERN…,TEXT)

- 返回TEXT中符合PATTERN的单词。

- 支持多个模式，用空格分隔。

- 模式中一般需要包含模式字符"%"。

- 示例：

  ```shell
  sources := foo.c bar.c baz.s ugh.h
  foo: $(sources)
  cc $(filter %.c %.s,$(sources)) -o foo
  ```

$(wildcard 通配符)

- 在Makefile规则中，通配符会被自动展开。<u>但在变量的定义和函数引用时，通配符将失效</u>。这种情况下如果需要通配符有效，就需要使用函数“wildcard”。
- 一般我们可以使用“$(wildcard *.c)”来获取工作目录下的所有的.c文件列表。
- 复杂一些用法；可以使用“(patsubst((wildcard *.c))”，首先使用“wildcard”函数获取工作目录下的.c文件列表；之后将列表中所有文件名的后缀.c替换为.o



origin函数

作用是告诉你变量是哪里来的

- 当从来未定义过该变量时，origin 函数返回 "undefined" 。
- 如果该变量为环境变量，那么返回 "enviroment" 。
- 如果变量是个默认定义，那么返回 "default"。
- 如果一个变量被定义在 Makefile 文件中，那么返回 "file" 。
- 如果变量来自命令行，那么返回 "command line" 。
- 如果变量被 override 被重新定义过，那么返回 "override"。



words函数

统计单词数目函数

```shell
$$ $(words, foo bar)	#2
$$ $(word $(words,TEXT), TEXT)	#字符串"TEXT"的最后一个单词, 也可以直接使用lastword函数
```



dir函数

$(dir NAMES…)  
函数功能：从文件名序列“NAMES…”中取出各个文件名的目录部分。文件名的目录部分就是包含在文件名中的最后一个斜线（ “/”） （包括斜线）之前的部分。 返回值：空格分割的文件名序列“NAMES…”中每一个文件的目录部分。 
函数说明：如果文件名中没有斜线，认为此文件为当前目录（“./” ）下的文件。 

```shell
$$ $(dir src/foo.c hacks) 
src/ ./
```



filter函数

过滤函数—filter

过滤掉字串“TEXT”中所有不符合模式“PATTERN”的单词，保留所有符合此模式的单词。可以使用多个模式。模式中一般需要包含模式字符“%”。存在多个模式时，模式表达式之间使用空格分割。

```shell
$$ sources := foo.c bar.c baz.s ugh.h 
$$ foo: $(sources)
$$ cc $(filter %.c %.s,$(sources)) -o foo
```



MAKEFLAGS

[关于MFLAGS与MAKEFLAGS - 言止予思 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lisuyun/p/4169249.html)



MAKEFILE_LIST

[(21条消息) Makefile 变量MAKEFILE_LIST_不闻窗外事的博客-CSDN博客](https://blog.csdn.net/qu1993/article/details/89020034)
