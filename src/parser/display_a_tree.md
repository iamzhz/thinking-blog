# 打印一棵树
## 介绍一哈
我的树大概长这样
``` cpp
class Tree {
    treeType type;
    Token tk;
    treeTypeNodeLabel label;
    std::vector<Tree*> children;
};
```
然后有一堆枚举
``` cpp
enum treeType {
    treeType_End,
    treeType_Token,
    treeType_Node
};

enum treeTypeNodeLabel {
    treeTypeNode_Main,
    treeTypeNode_None,
    treeTypeNode_Epsilon,
    treeTypeNode_Expr,
    treeTypeNode_Expr_,
    treeTypeNode_Term,
    treeTypeNode_Term_,
    treeTypeNode_Factor,
};
```
在树中，根据 `label` 判断是一个是否是树中的叶子节点, `type` 是表示非子节点的类型, `children` 是表示非子节点的子节点(们), `tk` 表示 Token, 是一个叶子结点的值.  
我的这个 Tree 一般存储堆中, 使用 `createTree()` 函数(们)创建.  
在用这颗树的时候, 先判断 `label`, 然后在使用 [`type` 与 `children`] 或 [`tk`].
## 打印一棵树
因此可以这样：
``` cpp
void Tree::display(int indent, bool last) {
    for (int i = 0; i < indent - 1; ++i) { // 打印缩进
        std::cout << (i < indent - 1 ? "│   " : "    ");
    }

    if (indent > 0) { // 打印树枝
        std::cout << (last ? "└── " : "├── ");
    }

    if (this->type == treeType_Token) { // 打印 Token
        std::cout << "Token " << this->tk.typeToText()
                  << " [" << this->tk.content << ']' << std::endl;  // 打印 Token 类型及内容
    } else { // 打印非 Token 节点(Node)
        std::cout << "Node " << treeTypeNodeLabelToText(this->label) << std::endl; // 打印节点类型

        for (size_t i = 0; i < this->children.size(); ++i) { // 打印子节点(们)
            this->children[i]->display(indent + 1, i == this->children.size() - 1); // 递归打印
        }
    }
}
```
完毕！