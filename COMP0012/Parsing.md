下面我按 **COMP0012 考试会考什么、你要会怎么做题** 来讲 Parsing。你可以把 Parsing 理解成：

> Lexer 已经把代码切成 tokens，Parser 要判断这些 tokens 的结构是否合法，并生成 parse tree / AST。

例如：

```text
int x = 1 + 2 ;
```

Lexer 看到的是：

```text
INT ID = NUM + NUM ;
```

Parser 要判断它是不是符合 grammar，比如：

```text
Decl → Type ID = Expr ;
Expr → Expr + Term | Term
Term → NUM | ID
```

---

# 1. Parsing 是什么？

考试定义可以这样写：

> Parsing is the process of analysing a sequence of tokens according to the grammar of a language, usually constructing a parse tree or AST.

中文理解：

> Parsing 就是根据语法规则检查 token 序列结构是否合法。

它主要关心的是 **syntax**，不是语义。

比如：

```c
int x = "hello";
```

从 parsing 角度可能是合法的，因为结构是：

```text
type id = expression ;
```

但从 semantic analysis 看类型错了。

---

# 2. Grammar 你要掌握什么？

Grammar 通常由这些部分组成：

```text
G = (N, T, S, P)
```

|符号|含义|
|---|---|
|N|Non-terminals，非终结符|
|T|Terminals，终结符/token|
|S|Start symbol|
|P|Productions，产生式规则|

例如：

```text
E → E + T | T
T → id | num
```

其中：

```text
E, T 是 non-terminal
+, id, num 是 terminal
E 是 start symbol
```

---

# 3. Parse Tree 和 AST

## Parse Tree

Parse tree 会保留完整语法推导结构。

例如：

```text
1 + 2 * 3
```

如果 grammar 有优先级，parse tree 会体现 `*` 比 `+` 先算。

---

## AST

AST = Abstract Syntax Tree。

它比 parse tree 更简洁，只保留语义上重要的结构。

例如：

```text
1 + 2 * 3
```

AST 大概是：

```text
      +
     / \
    1   *
       / \
      2   3
```

考试里通常更关注 grammar 和 parser，不一定要求你画完整 AST，但你要知道 parsing 的输出可以是 parse tree / AST。

---

# 4. Ambiguity：语法二义性

这是 Parsing 里很核心的概念。

如果一个 grammar 对同一个字符串能产生 **两个不同 parse trees**，这个 grammar 就是 ambiguous。

例如：

```text
E → E + E | E * E | id
```

对于：

```text
id + id * id
```

可能有两种理解：

```text
(id + id) * id
```

或者：

```text
id + (id * id)
```

这就是 ambiguous。

---

## 考试怎么问？

可能给你 grammar：

```text
E → E + E | E − E | −E | (E) | a
```

问：

> Is it fit for purpose? If not, demonstrate the problem and fix it.

你要说：

1. 不 fit；
    
2. 它 ambiguous；
    
3. 没有表达 unary minus、binary plus/minus 的 precedence 和 associativity；
    
4. 要改成分层 grammar。
    

---

## 怎么修？

例如想让 unary minus 优先级最高，`+` 和 `-` 左结合，可以写：

```text
E  → E + T | E - T | T
T  → -T | F
F  → (E) | a
```

如果要给 LL(1) parser 用，还要消除左递归：

```text
E  → T E'
E' → + T E' | - T E' | ε
T  → - T | F
F  → (E) | a
```

---

# 5. Left Recursion：左递归

左递归是 LL(1) parser 的大敌。

如果一个 non-terminal 可以推出以自己开头的东西，就是 left recursion。

最直接的形式：

```text
A → A α | β
```

例如：

```text
E → E + T | T
```

这就是直接左递归。

---

## 为什么不好？

Top-down parser / LL parser 会无限递归：

```text
parse E
→ parse E
→ parse E
→ parse E
...
```

所以要消除 left recursion。

---

## 直接左递归消除公式

原来：

```text
A → Aα | β
```

改成：

```text
A  → β A'
A' → α A' | ε
```

例如：

```text
E → E + T | T
```

变成：

```text
E  → T E'
E' → + T E' | ε
```

---

## 多个 α / β 的情况

如果：

```text
A → Aα1 | Aα2 | β1 | β2
```

改成：

```text
A  → β1 A' | β2 A'
A' → α1 A' | α2 A' | ε
```

---

# 6. Indirect Left Recursion：间接左递归

有时候左递归不是一眼看到的。

例如：

```text
S → X x | y
X → S d | z
```

这里：

```text
S → X x → S d x
```

所以 S 间接左递归。

---

## 处理思路

先替换，再消除直接左递归。

例如：

```text
S → Xx | y
X → Sd | z
```

把 `X → Sd` 中的 `S` 替换成 `S` 的规则：

```text
X → Xx d | y d | z
```

现在 `X → X x d` 是直接左递归。

消除：

```text
X  → y d X' | z X'
X' → x d X' | ε
```

考试如果让你“一步一步改，并标明规则”，你就要写清楚：

```text
Substitute S productions into X
Eliminate direct left recursion on X
```

