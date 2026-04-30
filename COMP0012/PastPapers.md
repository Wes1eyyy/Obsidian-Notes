![[Pasted image 20260430122628.png]]下面把图中 **(e)(i), (e)(ii), (f)** 都解出来。

---

## (e)(i) Type expressions

### Method declaration

```java
boolean mystery(int[] x, double y)
```

参数：

```text
x : array(int)
y : double
```

返回值：

```text
boolean
```

所以 type expression：

```text
mystery: x:array(int), y:double → boolean
```

也可以写成：

```text
mystery: array(int) × double → boolean
```

---

### Class declaration

```java
public class Cell {
    double[10] val;
    double max;
    Cell next;
}
```

字段类型：

```text
val  : array(double, 10)
max  : double
next : Cell
```

所以 type expression：

```text
Cell = { val: array(double, 10), max: double, next: Cell }
```

---

## (e)(ii) Type error 分析

代码：

```c
void main(){
    int x = 3;
    x = x - 0.5;
}
```

语义分析器步骤：

1. 建立 symbol table：
    

```text
x : int
```

2. 分析表达式：
    

```text
x - 0.5
```

其中：

```text
x : int
0.5 : real/double
```

3. 根据题目给出的 typing rule：
    

> arithmetic expressions 的两个 operands 必须都是 integers 或都是 reals。

但是这里是：

```text
int - real
```

两边类型不一致，所以 `x - 0.5` 本身就违反 arithmetic typing rule。

即使语言允许算出 real，赋值给：

```text
x : int
```

也会产生类型不匹配。

考试简短版：

```text
The semantic analyser records x as int. It then checks the expression x - 0.5. The left operand x has type int, while 0.5 has type real. The typing rule requires both operands of arithmetic operators to be either both integers or both reals. Therefore the expression is ill-typed, and the assignment to int x is rejected.
```

---

## (f) Translate `r = -x[i] - 5 * y` into TAC triples

表达式：

```text
r = -x[i] - 5 * y
```

先理解运算顺序：

```text
r = ( - x[i] ) - ( 5 * y )
```

对应 TAC：

```text
t1 = x[i]
t2 = -t1
t3 = 5 * y
t4 = t2 - t3
r = t4
```

---

## Triples representation

Triples 没有 `result` 列，结果用 instruction address 引用。

|Address|op|arg1|arg2|
|---|---|---|---|
|0|`array`|`x`|`i`|
|1|`uminus`|`(0)`||
|2|`*`|`5`|`y`|
|3|`-`|`(1)`|`(2)`|
|4|`assign`|`r`|`(3)`|

解释：

```text
(0) 表示第 0 行的结果，即 x[i]
(1) 表示第 1 行的结果，即 -x[i]
(2) 表示第 2 行的结果，即 5 * y
(3) 表示第 3 行的结果，即 -x[i] - 5*y
```

最终：

```text
r = (3)
```

![[Pasted image 20260430123400.png]]## 4(a) Indirect triples 的两个好处

**Indirect triples** 在 triples 外面加了一层 pointer table。

好处 1：**方便代码重排 / code reordering**。  
因为只需要改变 pointer table 的顺序，不需要移动真正的 triples 指令。

好处 2：**避免更新大量引用地址**。  
普通 triples 用 instruction address 引用前面结果；如果移动指令，地址会变，很多引用都要改。Indirect triples 中实际 triples 保持不动，只调整 pointer，所以引用更稳定。

考试版：

```text
Indirect triples make code reordering easier because the compiler can reorder the pointer list instead of moving the actual triples. They also avoid changing many instruction references, because triples can keep their original addresses while the indirection table determines execution order.
```

---

## 4(b) Activation record 三个组件

### Local data

Stores local variables of the current procedure/function.

### Control link

Points to the activation record of the caller, so the program can restore the caller’s environment when the current procedure returns.

### Actual parameters

Stores the values or addresses of arguments passed to the procedure/function.

考试版：

