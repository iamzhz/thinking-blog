# 逃 Error
“逃 ERROR”，顾名思义。本页记录了一些 Abandon 的一些错误。不包含语法错误，包含好不容易 de 掉的 编译器/连接器 错误、运行时错误、逻辑错误.
## include 的顺序
在 `src/include.h`下包含了这个 project 的所有头文件，其中有标准库的，也有 Abandon 中某个部分的头文件，之后让其他的每个编译单元都 include 这个文件。如果该文件的某个顺序错了，那就会让编译器 g++ 报一些某某符号 undefined 的错.<br />我甚至为此写一个注释：
```cpp
// pay attention to the order!! (to myself)
```
## 不要在头文件中定义变量！
Once upon a time, 我在某个头文件中加入了类似以下的代码
```cpp
PointerManager pmTree;
```
然而，因为上面提过的原因，每个编译单元都 include“主头文件”，而“主头文件”会包含每个其他的头文件，所以就会产生多个定义.<br />当然，在编译阶段没报错，但链接时就会报一大堆多重定义的错：`multiple definition of pmTree` 。<br />我刚开始想到把它放到实现文件（或 源文件）中，但也不行，这样编译器就报错了.<br />Emmmm, 我又想起了一个词—— **extern** .<br />于是，解决这个问题也很简单， 在头文件的定义前加上 `extern`，把定义（Definition）变为声明（Declaration），在把定义放在实现文件中.
```cpp
extern PointerManager pmTree;
```
## 这就是空指针鸭
“我要写 parser 辣”：我用一个写了一个类 `Tree`用来存储抽象语法树（Abstract Syntax Tree, AST ），在我的 `Parser`类中，我有一些用于解析非终结符的成员函数，每个函数返回一个指向 new 过的一个树的指针，so  ,<br />我在遇到错误的语法时会返回 nullptr，而在这之后我又直接引用了这个空指针，<br />于是，make 过了，运行时系统输出了类似这样的错：
```
[1]    2841 segmentation fault (core dumped)  ./abandon hello.abn
```
并且当前目录还多了个名为`core.2841`的文件，现请教 GPT-4o 得知，
> 在Linux系统中，出现“segmentation fault (core dumped)”错误时，系统通常会生成一个核心转储文件（core dump file）。这个文件包含程序在崩溃时的内存映像和寄存器状态，帮助开发者进行调试和分析。  
具体到你的例子中：  
● [1]    2841 表示进程ID为2841的程序在后台运行。  
● segmentation fault (core dumped) 表示程序由于非法访问内存地址而崩溃，并且系统生成了一个核心转储文件。  
生成的文件 core.2841 是核心转储文件，其中 2841 对应的是崩溃的进程ID。该文件包含了程序崩溃时的内存状态、寄存器状态、堆栈内容等信息。开发者可以使用调试工具，如gdb（GNU Debugger），来分析这个核心转储文件，以确定崩溃的原因。  

（我这时使用的是一个云 space，运行的 Linux）<br />~~这明显是一个比较明显的问题，但当时这个明显的问题显得不那么明显，~~<br />就是说“不知庐山真面目，只缘身在此山中”，我当时想不到这上面，害的我开始埋怨别的函数。<br />最后才领悟“啊，我 return 了个空指针，我又引用了这个空指针”.<br />解决办法就是不用 nullptr 来表示语法错误，于是我又定义了一个全局变量来表示:
```cpp
Tree* noneTreeClass = createTree(treeTypeNode_None);
```

# 不要吞我的 token
在试写 parser 后，我的输入是 `3+7`，而输出的树是
```
Node Expr
├── Node Term
│   ├── Node Factor
│   │   └── Token Int [3]
│   └── Node ε
└── Node ε
```
其文法是
```
Expr   -> Term Expr'
Expr'  -> + Term Expr' | - Term Expr' | ε
Term   -> Factor Term'
Term'  -> * Factor Term' | / Factor Term' | ε
Factor -> ( Expr ) | Number
```
哎，我加号呢？我 7 呢？
debug 后，发现原来是“吞 token”了.  
> 按照我的规定，parser 里的函数都是先使用现成的 token，后为下一个函数获取新 token; 每个函数最后必须有且只能有一个未使用的 token  

而我的一些函数因为多读了造成了 bug (我称之为“吞 token”，不知道是否有这个名词)  
比如以下代码，为 Expr' 写的一个函数 (含有 bug 的)
``` cpp
Tree* Parser::parse_Expr_() {
    Token tk = this->lx->current;   // 获取当前 token
    this->lx->getNextToken();       // 为下一个 token 提前读
    
    Tree* tr = createTree(treeTypeNode_Expr_);  // 构建一棵 Expr' 的树 (作为返回值)
    Tree* tr_Term;
    Tree* tr_Expr_;
    if (tk.matchSign("+") || tk.matchSign("-")) {   // 当前 token 是 + 或 - 的符号
        tr->add(createTree(tk));    // 将当前 token 封装为树加入到 tr 的子结点
    } else {
        return epsilonTreeClass; // 返回 ε
    }

    tr_Term = this->parse_Term();   // 继续解析 Term
    if (tr_Term == noneTreeClass) return noneTreeClass; // 如果 Term 解析失败, 返回失败
    tr->add(tr_Term);   // 否则将 Term 的树加入到 tr 的子结点

    tr_Expr_ = this->parse_Expr_(); // 继续解析 Expr'
    if (tr_Expr_ == noneTreeClass) return noneTreeClass;    // 如果 Expr' 解析失败, 返回失败
    tr->add(tr_Expr_);  // 否则将 Expr' 的树加入到 tr 的子结点
    return tr;  // 返回解析过后的 Expr' 树
}
```
不难发现, 如果输入是 ε 的, 那么它会依次执行
``` cpp
Token tk = this->lx->current; // 未使用的第一个 token (先标记为 token1)
this->lx->getNextToken();   // 未使用的第二个 token
Tree* tr = createTree(treeTypeNode_Expr_);
return epsilonTreeClass;
/* 因为定义不会被执行，且对应该示例无用，故弃之*/
```
这样它就会一下子读了两个 token，但却都没有使用，会导致其他代码看不到 token1  
解决方案：  
将代码改为只有返回的不是 ε 时，才 `this->lx->getNextToken()`  
如下
``` cpp
Tree* Parser::parse_Expr_() {
    Token tk = this->lx->current;
    // 减去了 this->lx->getNextToken();
    
    Tree* tr = createTree(treeTypeNode_Expr_);
    Tree* tr_Term;
    Tree* tr_Expr_;
    if (tk.matchSign("+") || tk.matchSign("-")) {
        tr->add(createTree(tk));
        this->lx->getNextToken(); // 增加了
    } else {
        return epsilonTreeClass; 
    }

    tr_Term = this->parse_Term();
    if (tr_Term == noneTreeClass) return noneTreeClass;
    tr->add(tr_Term);

    tr_Expr_ = this->parse_Expr_();
    if (tr_Expr_ == noneTreeClass) return noneTreeClass;
    tr->add(tr_Expr_);
    return tr;
}
```
其实也就是把 `this->lx->getNextToken()` 换了个位置，  
其他函数也有这个 bug，解决方法亦同