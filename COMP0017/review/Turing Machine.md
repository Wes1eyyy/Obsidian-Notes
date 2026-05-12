# 1. 通俗理解

**Turing Machine 图灵机**是一种非常抽象、非常基础的“计算机模型”。

你可以把它想象成：

```text
一个很笨但很严格的机器人，
在一条无限长的纸带上读写符号。
```

它每一步只能做几件简单的事：

```text
1. 看当前格子上的符号
2. 根据规则改写这个符号
3. 向左或向右移动一格
4. 进入下一个状态
```

虽然每一步都很简单，但只要规则设计得足够复杂，它就可以模拟我们平时写的程序。

---

# 2. 图灵机由什么组成？

一台图灵机通常写成：

```text
M = (Q, Σ, Γ, δ, q0, qaccept, qreject)
```

考试里看到这个不要慌，它只是把机器的组成部分写清楚。

| 符号             | 含义                   | 通俗理解        |
| -------------- | -------------------- | ----------- |
| (Q)            | finite set of states | 机器有哪些状态     |
| ($\Sigma$)     | input alphabet       | 输入可以包含哪些字符  |
| ($\Gamma$)     | tape alphabet        | 纸带上可以出现哪些字符 |
| ($\delta$)     | transition function  | 每一步怎么走      |
| ($q_0$)        | start state          | 初始状态        |
| ($q_{accept}$) | accept state         | 接受状态        |
| ($q_{reject}$) | reject state         | 拒绝状态        |

---

# 3. 纸带 Tape 是什么？

图灵机有一条无限长的纸带。

```text
a b b a _ _ _ _ ...
↑
head
```

每个格子里可以有一个符号。

空白格通常记作：

```text
blank symbol
```

例如：

```text
_
```

图灵机的读写头 head 每次只看一个格子。

---

# 4. Transition Function 是什么？

转移函数 (\delta) 是图灵机的“规则表”。

它告诉机器：

```text
在当前状态下，
如果看到某个符号，
应该写什么，
往哪边移动，
进入哪个新状态。
```

形式上可以写成：

```text
δ(q, a) = (q', b, R)
```

意思是：

```text
如果机器在状态 q，
并且当前读到符号 a，
那么它把 a 改写成 b，
向右移动一格，
进入状态 q'。
```

如果是：

```text
δ(q, a) = (q', b, L)
```

就是向左移动一格。

---

# 5. 图灵机如何处理输入？

比如输入是：

```text
abba
```

初始纸带大概是：

```text
a b b a _ _ _ ...
↑
head starts here
```

机器从 (q_0) 开始，根据 (\delta) 一步一步运行。

最后有三种可能：

```text
1. 进入 qaccept：接受输入
2. 进入 qreject：拒绝输入
3. 永远运行下去：loop forever
```

---

# 6. Accept / Reject / Halt / Loop

这四个词非常重要。

## Accept

```text
M accepts w
```

意思是：

```text
M 在输入 w 上最终进入 qaccept。
```

---

## Reject

```text
M rejects w
```

意思是：

```text
M 在输入 w 上最终进入 qreject。
```

---

## Halt

```text
M halts on w
```

意思是：

```text
M 最终停机。
```

停机包括两种情况：

```text
accept 或 reject
```

所以：

```text
halt = accept or reject
```

---

## Loop

```text
M loops on w
```

意思是：

```text
M 在输入 w 上永远运行，不停机。
```

注意：

```text
loop 不等于 reject。
```

---

# 7. 图灵机和程序有什么关系？

你可以把图灵机理解成：

```text
程序的数学抽象版本。
```

Python、Java、C++ 看起来比图灵机高级很多，但从“能计算什么”这个角度看，它们和图灵机能力一样。

所以这门课用图灵机来研究：

```text
什么问题能被程序解决？
什么问题永远不能被程序解决？
```

---

# 8. 考试 short English definition

如果题目问：

```text
What is a Turing machine?
```

可以写：

```text
A Turing machine is an abstract model of computation consisting of a finite set of states, an infinite tape, a tape head, and a transition function. At each step, it reads a symbol, writes a symbol, moves the head left or right, and changes state.
```

更短版本：

```text
A Turing machine is a mathematical model of computation that manipulates symbols on an infinite tape according to a finite set of rules.
```

---

# 9. 如果题目要求 formal definition

可以写：

```text
A Turing machine is a tuple
M = (Q, Σ, Γ, δ, q0, qaccept, qreject),
where Q is a finite set of states, Σ is the input alphabet, Γ is the tape alphabet, δ is the transition function, q0 is the start state, and qaccept and qreject are the accepting and rejecting states.
```

如果需要补充 transition function：

```text
The transition function δ determines, for each state and tape symbol, the next state, the symbol to write, and the direction in which the head moves.
```

---

# 10. 考试中常见问法

## 问法 1：Define a Turing machine

答：

```text
A Turing machine is a formal model of computation with a finite control, an infinite tape, a tape head, and a transition function. It computes by repeatedly reading and writing tape symbols, moving the head, and changing state.
```

---

## 问法 2：Give the tuple definition of a Turing machine

答：

```text
A Turing machine is a tuple M = (Q, Σ, Γ, δ, q0, qaccept, qreject), where Q is a finite set of states, Σ is the input alphabet, Γ is the tape alphabet, δ is the transition function, q0 is the initial state, and qaccept and qreject are the accepting and rejecting states.
```

---

## 问法 3：What does it mean for M to halt on input w?

答：

```text
M halts on input w if its computation on w eventually enters either qaccept or qreject.
```

---

## 问法 4：What does it mean for M to accept input w?

答：

```text
M accepts input w if its computation on w eventually enters the accepting state qaccept.
```

---

# 11. 一句话记忆

```text
图灵机 = 无限纸带 + 读写头 + 有限状态 + 转移规则。
```

英文考试版：

```text
A Turing machine is an abstract computer that reads and writes symbols on an infinite tape according to a finite transition function.
```

最重要的是，你要把它看成：

```text
用来严格定义“算法”和“可计算性”的模型。
```