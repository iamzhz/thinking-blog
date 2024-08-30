# 文法？文法！
## 前情提要 (可以跳过)
TBH，我在未写语法分析时，认为：
``` 
文法？有必要吗？我觉得我到时候写 parser 的时候能够应该能顺着自己的心写出来，
我还需要它？
(真实心理)
```
于是，  
我写 parser 的之前，思考了一下实现方法，  
于是，  
我只能又捧读龙书，重视起文法了.  
## 上下文无关文法
- **上下文无光文法**（Context-Free Grammar，CFG），以下简称为**文法**.  
- **终结符** (Non-terminal) 语法树中的最某端的节点，代表一个 token.  
- **非终结符** (Terminal) 不是语法树中的最某端的节点的节点，代表一群树状的 token(s).  

文法通常表示为 \\(A\to \alpha\\), 左侧部分 \\(A\\) 称为 非终结符，右侧的 \\(\alpha\\) 被称为**生产式**(Production) 或 **右部** (Right-hand side, RHS)  
- **生产式** 是由 终结符 和/或 非终结符 组成的符号序列：也就是一堆由 终结符 和(或) 非终结符 堆成的东西. 生产式 表示了左侧非终结符.  
  
以下是处理加减乘除及括号运算的文法(式1)
```
Expr   -> Expr + Term | Expr - Term | Term
Term   -> Term * Factor | Term / Factor | Factor
Factor -> ( Expr ) | Number
```
其中，`|` 用于分割相同非终结符中可能的不同的生产式，可以理解为 “或”.  
虽然但是，式1包含**左递归**.  
假如用此式**最左推导**(Leftmost Derivation)某些终结符符号(如`1+2`)，它会陷入 Expr 的无穷循环. 
  
因此放入到代码环境需要改为无左递归版本，如下(式2)
```
Expr   -> Term Expr'
Expr'  -> + Term Expr' | - Term Expr' | ε
Term   -> Factor Term'
Term'  -> * Factor Term' | / Factor Term' | ε
Factor -> ( Expr ) | Number
```
其中，`ε` 为空集，也就是啥都不需要，  
像这样把它变为**右递归**的做法，叫做**消除左递归**