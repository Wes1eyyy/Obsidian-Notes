好，我们按 **TA 带逃课学生补课** 的方式复习 NFA 和 DFA。你先别急着背形式化定义，先抓住一句话：

> DFA / NFA 都是“读字符串的机器”，它们一边读字符，一边在状态之间移动。最后停在 accepting state，就接受这个字符串；否则拒绝。

---

# 1. Automaton 是什么？

automaton 可以理解成一个“自动判卷机”。

它的任务是判断一个字符串是否属于某个语言。

比如我们想识别所有以 `ab` 结尾的字符串：

```text
ab
aab
bbab
abab
```

这些应该接受。

```text
a
aba
abb
ba
```

这些应该拒绝。

那么 DFA / NFA 就是用状态图来表达这个识别规则。

---

# 2. DFA 是什么？

DFA = Deterministic Finite Automaton。

中文常叫：

> 确定有限自动机

关键词是 **deterministic**，确定性。

意思是：

> 在任意一个状态，读到某个输入字符时，下一步去哪里是唯一确定的。

比如：

```text
当前状态 q0，读到 a，一定去 q1
当前状态 q0，读到 b，一定去 q0
```

不能有两个选择，也不能不知道去哪。

---

## DFA 的结构

一个 DFA 通常包含 5 个东西：

```text
(Q, Σ, δ, q0, F)
```

你不用一开始死背，但要知道每个是什么：

|符号|含义|
|---|---|
|Q|所有状态的集合|
|Σ|字母表，也就是可能出现的输入字符|
|δ|transition function，状态转移函数|
|q0|start state，初始状态|
|F|accepting states，接受状态集合|

---

# 3. 用一个最简单 DFA 理解

我们设计一个 DFA，识别所有 **以 a 结尾** 的字符串。

字母表：

```text
Σ = {a, b}
```

要接受：

```text
a
ba
aba
bba
```

要拒绝：

```text
空串
b
ab
abb
```

---

## 思路

我们只需要记住：

> 当前读到的最后一个字符是不是 a。

所以设计两个状态：

```text
q0：目前最后一个字符不是 a，或者还没读字符
q1：目前最后一个字符是 a
```

其中 `q1` 是接受状态。

状态转移：

|当前状态|读 a|读 b|
|---|---|---|
|q0|q1|q0|
|q1|q1|q0|

画成文字图：

```text
        a
   ┌────────▶ ((q1))
   │           ▲  │
   │           │  │ b
   │           │  ▼
→ q0 ◀────────┘
     b       a
```

更清楚地说：

```text
q0 --a--> q1
q0 --b--> q0
q1 --a--> q1
q1 --b--> q0
```

---

## 检查字符串 `aba`

```text
start q0
read a → q1
read b → q0
read a → q1
```

最后停在 `q1`，所以接受。

---

## 检查字符串 `abb`

```text
start q0
read a → q1
read b → q0
read b → q0
```

最后停在 `q0`，不是接受状态，所以拒绝。

---

# 4. NFA 是什么？

NFA = Nondeterministic Finite Automaton。

中文常叫：

> 非确定有限自动机

关键词是 **nondeterministic**，非确定性。

意思是：

> 在某个状态读到某个字符时，它可以有多个选择，也可以没有选择。

甚至还可以有 **ε-transition**，也就是：

> 不读任何字符，直接跳到另一个状态。

---

# 5. NFA 和 DFA 最大区别

|对比点|DFA|NFA|
|---|---|---|
|一个状态读一个字符|只能有一个下一状态|可以有多个下一状态|
|能不能没有转移|通常完整 DFA 每个字符都要有转移|可以没有|
|能不能 ε 跳转|不可以|可以|
|接受条件|唯一路径结束在接受状态|只要存在一条路径能到接受状态就接受|

最重要的是最后一句：

> NFA 只要有一条成功路径，就接受字符串。

---

# 6. NFA 的“多条路”怎么理解？

假设一个 NFA 有这样的转移：

```text
q0 --a--> q1
q0 --a--> q2
```

意思是：

> 在 q0 读到 a 时，它可以去 q1，也可以去 q2。

你可以把 NFA 想象成：

> 机器会同时尝试所有可能路径。只要其中一条成功，就算成功。

---

# 7. 一个简单 NFA 例子

设计一个 NFA，识别所有 **包含子串 `ab`** 的字符串。

例如：

```text
ab
aab
abb
baba
```

都接受。

```text
a
b
ba
aaa
bbb
```

拒绝。

---

## NFA 思路

我们可以设计：

```text
q0：还没开始匹配 ab
q1：已经看到 a，正在等 b
q2：已经看到 ab，接受
```

转移：

```text
q0 --a--> q0
q0 --b--> q0
q0 --a--> q1
q1 --b--> q2
q2 --a--> q2
q2 --b--> q2
```

