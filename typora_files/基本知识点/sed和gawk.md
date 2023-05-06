-i参数为直接替换，不加参数为展示不修改原文件。

`g`代表每行出现的字符全部替换

格式：sed -i “s/原字符串/新字符串/g” 文件名。

在行首添加字符，比如`HEAD`

```bash
sed 's/^/HEAD&/g' test.file
```

在每行的行尾添加字符，比如`TAIL`

```bash
sed 's/$/&TAIL/g' test.file
```

行首和行尾：

```bash
sed '/./{s/^/HEAD&/;s/$/&TAIL/}' test.file
```