---

# 7. Left Factoring：左因子提取

如果多个产生式有相同前缀，LL parser 看一个 token 不能决定选哪条规则。

例如：

```text
A → αβ1 | αβ2
```

提取公共前缀：

```text
A  → α A'
A' → β1 | β2
```

---

## 例子

```text
S → if E then S else S | if E then S
```

有共同前缀：

```text
if E then S
```

可以改成：

```text
S  → if E then S S'
S' → else S | ε
```

---

## 考试重点

如果题目说：

> preprocess the grammar for LL(1)

通常你要做：

1. eliminate left recursion；
    
2. left factor；
    
3. sometimes remove ambiguity / encode precedence；
    
4. then compute FIRST/FOLLOW。
    

---

# 8. FIRST Set

FIRST 是 Parsing 必考。

## FIRST(X) 是什么？

> FIRST(X) 是从 X 推导出来的字符串可能以哪些 terminal 开头。

例如：

```text
A → aB | bC
```

那么：

```text
FIRST(A) = {a, b}
```

---

## 基本规则

### 规则 1：terminal

```text
FIRST(a) = {a}
```

### 规则 2：epsilon

```text
FIRST(ε) = {ε}
```

### 规则 3：non-terminal

如果：

```text
A → α | β
```

那么：

```text
FIRST(A) = FIRST(α) ∪ FIRST(β)
```

---

## 重要规则：串的 FIRST

如果：

```text
A → B C
```

先看 `B`。

- 如果 `B` 不能推出 ε，那么：
    

```text
FIRST(BC) = FIRST(B)
```

- 如果 `B` 能推出 ε，那么还要继续看 `C`：
    

```text
FIRST(BC) = FIRST(B) - {ε} ∪ FIRST(C)
```

---

## 例子

```text
S → A b
A → a | ε
```

那么：

```text
FIRST(A) = {a, ε}
```

因为 A 可以为空，所以：

```text
FIRST(S) = {a, b}
```

解释：

```text
S → A b
```

如果 A 推出 `a`，开头是 `a`；  
如果 A 推出 ε，开头就是 `b`。

---

# 9. FOLLOW Set

FOLLOW 也是必考。

## FOLLOW(A) 是什么？

> FOLLOW(A) 是在某个 sentential form 中，可能紧跟在 A 后面的 terminals。

简单说：

> A 后面可能出现什么 token。

---

## FOLLOW 基本规则

### 规则 1：start symbol

如果 S 是 start symbol：

```text
$ ∈ FOLLOW(S)
```

`$` 表示输入结束。

---

### 规则 2：A → α B β

如果 B 后面还有 β：

```text
FIRST(β) - {ε} 加入 FOLLOW(B)
```

例如：

```text
S → A b
```

那么：

```text
b ∈ FOLLOW(A)
```

---

### 规则 3：A → α B 或 β 能推出 ε

如果 B 在结尾，或者 B 后面的东西可以为空：

```text
FOLLOW(A) 加入 FOLLOW(B)
```

例如：

```text
S → A
```

那么：

```text
FOLLOW(S) 加入 FOLLOW(A)
```

因为 A 后面没有东西了，A 后面能出现的就是 S 后面能出现的。

---

## FOLLOW 例子

Grammar：

```text
S → A b | c
A → a | ε
```

Start symbol 是 S。

```text
FOLLOW(S) = {$}
```

因为：

```text
S → A b
```

A 后面是 b，所以：

```text
FOLLOW(A) = {b}
```

---

# 10. LL(1) Parsing Table

LL(1) 意思是：

```text
L：left-to-right scan
L：leftmost derivation
1：lookahead 1 token
```

它是 top-down parser。

---

## 怎么构造 LL(1) table？

对于每条 production：

```text
A → α
```

### 情况 1

如果：

```text
a ∈ FIRST(α)
```

就填：

```text
M[A, a] = A → α
```

### 情况 2

如果：

```text
ε ∈ FIRST(α)
```

那么对每个：

```text
b ∈ FOLLOW(A)
```

填：

```text
M[A, b] = A → ε
```

---

## 如果一个 cell 出现多个 production？

那 grammar 不是 LL(1)。

例如同一个格子里既要填：

```text
A → α
```

又要填：

```text
A → β
```

说明 parser 看一个 token 不知道选哪条规则。

---

# 11. LL(1) 快速判断

一个 grammar 要是 LL(1)，通常需要满足：

1. 没有 left recursion；
    
2. 已经 left factored；
    
3. 对同一个 non-terminal 的不同 production，它们的 FIRST sets 不相交；
    
4. 如果某个 production 可以推出 ε，那么它的 FOLLOW 和其他 FIRST 不能冲突。
    

考试里常问：

> Which rows are part of the LL(1) parse table?

这种题你要用 FIRST/FOLLOW 逐格判断。

---

# 12. LR(1) Parsing

LR(1) 是 bottom-up parsing。

意思是：

```text
L：left-to-right scan
R：rightmost derivation in reverse
1：one lookahead token
```

它比 LL(1) 更强，可以处理更多 grammar。

---