注意 q0 上读 `a` 有两条路：

```text
q0 --a--> q0
q0 --a--> q1
```

这就是 NFA。

它的意思是：

> 每当看到一个 a，我既可以把它当作普通字符忽略，也可以猜它是 `ab` 的开头。

---

## 检查字符串 `baab`

```text
start q0
read b → q0
read a → q0 或 q1
read a → q0 或 q1
read b → 如果之前在 q1，则可以到 q2
```

存在一条路径到 `q2`，所以接受。

---

# 8. ε-transition 是什么？

ε-transition 是 NFA 特有的重要概念。

它表示：

> 不消耗任何输入字符，就可以从一个状态跳到另一个状态。

例如：

```text
q0 --ε--> q1
```

意思是：

```text
当前在 q0，不读字符，也可以直接去 q1
```

---

## ε-transition 用来干什么？

它经常用于把小自动机拼起来。

比如 regex：

```regex
a|b
```

表示匹配 `a` 或 `b`。

可以设计成：

```text
        ε       a       ε
      ┌──▶ q1 ───▶ q2 ───┐
      │                  ▼
→ q0                    q5
      │                  ▲
      └──▶ q3 ───▶ q4 ───┘
        ε       b       ε
```

从 q0 不读字符，可以选择走 a 分支，也可以选择走 b 分支。

---

# 9. DFA 和 NFA 谁更强？

这是考试常问概念。

答案是：

> DFA 和 NFA 表达能力一样强。

也就是说：

```text
任何 NFA 都可以转换成等价 DFA。
任何 DFA 也天然是 NFA 的一种特殊情况。
```

NFA 通常更容易设计，DFA 更容易直接执行。

---

# 10. NFA 转 DFA：Powerset Construction

这是重点中的重点。

NFA 里面一个状态读字符可能去多个状态，所以 DFA 要怎么模拟？

核心思想是：

> DFA 的一个状态 = NFA 的一组可能状态。

也就是说，如果 NFA 读到某个位置时，可能同时在：

```text
{q0, q1, q3}
```

那 DFA 就把这个集合当成一个状态。

这就是 powerset construction。

因为 NFA 有 n 个状态时，所有状态子集最多有：

```text
2^n
```

个，所以叫 powerset。

---

# 11. Powerset Construction 的步骤

给你一个 NFA，要转 DFA，你按这个流程做：

## Step 1：求初始 DFA 状态

如果没有 ε-transition：

```text
DFA start state = {NFA start state}
```

如果有 ε-transition：

```text
DFA start state = ε-closure(NFA start state)
```

---

## Step 2：对每个 DFA 状态和每个输入字符做 move

比如当前 DFA 状态是：

```text
{q0, q1}
```

读入 `a`：

```text
从 q0 读 a 能去哪？
从 q1 读 a 能去哪？
把结果合并
```

得到一个新的集合，比如：

```text
{q1, q2}
```

这个集合就是新的 DFA 状态。

---

## Step 3：如果有 ε-transition，再求 ε-closure

也就是读完字符后，还要把所有可以通过 ε 免费到达的状态也加进来。

---

## Step 4：重复直到没有新状态

一直做，直到所有集合状态都处理完。

---

## Step 5：确定接受状态

DFA 的某个集合状态只要包含 NFA 的接受状态，它就是接受状态。

比如 NFA 接受状态是 `q3`。

那么 DFA 状态：

```text
{q0, q3}
```

是接受状态。

```text
{q0, q1}
```

不是接受状态。

---

# 12. ε-closure 是什么？

ε-closure(q) 表示：

> 从 q 出发，只走 ε-transition，能到达的所有状态，包括 q 自己。

比如：

```text
q0 --ε--> q1
q1 --ε--> q2
q2 --a--> q3
```

那么：

```text
ε-closure(q0) = {q0, q1, q2}
```

不能包括 q3，因为到 q3 要读 `a`，不是 ε。

如果是一个状态集合：

```text
ε-closure({q0, q4})
```

就分别求 q0 和 q4 的 ε-closure，再合并。

---

# 13. 用一个小例子做 NFA → DFA

NFA：

```text
状态：q0, q1, q2
字母表：{a, b}
初始状态：q0
接受状态：q2

转移：
q0 --a--> q0
q0 --a--> q1
q1 --b--> q2
q2 --a--> q2
q2 --b--> q2
```

这个 NFA 识别包含 `ab` 的字符串。

没有 ε-transition，所以不用 ε-closure。

---

## DFA 初始状态

```text
A = {q0}
```

---

## 从 A = {q0} 出发

读 `a`：

```text
q0 --a--> q0, q1
```

所以：

```text
A --a--> B = {q0, q1}
```

读 `b`：

```text
q0 --b--> q0
```

所以：

```text
A --b--> A = {q0}
```

