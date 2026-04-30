![[Pasted image 20260430001950.png]]
这题要做的是：把原 grammar 预处理成更适合 **LL(1)** 的形式。核心操作有两个：

```text
1. left factor
2. eliminate left recursion
```

原 grammar 是：

```text
S → C | C ; S

C → if { S }
  | for G { S }

G → id < id
  | id > id
  | ! G
  | ( G )
  | G and G
  | G and id
```

---

# Step 1：left factor `S`

原来：

```text
S → C | C ; S
```

两个 production 都以 `C` 开头，所以要 left factor。

改成：

```text
S  → C S'
S' → ; S | ε
```

意思是：

```text
S 可以是一个 C
也可以是 C 后面接 ; S
```

---

# Step 2：处理 `G` 的左递归

原来 `G` 有左递归：

```text
G → G and G
G → G and id
```

这两条都是：

```text
G → G α
```

会导致 top-down parser 无限递归。

先把非左递归部分抽出来，记作 `B`：

```text
B → id < id
  | id > id
  | ! G
  | ( G )
```

那么 `G` 可以理解成：

```text
G → B
  | B and G
  | B and id
  | ...
```

也就是：

```text
G → B G'
G' → and H G' | ε
```

其中 `H` 表示 `and` 后面可以接：

```text
G 或 id
```

---

# Step 3：left factor `B`

`B` 中有：

```text
B → id < id
  | id > id
```

两个都以 `id` 开头，所以 left factor：

```text
B → id R
  | ! G
  | ( G )

R → < id
  | > id
```

---

# Step 4：处理 `and` 后面的部分 `H`

因为原 grammar 允许：

```text
G and G
G and id
```

所以 `and` 后面可以是一个完整的 `G`，也可以是单独的 `id`。

但是如果后面以 `id` 开头，会有歧义：

```text
id
id < id
id > id
```

所以我们写成：

```text
H  → id H'
   | ! G
   | ( G )

H' → < id
   | > id
   | ε
```

其中：

```text
H' → ε
```

表示 `and id` 这种情况。

---

# Final grammar

所以最终完整 grammar 可以写成：

```text
S  → C S'
S' → ; S | ε

C  → if { S }
   | for G { S }

G  → B G'

G' → and H G'
   | ε

B  → id R
   | ! G
   | ( G )

R  → < id
   | > id

H  → id H'
   | ! G
   | ( G )

H' → < id
   | > id
   | ε
```

---

# 考试里可以这样写操作说明

```text
left factor S

S  → C S'
S' → ; S | ε
```

```text
eliminate left recursion on G by introducing G'

G  → B G'
G' → and H G' | ε
```

```text
left factor the id-starting alternatives in B

B → id R | ! G | ( G )
R → < id | > id
```

```text
left factor the expression after and

H  → id H' | ! G | ( G )
H' → < id | > id | ε
```

---

# 快速记忆

这题最关键的三件事：

```text
S → C | C;S
```

有公共前缀 `C`，所以 left factor。

```text
G → G and G | G and id
```

有左递归，所以消除左递归。

```text
id < id | id > id
```

有公共前缀 `id`，所以 left factor。