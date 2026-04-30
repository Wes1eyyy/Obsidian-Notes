## Semantic Analysis 你需要掌握的最小考纲

### 1. Syntax vs Semantics

**Syntax**：程序的结构是否合法。  
例如：

```c
int = x ;
```

结构错，是 syntax error。

**Semantics**：程序结构合法后，意思是否正确。  
例如：

```c
int x = "hello";
```

结构可能合法，但类型错，是 semantic error。

---

### 2. 这题答案

题目选项：

A. The semantics of a language assigns meaning to the member words of the language.  
**正确。** Semantics 负责赋予程序含义。

B. Syntax errors are detected at run-time.  
**错误。** Syntax errors 通常在 compile-time / parsing 阶段发现。

C. Using an undefined variable causes a syntax error.  
**错误。** Undefined variable 是 semantic error，不是 syntax error。

D. The syntax of a language determines its structure, i.e. what words are part of the language.  
**正确。** Syntax 决定哪些 token/string 组合属于语言。

所以答案是：

```text
A, D
```

---

## 3. Semantic Analysis 主要检查什么

你记住这几类：

| 检查内容                   | 例子                        | 错误类型           |
| ---------------------- | ------------------------- | -------------- |
| Scope checking         | 变量没声明就使用                  | semantic error |
| Multiple declaration   | 同一作用域重复声明变量               | semantic error |
| Type checking          | `int x = "hello"`         | semantic error |
| Function call checking | 参数数量/类型不匹配                | semantic error |
| Return checking        | `int f(){ return "hi"; }` | semantic error |

---

## 4. Static Binding vs Dynamic Binding

**Static binding / static scoping**：  

Static binding resolves names using the program’s lexical structure at compile time.
根据代码的词法位置找变量，compile time 决定。

**Dynamic binding / dynamic scoping**：  

Dynamic binding resolves names using the runtime call chain during execution.
根据运行时调用链找变量，runtime 决定。

考试常给一段代码，让你分别算 static / dynamic 输出。

---

## 5. Symbol Table

Semantic analyser 会用 **symbol table** 存信息：

```text
name, kind, type, scope
```

例如：

```text
x | variable | int | local
foo | function | int → bool | global
```

常见操作：

```text
INSERT：声明变量时加入当前 scope
LOOKUP：使用变量时从当前 scope 向外层 scope 查找
```

---

## 6. Type Expression

会把声明写成类型表达式。

例如：

```java
int foo(boolean b, double[] a)
```

写成：

```text
foo: boolean × array(double) → int
```

类：

```java
class node {
  int[10] val;
  node next;
}
```

写成：

```text
node = { val: array(int,10), next: node }
```

---

## 7. 为什么 Semantic Analysis 不能靠 CFG Parser 完成？

Parser 主要处理 **context-free syntax**。

但 semantic checks 需要上下文信息，比如：

```text
变量之前有没有声明？
这个变量类型是什么？
函数参数数量对不对？
return 类型和函数声明是否一致？
```

这些需要 symbol table / type environment，所以属于 context-sensitive analysis。

---

## 一句话记忆

> Syntax 看“结构像不像代码”；Semantics 看“这段代码有没有意义”。

考试最常见判断：

```text
undefined variable = semantic error
type mismatch = semantic error
syntax error = compile-time/parser stage
semantics assigns meaning
syntax defines structure
```


Explain the difference between a context-free grammar and a context-sensitive grammar.

In a context-free grammar, every production has a single non-terminal on the left-hand side, e.g. A → α, so the non-terminal can be rewritten independently of its context. In a context-sensitive grammar, productions may depend on surrounding symbols, e.g. αAβ → αγβ, so rewriting can depend on the context in which a symbol appears.



![[Pasted image 20260430121424.png]]Scope checking and type checking are context-sensitive semantic analyses. Scope checking requires information about declarations and active scopes, while type checking requires the declared types of variables and functions. A parser using a context-free grammar only checks syntactic structure, so these checks require semantic information such as symbol tables and type environments.