# CST 与 AST
原来树与树是不同的？我还以为所有的都叫 AST 抽象语法树
## CST
**CST**（Concrete Syntax Tree，具体语法树）  
CST 是对输入源代码的一种更接近原始语法结构的直接表示，它保留了更多的语法细节，包括可能的冗余和不必要的信息  
像这样
```
Node Expr
├── Node Term
│   ├── Node Factor
│   │   └── Node Expr
│   │       ├── Node Term
│   │       │   ├── Node Factor
│   │       │   │   └── Token Int [10]
│   │       │   └── Node ε
│   │       └── Node Expr'
│   │           ├── Token Sign [-]
│   │           ├── Node Term
│   │           │   ├── Node Factor
│   │           │   │   └── Token Int [2]
│   │           │   └── Node ε
│   │           └── Node ε
│   └── Node Term'
│       ├── Token Sign [*]
│       ├── Node Factor
│       │   └── Token Int [4]
│       └── Node ε
└── Node ε
```
# AST
**AST** （Abstract Syntax Tree，抽象语法树）  
也就是把 CST 去掉没用的 ε 与只包含一个子节点的节点，但保留子节点  
像这样  
```
Node Expr
├── Node Factor
│   ├── token Int [10]
│   └── Node Expr'
│       ├── Token Sign [-]
│       └── Token Int [2]
└── Node Term'
    ├── Token Sign [*]
    └── Token Int [4]
```
这么样？看着是不是清净很多？
## Thinking
CST 可以直接生成的，也可以直接生成 AST，不过可能会没直接生成 CST 简单. 我生成的就是 CST, display 出来的树比较难看, 所以, 我还想往 Tree 类再加一个成员函数, 起名为 `cst2ast()`, 它在 CST 生成之后执行, 通过遍历一棵树将使得其变得更抽象, 应该也不算难
## 半晌后更新
已经写成了, 输出:
```
Node Term
├── Node Expr
│   ├── Token Int [10]
│   └── Node Expr'
│   │   ├── Token Sign [-]
│   │   └── Token Int [2]
└── Node Term'
│   ├── Token Sign [*]
│   └── Token Int [4]
```
code:
``` cpp
Tree* Parser::cst2ast(Tree* tr) {
    if (tr->type == treeType_Token) return tr;

    std::vector<Tree*> & c = tr->children; // 酱紫写起来比较好看
    for (auto it = c.begin();  it != c.end();  /*这里是空的*/) {
        if ((*it)->type == treeType_Node && (*it)->label == treeTypeNode_Epsilon) { // 如果是空集
            it = c.erase(it); // “擦”去它
        } else { // 否则 (废话)
            Tree* result = cst2ast(*it); // 将一个子节点扔给另一个自己处理
            if (result == nullptr) it = c.erase(it); // 如果其返回 nullptr, 则擦去它
            else { // 否则
                *it = result; // 使用它
                it ++; // 调到下一个迭代器
            }
        }
    }
    if (c.empty()) return nullptr; // 如果是空的,直接返回 nullptr (算是标记吧)
    if (c.size() == 1) return c[0]; // 如果只要一个子节点,就单独返回它,不包括自己
    return tr; // 最后剩下的一个完美的 Tree
}
```
注意: 在循环中, 如果是空集(treeTypeNode_Epsilon), 并且 `it = c.erase(it)`, 那么就不需要`it ++`了, 所以 for 循环的第三个表达式是空的, 之后根据情况再在循环体内写  
它的结果与我自己动手做成的完全一样, 不过只有那些线不一样, 这就该埋怨我的 Tree::display() 了, 详见[打印一棵树](display_a_tree.md)  
## 两天后
其实这样输出的也并不是十分的 AST, 因为撇来撇去的，所以我想再加上一些语句把一些撇去掉，只保留...那叫什么东西  
反正就是，原来是这样的
```
Node Expr
├── Token Int [1]
└── Node Expr'
│   ├── Token Sign [+]
│   └── Token Int [3]
```
操作之后就变成
```
Node Expr
├── Token Int [1]
├── Token Sign [+]
└── Token Int [3]
```
我想你懂我的意思。感觉也简单，就是在 `return tr;` 前加上一些判断的代码, 再做一些什么操作，不过要有一个函数判断是不是带撇的  
稍等   
不过，突然感觉没那个必要了，刚写的代码放这里纪念一下(说不定中间代码生成时有用)
``` cpp
bool treeTypeNodeLabelIs_(treeTypeNodeLabel label) {
    switch(label) {
        case treeTypeNode_Main: return false;
        case treeTypeNode_None: return false;
        case treeTypeNode_Epsilon: return false;
        case treeTypeNode_Expr: return false;
        case treeTypeNode_Expr_: return true;
        case treeTypeNode_Term: return false;
        case treeTypeNode_Term_: return true;
        case treeTypeNode_Factor: return false;
    }
    return false;
}
bool treeTypeNodeLabelTogether(treeTypeNodeLabel l1, treeTypeNodeLabel l2) {
    if (l1 == treeTypeNode_Expr && l2 == treeTypeNode_Expr_) return true;
    if (l1 == treeTypeNode_Term && l2 == treeTypeNode_Term_) return true;
    return false;
}
```