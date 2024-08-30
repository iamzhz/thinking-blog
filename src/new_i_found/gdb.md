# GDB 这么好用？

> 以前，我只有一个时候使用 gdb，那就是-查看程序的汇编代码的时候  
我确实是大材小用了，

我以前调试程序都是直接在代码中 print 变量的值，再简单点就是加一个宏，比如  
``` cpp
#define DEBUG(info) std::cout << "[INFO] " << info << std::endl;
```
这时候我的宏的参数都是不加括号的，因为可以这样玩 `DEBUG("Point 1: " << var)`  
在写这个 project 的时候我才真正使用 gdb 调试程序

# GDB

1. 编译源程序时加上 `-g` 参数，生成调试信息  
2. 运行 `gdb your_program` 命令，进入 gdb 调试环境  
   这里的 `your_program` 是你编译后的可执行文件  
3. 输入 `break` 命令，设置断点，程序运行到这里会自动暂停
   比如在 启动函数 `func` 处设置断点，输入 `break func`  
   这里的 `break` 可以简写成 `b`
4. 输入 `run` 命令运行程序，程序会在 gdb 调试环境中运行  
   这里的 `run` 可以简写成 `r`
5. 输入 `next` 命令，单步执行程序，遇到断点会自动暂停 
   这里的 `next` 可以简写成 `n`
6. 输入 `continue` 命令，继续运行程序，遇到断点会自动暂停  
   这里的 `continue` 可以简写成 `c`
7. 输入 `print` 命令，查看变量的值  
   这里的 `print` 可以简写成 `p`
8. 输入 `backtrace` 命令，查看函数调用栈  
   这里的 `backtrace` 可以简写成 `bt`
9. 输入 `quit` 命令，退出 gdb 调试环境  
   这里的 `quit` 可以简写成 `q`

