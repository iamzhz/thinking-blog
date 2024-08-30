# 怎么会跳到这里面？
在我往 parser 中加 if else 后，也修改了对 Sentence 的解析，但是，会出现这种问题
Abandon 代码是这样的（可能与写到最后的 Abandon 代码有出入，但是理想中现在可以解析的代码）
``` c
fn int main(void) {
    if (x) {
        @print("It is true"); // 在这里的分号处报错
    } else {
       @print("It is not true");
    }
    @hello("hello");
    x = 1 + 2;
}
```
而执行的命令后是这样的：
``` c
PS D:\zhz\v\Abandon> ./abandon hello.abn
Input File: hello.abn 
Output File:
[Error] 3,29 ( or something other expected
[free] 69 pointer(s)
```
因为我修改了 parse_Sentence 函数，所以应该这应该与该函数有关，通过 gdb 对该函数打断点，发现了端倪   
每次到这里判断都会跳进那个如果是关键字且是 if 的判断里去：  
``` cpp
Tree* Parser::parse_Sentence() {
    Tree* tr = createTree(treeTypeNode_Sentence);
    Tree* tr_Expr;

    if (this->current.matchKeyword("if")) { // 通过测试，每次到这里判断都会跳进这个if判断
        Tree* tr_If = this->parse_If();
        ERROR_noneTreeClass(If);
        tr->add(tr_If);
        return tr;
    } else if (this->current.matchKeyword("else")) {
        Tree* tr_Else = this->parse_Else();
        ERROR_noneTreeClass(Else);
        tr->add(tr_Else);
        return tr;
    }
        
    tr_Expr = this->parse_Expr();
    ERROR_noneTreeClass(Expr);
    tr->add(tr_Expr);

    if (!this->current.matchSign(";")) {
        EXPECTED_ERROR(";");
    }
    tr->add(createTree(this->current));
    this->getNextToken();

    return tr;
}
```
所以我怀疑与 `Token::matchKeyword` 有关。那就看一下：
``` cpp
bool Token::matchKeyword(std::string content) {
    if (this->type == tokenTypeKeyword) {
        if (this->content == content) return true;
    }
    return true;
}
```
好吧。。。。我也不知道什么时候写了这几行 bad code。。哎，看看我的 git 提交，emm，Jul 1，两个月前, 现在是 2024/8/26    
正如我现在正在写的 markdown 文件名 - `all_true.md`，你应该看到的是 `all_true.html` 或者是什么的  

完毕