# 1. 通俗理解

在这门课里，**language** 不是英语、中文，而是：

```text
一组字符串的集合。
```

比如：

```text
L = { w ∈ {a,b}* | w contains at least one a }
```

意思是：

```text
L 是所有由 a 和 b 组成，并且至少包含一个 a 的字符串。
```

例如：

```text
a      ∈ L
ba     ∈ L
bbbabb ∈ L
bbb    ∉ L
```

图灵机处理 language 的问题，本质就是：

```text
给一个字符串 w，判断它属不属于 L。
```

---

# 2. Language recognition 是什么？

## 通俗解释

如果一台图灵机 **recognises** 一个 language (L)，意思是：

```text
只要输入 w 真的是 L 里面的字符串，
机器最终一定会 accept。
```

但是，如果 (w) 不在 (L) 里面，机器不一定会明确 reject。

它可能：

```text
reject
```

也可能：

```text
一直运行下去，永远不停
```

所以 recognition 只保证 **yes case**。

你可以把 recogniser 想成一个“只擅长确认正确答案的人”：

```text
如果答案是 yes，它一定能告诉你 yes。
如果答案是 no，它可能告诉你 no，也可能一直想不出来。
```

---

## 考试 short English definition

```text
A Turing machine M recognises a language L if, for every input w:
if w ∈ L, then M accepts w;
if w ∉ L, then M either rejects w or loops forever.
```

更短版本：

```text
M recognises L iff M accepts exactly the strings in L.
For strings not in L, M may reject or loop.
```

---

# 3. Language decision 是什么？

## 通俗解释

如果一台图灵机 **decides** 一个 language (L)，意思是：

```text
对于任何输入 w，
机器都必须最终停下来，
并且正确回答 yes 或 no。
```

如果 (w ∈ L)，机器 accept。  
如果 (w ∉ L)，机器 reject。

关键是：

```text
decider 不允许无限循环。
```

所以 decision 比 recognition 更强。

你可以把 decider 想成一个“必须交卷的人”：

```text
如果答案是 yes，它说 yes。
如果答案是 no，它说 no。
而且无论如何都不能一直拖着不回答。
```

---

## 考试 short English definition

```text
A Turing machine M decides a language L if, for every input w:
if w ∈ L, then M accepts w;
if w ∉ L, then M rejects w;
and M halts on all inputs.
```

更短版本：

```text
M decides L iff M accepts exactly the strings in L and halts on every input.
```

---

# 4. Recognition vs Decision 的核心区别

|概念|(w ∈ L)|(w ∉ L)|是否必须 halt|
|---|---|---|---|
|Recognise (L)|accept|reject 或 loop|不一定|
|Decide (L)|accept|reject|必须|

一句话记忆：

```text
Recognise: yes 一定能确认，no 可能卡住。
Decide: yes/no 都必须确认。
```

---

# 5. 为什么 decision 更强？

因为如果一台机器可以 **decide** (L)，它当然也可以 **recognise** (L)。

也就是说：

```text
decidable ⇒ recognisable
```

原因很简单：

```text
如果机器连 yes/no 都能判断，
那它当然能识别 yes 的情况。
```

但反过来不成立：

```text
recognisable ⇏ decidable
```

因为 recogniser 在 no 的情况下可能永远 loop。

---
![[Pasted image 20260510004453.png]]
# 6. 考试中怎么写得高分？

如果题目问：

```text
When does a Turing machine M decide a language L?
```

你写：

```text
M decides L if M halts on every input w, accepting when w ∈ L and rejecting when w ∉ L.
```

如果题目问：

```text
When does a Turing machine M recognise a language L?
```

你写：

```text
M recognises L if M accepts every string in L, and accepts no strings outside L. On strings not in L, M may reject or loop forever.
```

---

# 7. 最容易混淆的一点

注意：

```text
loop forever 不是 reject。
```

如果机器对某个输入一直运行：

```text
它没有给出 no
它只是没有停
```

所以：

```text
reject = 明确拒绝并停机
loop = 不停机，没有答案
```

这就是为什么 recognisable 和 decidable 不一样。