```text
Local data stores the local variables of the current procedure. The control link points to the caller’s activation record, allowing control to return to the previous activation. Actual parameters store the values or addresses passed into the procedure.
```

---

## 4(c) Optimisation techniques

### Constant Folding

**What it does:**  
Computes constant expressions at compile time.

Example:

```c
x = 3 * 5;
```

becomes:

```c
x = 15;
```

**Benefit:**  
Reduces runtime computation and can enable further optimisations.

**Risk:**  
Compiler must preserve language semantics, especially for overflow, floating-point precision, and exceptions such as division by zero.

---

### Common Subexpression Elimination

**What it does:**  
Avoids recomputing the same expression if its operands have not changed.

Example:

```c
x = a * b + c;
y = a * b - d;
```

becomes:

```c
t = a * b;
x = t + c;
y = t - d;
```

**Benefit:**  
Reduces repeated computation and improves execution speed.

**Risk:**  
Only safe if the expression has no side effects and its operands have not changed; compiler must consider aliasing, memory writes, and function calls.

---

![[Pasted image 20260430123445.png]]
## 最短考试答案

```text
Constant folding evaluates constant expressions at compile time, reducing runtime work, but must be careful with overflow, floating-point precision and exceptions.

Common subexpression elimination computes repeated expressions once and reuses the result, improving speed, but is only safe if operands are unchanged and the expression has no side effects or aliasing issues.
```

答案选：

```text
B. Dead Code Elimination
C. Constant Folding
```

---

原代码：

```c
int x = 10;
int y = 3 * x;
int z = y - 31;

z = z + 1;
while (z != 0) {
    y = y * 3;
    z = z + 1;
}
y = x * z * (y + 7);
```

先做常量推导：

```text
x = 10
y = 3 * 10 = 30
z = 30 - 31 = -1
z = z + 1 = 0
```

所以到 `while` 前：

```text
z = 0
```

因此：

```c
while (z != 0)
```

条件为 false，整个循环不会执行。

循环后：

```c
y = x * z * (y + 7)
```

因为：

```text
z = 0
```

所以：

```text
y = 10 * 0 * (30 + 7) = 0
```

最终：

```c
int x = 10;
int y = 0;
int z = 0;
```

---

为什么选 C？

因为编译器把：

```text
3 * 10
30 - 31
-1 + 1
10 * 0 * ...
```

这些常量表达式在编译期算出来了，这叫 **Constant Folding**。

---

为什么选 B？

因为循环：

```c
while (z != 0) { ... }
```

在常量推导后永远不会执行，所以可以删除。  
这叫 **Dead Code Elimination**。

---

为什么不选 A？

Common Subexpression Elimination 是消除重复表达式，例如：

```c
a * b + a * b
```

但这里没有需要复用的重复表达式。

---

为什么不选 Inlining？

Inlining 是把函数调用替换成函数体。这里没有函数调用。

---

最终答案：

```text
B and C
```

如果选项里有 “Constant Folding and Dead Code Elimination”，就选那个组合项。

![[Pasted image 20260430123615.png]]这题只做 **dead code elimination**，不能做其他优化。

原代码关键部分：

```c
while (n > 0) {
    b = b + 3 * n + y;
    n = n - 3;
}

if (n > 0) {
    ...
} elseif (n < 0) {
    return -1;
} else {
    n = n - 1;
    return n;
}
```

循环结束条件是：

```text
n <= 0
```

所以循环后面的：

```c
if (n > 0)
```

永远不可能成立，是 unreachable dead code，可以删掉。

而 `b` 只在这个永远不会执行的分支里被返回：

```c
return b;
```

所以 `b` 的初始化和循环中对 `b` 的更新也都变成 dead code，可以删掉。

---

## 答案

```c
def mystery(int x, int y) {
    int n = x;

    while (n > 0) {
        n = n - 3;
    };

    if (n < 0) {
        return -1;
    } else {
        n = n - 1;
        return n;
    }
}
```

---

