下面是 **Intermediate Code, Runtime, and Code Optimisation** 的最小考试版考纲。

---

# 1. Intermediate Code / IR

**IR = Intermediate Representation**，中间表示。  
作用：把 AST 转成更接近机器、但又不依赖具体机器的代码，方便优化和生成目标代码。

常见 IR：

```text
Three Address Code / TAC
Quadruples
Triples
Indirect triples
```

---

## TAC：Three Address Code

特点：

> 每条指令最多三个地址。

例如：

```text
a = b + c
t1 = c * d
x = y
```

复杂表达式要拆：

```text
d = -b - a[i] * c
```

拆成：

```text
t1 = -b
t2 = a[i]
t3 = t2 * c
t4 = t1 - t3
d = t4
```

---

## Quadruples

四列：

```text
op | arg1 | arg2 | result
```

例子：

|op|arg1|arg2|result|
|---|---|---|---|
|`uminus`|`b`||`t1`|
|`array`|`a`|`i`|`t2`|
|`*`|`t2`|`c`|`t3`|
|`-`|`t1`|`t3`|`t4`|
|`assign`|`t4`||`d`|

---

## Triples

三列：

```text
op | arg1 | arg2
```

不用 `result`，结果用该行编号表示。

例如：

|address|op|arg1|arg2|
|---|---|---|---|
|0|`uminus`|`b`||
|1|`array`|`a`|`i`|
|2|`*`|`(1)`|`c`|
|3|`-`|`(0)`|`(2)`|
|4|`assign`|`d`|`(3)`|

---

## Indirect Triples

在 triples 外面加一层指针表。

优点：

1. **方便代码重排**：只移动指针，不改 triples。
    
2. **避免大量修改引用地址**：triples 中的行号引用不用跟着变。
    

---

# 2. Basic Blocks and Flow Graph

## Basic block

一段连续代码，满足：

```text
只有入口在第一条语句
只有出口在最后一条语句
中间没有跳转进入或跳出
```

---

## Leader 规则

一条语句是 leader，如果它是：

1. 第一条语句；
    
2. jump 的目标；
    
3. jump 后面的下一条语句。
    

每个 leader 到下一个 leader 前，就是一个 basic block。

---

## Flow graph

把 basic blocks 当节点，根据跳转关系连边：

```text
conditional jump 有两个 successor
unconditional jump 有一个 successor
普通语句 fall-through 到下一个 block
```

---

# 3. Runtime Organisation

程序运行时内存通常分成：

```text
Code
Static Data
Stack
Heap
```

---

## Code area

存机器代码。通常只读、固定大小。

## Static data

存全局变量、静态变量。地址固定。

## Stack

存函数调用产生的 **activation records**。

## Heap

存动态分配对象，例如：

```c
malloc
new
```

如果对象生命周期超过创建它的函数，就应该在 heap 上。

---

# 4. Activation Record

每次函数调用都会在 stack 上产生一个 activation record。

常见内容：

|Component|作用|
|---|---|
|Local data|局部变量|
|Actual parameters / arguments|传入参数|
|Return value|函数返回值|
|Return address|函数结束后跳回哪里|
|Control link|指向 caller 的 activation record|
|Access link|用于访问外层作用域变量|
|Temporaries|表达式计算中的临时值|
|Saved machine status|保存寄存器等机器状态|

|Component|Function|
|---|---|
|Local data|Stores local variables of the current procedure/function|
|Actual parameters / arguments|Stores the values or addresses passed to the procedure/function|
|Return value|Stores the result returned by the procedure/function|
|Return address|Stores the address to continue execution after the function returns|
|Control link|Points to the activation record of the caller|
|Access link|Points to the activation record of the enclosing lexical scope|
|Temporaries|Stores temporary values used during expression evaluation|
|Saved machine status|Stores saved registers and other machine state information|
---

## 这道题可以答三个

考试简洁版：

```text
Local data: stores local variables of the current procedure.
Actual parameters: stores values or addresses passed to the procedure.
Control link: points to the activation record of the caller, so control can return correctly.
```

也可以答：

```text
Return address: address to continue execution after the call.
Temporaries: temporary values during expression evaluation.
Return value: space for the procedure result.
```

---

# 5. Memory Management

手动动态内存分配的问题：

## Memory leak

分配了 heap object 但没有释放。

```c
p = malloc(...);
// forgot free(p)
```

## Dangling reference

对象已经释放，但指针还在使用。

```c
free(p);
*p = 3;
```

## Double free

同一块内存释放两次。

```c
free(p);
free(p);
```

Garbage collection 可以自动回收内存，但可能有额外开销、暂停程序、降低性能。

---

# 6. Code Optimisation

核心原则：

> Preserve semantics, improve performance / memory / energy.

也就是：

```text
不能改变程序结果，只能让它更好。
```

---

## Constant Folding

编译期计算常量。

```text
x = 3 * 5
```

优化为：

```text
x = 15
```

风险：overflow、浮点精度差异。

---

## Dead Code Elimination

删除不会影响结果的代码。

```text
x = 1
x = 2
```

第一句可能是 dead code。

或者：

```text
if false { ... }
```

整个分支不可达。

---

## Common Subexpression Elimination

重复表达式只算一次。

```text
x = a * b + c
y = a * b - d
```

优化为：

```text
t = a * b
x = t + c
y = t - d
```

风险：变量中间被修改、aliasing、side effects。

---

## Strength Reduction

用便宜操作替换贵操作。

```text
i * 2
```

在循环中可变成：

```text
t = t + 2
```

乘法变加法。

---

## Copy Propagation

如果：

```text
x = y
z = x + 1
```

可改为：

```text
z = y + 1
```

之后 `x = y` 可能变成 dead code。

---

## Inlining

把函数调用替换成函数体。
Replaces a function call with the body of the called function.

优点：减少 call overhead，暴露更多优化机会。  
风险：代码体积变大。

---

## Loop Unrolling

展开循环，减少 loop 控制开销。
Repeats the loop body multiple times in one iteration to reduce loop-control overhead.

优点：减少 branch，提高性能。  
风险：代码变长，cache pressure 增大。

---

## Loop-invariant Code Motion

把循环中不变的计算移到循环外。

```text
while (...) {
    x = a + b;
}
```

如果 `a,b` 不变，可优化为：

```text
x = a + b;
while (...) {
    ...
}
```

---

# 7. Code Generation

把 IR 转成目标机器代码。

需要考虑：

```text
instruction set
register allocation
memory access
basic blocks
next-use information
```

---

## RISC vs CISC

**RISC**：指令简单，通常 load/store architecture。

```text
LOAD R1, b
LOAD R2, 3
MUL R3, R1, R2
STORE a, R3
```

**CISC**：指令复杂，可能一条指令完成内存运算。

```text
MUL a, b, 3
```

---

# 最短记忆版

```text
IR/TAC：把代码拆成简单中间指令。
Quadruple：op, arg1, arg2, result。
Runtime：Code / Static / Stack / Heap。
Activation record：一次函数调用在 stack 上的记录。
Optimisation：保持语义不变，提升性能。
常见优化：constant folding, DCE, CSE, strength reduction, inlining, loop unrolling。
Code generation：把 IR 变成机器指令。
```