### strncat

```c
char *strncat(char *dest, const char *src, size_t n)
```

### strncpy

```c
char *strncpy(char *dest, const char *src, size_t n)
```

- 把 **src** 所指向的字符串复制到 **dest**，最多复制 **n** 个字符。当 src 的长度小于 n 时，dest 的剩余部分将用空字节填充。
- strncpy 没有自动加上终止符的。

### snprintf

```c
int snprintf ( char * str, size_t size, const char * format, ... );
```

- snprintf() 函数的返回值是输出到 str 缓冲区中的字符数，不包括字符串结尾的空字符 \0。
- 如果 snprintf() 输出的字符数超过了 size 参数指定的缓冲区大小，则输出的结果会被截断，只有 size - 1 个字符被写入缓冲区，最后一个字符为字符串结尾的空字符 \0。

