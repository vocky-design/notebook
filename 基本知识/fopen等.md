FILE *fopen(const char *file_name, const char *mode);

文件使用方式由r、w、a、b、、+等6个字符拼成，其各字符的含义是：

r（read）读；

w（write）写；

a（append）添加；

b（binary）二进制文件；

t（text）文本文件，可省略；

\+ 读和写

*fopen ()*没有设定创建文件权限的参数，通过*fopen*创建的文件的权限是通过*0666 &*（*~umask*）得到的。



<io.h>

int open(char *path, int access, [, int auth]);



int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);