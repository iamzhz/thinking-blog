# 修改文法也很有用的吧！
```
Sentences -> Sentence Sentences | Sentence
Statements -> { Sentences } | Sentence

ExprList -> Expr , ExprList' | ε
ExprList' -> , Expr ExprList' | ε
```
```
Sentences -> Sentence Sentences | Sentence |  }
Statements -> { Sentences | Sentence

ExprList -> Expr ExprList'
ExprList' -> , Expr ExprList' | ε
```