# 鈹溾攢鈹€
是的，标题你没有看错，也不是你编码的问题  
Just now，我的 parse 加减乘除写好了  
## 问题描述
我在 Web 上的 MarsCode(Linux Debian) 写的了一些代码，在上面 push 到了 GitHub，此时此刻，我要把它 pull 到本地(Windows 11)  
嗯？有 Error？算了重新 clone 吧  
clone 好了，run 一下  
emmmm.. 什么东西？
```
$ ./abandon hello.abn
Input File: hello.abn 
Output File: 
Node Expr
鈹溾攢鈹€ Node Term
鈹?  
```
正确结果应该是
```
➜  Abandon git:(main) ./abandon hello.abn 
Input File: hello.abn 
Output File: 
Node Expr
├── Node Term
│   ├── Node Factor
│   │   └── Node Expr
│   │   │   ├── Node Term
│   │   │   │   ├── Node Factor
│   │   │   │   │   └── Token Int [10]
│   │   │   │   └── Node ε
│   │   │   └── Node Expr'
│   │   │   │   ├── Token Sign [-]
│   │   │   │   ├── Node Term
│   │   │   │   │   ├── Node Factor
│   │   │   │   │   │   └── Token Int [2]
│   │   │   │   │   └── Node ε
│   │   │   │   └── Node ε
│   └── Node Term'
│   │   ├── Token Sign [*]
│   │   ├── Node Factor
│   │   │   └── Token Int [4]
│   │   └── Node ε
└── Node ε
[free] 0x564e6680b5e0
[free] 0x564e6680b640
[free] 0x564e6680b6c0
[free] 0x564e6680b750
[free] 0x564e6680b7b0
[free] 0x564e6680b860
[free] 0x564e6680b8c0
[free] 0x564e6680b920
[free] 0x564e6680b9a0
[free] 0x564e6680ba90
[free] 0x564e6680bb10
[free] 0x564e6680bb70
[free] 0x564e6680bbd0
[free] 0x564e6680bc70
[free] 0x564e6680bcf0
[free] 0x564e6680bd50
[free] 0x564e6680bdb0
[free] 0x564e6680bf40
[free] 0x564e6680bfa0
[free] 0x564e6680c040
[free] 0x564e6680c0d0
```
并且，并且！我这之中的我的`hello.abn`中的文件内容完全一样  
现在(2024/8/5 19:28)还没有解决，稍等
## 解决过程
- 19:28 刚刚开始  
- 19:41 暂无头绪  
- 19:47 我一气之下把在 Windows 下的文件的 UTF-8 编码全改成 Chinese 2312，现在好了（我就知道跟编码有关  
  只是我不解我代码里全是 ASCII 中的字符，为什么会与编码...
- 吃了顿饭
- 21:05 全部又改为 UTF-8，又可以... 这就是玄学吗？  

如果我有幸让您看到了这几段字，并且您知道原因的话(不知道也没关系)...请给我发 email `iamzhz@foxmail.com`，感谢，如果没人看到的话就算了  
P.S. 我所 clone 的 commit 的 hash 值是 [`ae8546c4b3c72b075b3ac2c1675ee70ae8145557`](https://github.com/iamzhz/Abandon/tree/ae8546c4b3c72b075b3ac2c1675ee70ae8145557)  
## 8/6 更新
我才发现我好像确实用了 ASCII 外的字符 ε 和 制表符(不是指 tab)，是我没注意到  
好吧我悟了  
我我乱码改为 ε，重新 make 了  
于是
```
PS D:\zhz\v\Abandon> ./abandon hello.abn
Input File: hello.abn 
Output File:
Node Expr
锟斤拷锟斤拷锟斤拷 Node Term
锟斤拷   锟斤拷锟斤拷锟斤拷 Node Factor
锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Expr
锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Term
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Factor
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Token Int [10]
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node 蔚
锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Expr'
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Token Sign [-]
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Term
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Factor
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Token Int [2]
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node 蔚
锟斤拷   锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node 蔚
锟斤拷   锟斤拷锟斤拷锟斤拷 Node Term'
锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Token Sign [*]
锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node Factor
锟斤拷   锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Token Int [4]
锟斤拷   锟斤拷   锟斤拷锟斤拷锟斤拷 Node 蔚
锟斤拷锟斤拷锟斤拷 Node 蔚
```
这就是传说中的 锟斤拷 吗？ 
## 但是我想用 UTF-8 编码啊？！
搜索“Windows gcc utf-8”，emmmmmm，只要在编译时加上 `-finput-charset=UTF-8 -fexec-charset=GBK`，就可以啦。于是我的 makefile 变成这样了 (不过我不会把它 push 到 Github 上)
``` makefile
cc := g++
std := -std=c++11
abandon: src/*.cc src/*/*.cc
	$(cc) $^ -o $@ $(std) -g -finput-charset=UTF-8 -fexec-charset=GBK
```
完美