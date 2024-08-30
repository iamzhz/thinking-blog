# 小结：parse 加减乘除
截止现在(2024/8/5 14:18), Abandon 已经能够将任意的加减乘除解析为 CST 了  
比如输入`(10-2)*4`, 能够解析出树:
```
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
```