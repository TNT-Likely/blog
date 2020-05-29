# JS中AST的使用

## AST是什么

> 在计算机科学中，抽象语法树（Abstract Syntax Tree，AST），或简称语法树（Syntax tree），是源代码语法结构的一种抽象表示。它以树状的形式表现编程语言的语法结构，树上的每个节点都表示源代码中的一种结构。之所以说语法是“抽象”的，是因为这里的语法并不会表示出真实语法中出现的每个细节。比如，嵌套括号被隐含在树的结构中，并没有以节点的形式呈现；而类似于 if-condition-then 这样的条件跳转语句，可以使用带有三个分支的节点来表示。

![WhatisAST](WhatIsAST.png)

## AST怎么产生的

### AST生成过程

![GenerateAST](GenerateAST.png)

#### 词法分析

常见词法单元如下:

| 词法单元 | 含义 | 模式 | 举例 |
|  ----  | ---- | ----  | ---- |
| reservedWord | 保留字 | | let、const、class等 |
| idenitifier | 标识符 | 以字母、_、$开头的连续字符 | const、let、a |
| operator | 操作符 | | *、/、+、- |
| whitespace | 空白符 | | |
| number | 数字 | | |
| separator | 分隔符 | | {、}、(、)|
| ... | | |

示例词法解析程序:

![LexicanAnazylerCode](LexicalAnalyzerCode.png)


示例词法解析结果如下:

![LexicalAnazyler](LexicalAnalyzer.png)

#### 语法分析

示例语法解析程序:

![SyntaxAnazylerCode](SyntaxAnalyzerCode.png)

### 市面上的解析器

- [acorn](https://github.com/acornjs/acorn/tree/master/acorn)
- [esprima](https://github.com/jquery/esprima)
- [babel](https://github.com/babel/babel/tree/master/packages/babel-parser)
- Typescript
- eslint 

## AST的使用场景

- 代码格式化、代码高亮、代码自动补全
- elsint、jslint等代码检查
- webpack、rollup代码打包
- typescript、jsx等转化为原生代码
- 代码重复率检测

## AST实战使用

- 