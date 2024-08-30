# 截止目前所有的上下文无关文法
```
Expr -> Assign
Assign -> Add Assign'
Assign' -> = Add Assign' | ε
Add   -> Term Expr'
Add'  -> + Term Expr' | - Term Expr' | ε
Term   -> Factor Term'
Term'  -> * Factor Term' | / Factor Term' | ε
Factor -> ( Expr ) | Int
ExprList -> Expr ExprList' | ε
ExprList' -> , Expr ExprList' | ε
FunctionCall -> @ Id ( ExprList )
Sentence -> Expr ; | If | Else
Sentences -> Sentence Sentences | Sentence
Statements -> { Sentences } | Sentence

DefineFuction -> fn Type Id ( ExprList ) Statements
DefineVariable -> Type Id ;

If -> if ( Expr ) Statements
Else -> else Statements
While -> while ( Expr ) Statements
DoWhile -> do Statements while ( Expr ) ;
For -> for (Expr ; Expr ; Expr ) Statements

Break -> break ;
Continue -> continue ;
Return -> return Expr ;
```