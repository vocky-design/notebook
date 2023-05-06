exit是一个**库函数**，在进程正常结束时调用。

功能：关闭所有文件，终止正在执行的进程。

```c
#include <stdlib.h>
#define EXIT_FAILURE 1
#define EXIT_SUCCESS 0
void exit(int status);
```

_exit是一个**系统调用**，用来终止一个进程。

#include <unistd.h>

void _exit(int status);

exit与_exit都是用来正常退出进程，二者区别在于：

- exit库函数会先检查进程打开文件的状况，并且负责把文件缓冲区中的内容写回到文件中；
- 而_exit系统调用则不会，所以调用\_exit系统调用退出进程有可能会造成文件内容丢失。