---

## 从 B = {q0, q1} 出发

读 `a`：

```text
q0 --a--> q0, q1
q1 --a--> nowhere
```

所以：

```text
B --a--> B = {q0, q1}
```

读 `b`：

```text
q0 --b--> q0
q1 --b--> q2
```

所以：

```text
B --b--> C = {q0, q2}
```

---

## 从 C = {q0, q2} 出发

读 `a`：

```text
q0 --a--> q0, q1
q2 --a--> q2
```

所以：

```text
C --a--> D = {q0, q1, q2}
```

读 `b`：

```text
q0 --b--> q0
q2 --b--> q2
```

所以：

```text
C --b--> C = {q0, q2}
```

---

## 从 D = {q0, q1, q2} 出发

读 `a`：

```text
q0 --a--> q0, q1
q1 --a--> nowhere
q2 --a--> q2
```

所以：

```text
D --a--> D
```

读 `b`：

```text
q0 --b--> q0
q1 --b--> q2
q2 --b--> q2
```

所以：

```text
D --b--> C
```

---

## DFA 状态表

|DFA 状态|NFA 状态集合|读 a|读 b|是否接受|
|---|---|---|---|---|
|A|{q0}|B|A|否|
|B|{q0, q1}|B|C|否|
|C|{q0, q2}|D|C|是|
|D|{q0, q1, q2}|D|C|是|

C 和 D 包含 NFA 接受状态 q2，所以是接受状态。

---

# 14. DFA complement：补语言

这也是考试常见点。

如果一个 DFA 识别语言 L，那么 complement DFA 识别：

```text
Σ* - L
```

也就是所有原来不接受的字符串。

做法很简单：

> 把 accepting states 和 non-accepting states 对调。

但是有一个前提：

> DFA 必须是 complete 的。

也就是每个状态对每个输入字符都必须有转移。

如果缺转移，需要先补一个 dead state。

---

## 为什么要 complete？

比如某个 DFA：

```text
q0 --a--> q1
```

但 q0 没有 b 转移。

如果你直接对调接受状态，读到 `b` 时机器不知道去哪，无法正确表达“拒绝变接受”。

所以要补：

```text
q0 --b--> q_dead
q_dead --a--> q_dead
q_dead --b--> q_dead
```

然后再交换接受/非接受状态。

---

# 15. DFA minimisation 简单理解

DFA minimisation 是把等价状态合并，得到更小的 DFA。

两个状态等价的意思是：

> 从这两个状态出发，对任何后续输入，它们最终接受/拒绝的结果都一样。

常用方法是 partition refinement：

## Step 1

先分成两组：

```text
接受状态
非接受状态
```

## Step 2

看每个状态读同一个字符后，会不会跳到不同分组。

如果会，就要继续拆。

## Step 3

重复直到不能再拆。

---

# 16. NFA / DFA 常考题型

COMP0012 里通常会这样考：

## 题型 1：给语言，画 NFA / DFA

比如：

```text
识别所有以 a 结尾的字符串
识别所有包含 ab 的字符串
识别所有二进制数能被 3 整除的字符串
```

你要会设计状态含义。

---

## 题型 2：NFA 转 DFA

给你一个 NFA 图，让你 powerset construction。

你要会写：

```text
ε-closure
move
新的 DFA 状态集合
接受状态
```

---

## 题型 3：DFA complement

问：

```text
Can you always complement a DFA?
```

标准答案：

> 对 complete DFA，可以通过交换 accepting 和 non-accepting states 得到 complement。如果 DFA 不完整，需要先加 dead state。

---

## 题型 4：DFA minimisation

给你一个 DFA，让你用 table filling 或 partition refinement 最小化。

你要会分组、拆分、合并。

---

# 17. 你最应该记住的区别

|概念|你要怎么想|
|---|---|
|DFA|每一步只有一条路|
|NFA|每一步可以有多条路|
|ε-transition|不读字符也能跳|
|NFA 接受|只要有一条路径成功|
|Powerset construction|DFA 的一个状态 = NFA 的一组可能状态|
|ε-closure|只靠 ε 能到的所有状态|
|DFA complement|完整 DFA 交换接受/非接受|
|DFA minimisation|合并行为完全一样的状态|

---

# 18. 给你一个最基础练习

设计一个 DFA，识别所有由 `{0,1}` 组成，并且 **以 `01` 结尾** 的字符串。

应该接受：

```text
01
001
101
1101
```

不应该接受：

```text
0
1
10
011
010
```

提示你设计 3 个状态：

```text
q0：目前没有匹配到有用后缀
q1：当前最后一个字符是 0
q2：当前最后两个字符是 01，接受状态
```

你可以先试着写状态转移表：

|状态|读 0|读 1|
|---|---|---|
|q0|?|?|
|q1|?|?|
|q2|?|?|