注意：这里不能进一步改成更短的形式，因为题目说 **dead code elimination, but no other optimization**。所以我们只删除不可达代码和不再被使用的赋值，不做额外语义化简。

![[Pasted image 20260430123637.png]]

这题代码是：

```c
r = 1;
repeat {
    r = r * a;
    n = n + 1;
} until (n >= 0)
```

`repeat ... until` 的特点是：

> 循环体至少执行一次；执行完循环体后检查条件。如果条件为 false，就继续循环。

---

## 1. Translate into 3-address code

可以写成：

```text
r = 1

LABEL L1:
t1 = r * a
r = t1
t2 = n + 1
n = t2
t3 = n >= 0
FJUMP t3 L1
```

解释：

```text
t3 = n >= 0
FJUMP t3 L1
```

意思是：

> 如果 `n >= 0` 是 false，就跳回 L1 继续循环。

也可以用 `CJUMP` 写：

```text
r = 1

LABEL L1:
t1 = r * a
r = t1
t2 = n + 1
n = t2
t3 = n >= 0
CJUMP t3 L2
JUMP L1
LABEL L2:
```

两种写法都可以。第一种更简洁。

---

## 2. Partition into basic blocks

用第一种 TAC：

```text
1  r = 1
2  LABEL L1:
3  t1 = r * a
4  r = t1
5  t2 = n + 1
6  n = t2
7  t3 = n >= 0
8  FJUMP t3 L1
```

---

## Leader 规则

一个语句是 leader，如果它是：

```text
1. 第一条语句
2. jump 的目标
3. jump 后面的下一条语句
```

这里：

```text
1 是第一条语句，所以是 leader
2 是 L1，是 jump target，所以是 leader
```

第 8 行后面没有下一条语句，所以没有新的 leader。

---

## Basic blocks

因此有两个 basic blocks：

```text
B1:
1  r = 1
```

```text
B2:
2  LABEL L1:
3  t1 = r * a
4  r = t1
5  t2 = n + 1
6  n = t2
7  t3 = n >= 0
8  FJUMP t3 L1
```

---

## Flow relation

```text
B1 → B2
B2 → B2   if n >= 0 is false
B2 exits  if n >= 0 is true
```

更准确说：

```text
FJUMP t3 L1
```

如果 `t3` 为 false，跳回 `L1`，也就是 `B2` 自循环；如果 `t3` 为 true，则循环结束，fall through 到后续代码或 exit。

---

## 考试最终答案

```text
3-address code:

r = 1
LABEL L1:
t1 = r * a
r = t1
t2 = n + 1
n = t2
t3 = n >= 0
FJUMP t3 L1
```

```text
Basic blocks:

B1:
r = 1

B2:
LABEL L1:
t1 = r * a
r = t1
t2 = n + 1
n = t2
t3 = n >= 0
FJUMP t3 L1
```

Justification:

```text
The first statement is a leader. L1 is also a leader because it is the target of a jump. Therefore the code is partitioned into two basic blocks: the initial assignment block and the loop body block.
```

![[Pasted image 20260430123714.png]]
## (i) RISC vs CISC

**RISC** uses a small set of simple instructions. Operations are usually done on registers, so memory values must first be loaded into registers and then stored back.

**CISC** uses more complex instructions. A single instruction may perform a more complicated operation, sometimes directly involving memory.

---

## (ii) Code for `a = b * 3`

### RISC-style code

```text
LOAD R1, b
LOAD R2, 3
MUL R3, R1, R2
STORE a, R3
```

Explanation:

```text
R1 = b
R2 = 3
R3 = b * 3
a = R3
```

---

### CISC-style code

```text
MUL a, b, 3
```

or:

```text
MULT a, b, 3
```

Explanation:

```text
a = b * 3
```

---

Exam-short version:

```text
RISC has simple load/store instructions and usually requires several instructions using registers. CISC has more complex instructions and may perform memory operations in one instruction.

RISC:
LOAD R1, b
LOAD R2, 3
MUL R3, R1, R2
STORE a, R3

CISC:
MUL a, b, 3
```