# 13. LR(1) Item

LR(1) item 长这样：

```text
[A → α · β, a]
```

意思是：

- 我们正在识别 production `A → αβ`；
    
- 点 `·` 表示已经识别到哪里；
    
- lookahead `a` 表示 reduce 时允许看到的下一个 token。
    

例如：

```text
[E → E + · T, $]
```

意思是：

> 已经看到 `E +`，接下来期待一个 `T`，并且这个 item 的 lookahead 是 `$`。

---

# 14. Closure

LR(1) 里 closure 是重点。

如果 item 是：

```text
[A → α · B β, a]
```

点后面是 non-terminal `B`，那么要把 B 的所有 productions 加进来：

```text
[B → · γ, b]
```

其中：

```text
b ∈ FIRST(βa)
```

---

## 通俗理解

如果 parser 当前期待一个 B，那么它也要准备好识别 B 的所有可能形式。

---

# 15. GOTO

GOTO 表示点跨过一个 symbol。

如果状态里有：

```text
[A → α · X β, a]
```

那么读入 X 后变成：

```text
[A → α X · β, a]
```

然后再做 closure。

---

# 16. LR(1) Conflict

考试会问 conflict 类型。

主要有两种：

## Shift/Reduce Conflict

同一个状态、同一个 lookahead 下：

- 既可以 shift；
    
- 又可以 reduce。
    

典型来自 dangling else 或表达式优先级不清。

---

## Reduce/Reduce Conflict

同一个状态、同一个 lookahead 下：

- 有两个不同 production 都可以 reduce。
    

例如 parser 不知道该 reduce 成 A 还是 B。

---

## Shift/Shift Conflict

一般 LR parser 中不会有真正的 shift/shift conflict，因为 shift 动作由 terminal transition 决定，通常唯一。

---

# 17. 如何判断 grammar 是否 LR(1)

考试短答可以写：

> Construct the canonical collection of LR(1) item sets using closure and goto. Then build the LR(1) parsing table. If no table entry contains more than one action, the grammar is LR(1); otherwise, conflicts show it is not LR(1).

中文理解：

1. 构造所有 LR(1) item states；
    
2. 根据 states 建 parsing table；
    
3. 如果每个格子最多一个 action，就是 LR(1)；
    
4. 如果有 shift/reduce 或 reduce/reduce conflict，就不是。
    

---

# 18. Parsing 部分最高频题型

你复习时优先练这些。

## 题型 1：解释 parsing

会写定义：

```text
Parsing analyses a sequence of tokens according to a grammar and constructs a parse tree or AST.
```

---

## 题型 2：判断 grammar 是否 ambiguous / fit for purpose

你要会说：

- 是否有 ambiguity；
    
- 是否表达 precedence；
    
- 是否表达 associativity；
    
- 如何重写 grammar。
    

---

## 题型 3：消除左递归

高频。

看到：

```text
A → Aα | β
```

马上写：

```text
A  → βA'
A' → αA' | ε
```

---

## 题型 4：左因子提取

看到公共前缀：

```text
A → αβ1 | αβ2
```

写：

```text
A  → αA'
A' → β1 | β2
```

---

## 题型 5：FIRST/FOLLOW

必须手算熟练。

---

## 题型 6：LL(1) parse table

用 FIRST/FOLLOW 填表。

---

## 题型 7：LR(1) item closure / one-hop goto

2024 考过让你画 start state 和 one-hop reachable states。你不一定要画完整 DFA，但要会：

```text
start item
closure
goto
```

---

# 19. 考试答题模板

## Parsing 定义模板

```text
Parsing is the syntactic analysis phase of a compiler. It takes a sequence of tokens produced by the lexer and checks whether it conforms to the grammar of the language, usually producing a parse tree or AST.
```

---

## 消除左递归模板

```text
For A → Aα | β, replace it with:

A  → βA'
A' → αA' | ε
```

---

## Left factoring 模板

```text
For A → αβ1 | αβ2, replace it with:

A  → αA'
A' → β1 | β2
```

---

## LL(1) table 模板

```text
For each production A → α:
1. For each terminal a in FIRST(α), put A → α in M[A,a].
2. If ε ∈ FIRST(α), then for each b in FOLLOW(A), put A → α in M[A,b].
3. If any cell has more than one production, the grammar is not LL(1).
```

---

## LR(1) 判断模板

```text
Construct the canonical LR(1) item sets using closure and goto, then build the parsing table. If no state/lookahead pair has multiple actions, the grammar is LR(1). Otherwise, the conflicts indicate it is not LR(1).
```

---

# 20. 你现在复习 Parsing 的顺序

我建议你按这个顺序学：

```text
1. Grammar 基本概念
2. Ambiguity / precedence / associativity
3. Left recursion elimination
4. Left factoring
5. FIRST
6. FOLLOW
7. LL(1) parse table
8. LR(1) items: closure + goto
9. Conflict: shift/reduce, reduce/reduce
```

一句话总结：

> Parsing 部分的核心不是背定义，而是会改 grammar、算 FIRST/FOLLOW、填 LL(1) 表、看 LR(1) item。