# COMP0017 Lecture 4 — Mapping Reduction
**Course:** Computability and Complexity Theory  
**Lecturer:** Fabio Zanasi  
**Topic:** Undecidability & Recognisability via Mapping Reduction  

---

## 🧩 1. Recap: From the Halting Problem

We already proved:

1. **HALT is undecidable.**  
   No algorithm can decide whether an arbitrary program halts on a given input.

2. **HALT⁻ (the complement) is unrecognisable.**  
   No Turing machine can enumerate all programs that do *not* halt.

---

## 🧭 2. Motivation: A Reusable Proof Technique

We want a systematic way to prove new problems **undecidable** or **unrecognisable**  
by *reusing* what we already know.

> Key idea: **reduce** one problem to another.  
> If we can translate instances of a known hard problem (like HALT) into another problem \(L\),  
> then \(L\) must be at least as hard.

---

## 🔁 3. Definition: Mapping Reduction

Given two languages \(L', L \subseteq \Sigma^*\):

$$
L' \le L
$$

means that there exists a total computable function

$$
f : \Sigma^* \to \Sigma^*
$$

such that

$$
x \in L' \iff f(x) \in L
$$

**Interpretation:**  
The function \(f\) transforms instances of \(L'\) into instances of \(L\).  
Thus, \(L\) is *at least as difficult as* \(L'\).

---

## 🧠 4. Basic Theorems

### Theorem 1
If \(L' \le L\) and \(L\) is **decidable**, then \(L'\) is **decidable**.

### Corollary 1
If \(L' \le L\) and \(L'\) is **undecidable**, then \(L\) is **undecidable**.

> ✅ To prove **L** undecidable,  
> it is enough to show **HALT ≤ L**.

---

## ⚙️ 5. Example 1 — Empty Tape Halting (ETH)

**Definition**

$$
ETH = \{ x \mid x = \text{code}(M) \text{ and } M \text{ halts on } \varepsilon \}
$$

**Goal:** Show ETH is undecidable.

**Proof sketch**

Reduce \(HALT \le ETH\).

Construct \(f(\langle y, x \rangle) = \text{code}(M_{y,x})\),  
where \(M_{y,x}\) behaves as:

1. If input ≠ ε → loop forever.  
2. If input = ε → write x, then simulate $(M_y(x))$.

Then

$$
\langle y, x \rangle \in HALT \iff f(\langle y,x \rangle) \in ETH
$$

Hence, ETH is undecidable.

---

## 🧮 6. Example 2 — Full Language Problem (FL)

**Definition**

$$
FL = \{ x \mid x = \text{code}(M) \text{ and } M \text{ halts on all inputs} \}
$$

**Goal:** Show FL is undecidable.

**Reduction:** \(HALT \le FL\).

Construct $(f(\langle y,x\rangle) = \text{code}(M_{y,x})), where (M_{y,x})$:

1. Erases its input.  
2. Writes x on tape.  
3. Simulates \(M_y(x)\).

Then

$$
\langle y, x \rangle \in HALT \iff f(\langle y,x\rangle) \in FL
$$

Therefore, FL is undecidable.

---

## 🧩 7. Example 3 — Equivalence Problem (EQ)

**Definition**

$$
EQ = \{ \langle y, x \rangle \mid M_y, M_x \text{ compute the same partial function} \}
$$

**Goal:** Show EQ is undecidable.

**Reduction:** \(FL \le EQ\).

Construct \(f(z) = \langle \text{code}(M_1), \text{code}(M_2) \rangle\), where:

- \(M_1\): runs \(M_z\) on its input; outputs 1 if M halts, loops otherwise.  
- \(M_2\): outputs 1 on all inputs.

Then

$$
z \in FL \iff f(z) \in EQ
$$

Thus EQ is undecidable.

---

## 🚫 8. EQ is Not Recognisable

We already know $(HALT⁻)$ is **unrecognisable**.

To show EQ is unrecognisable, reduce $(HALT⁻ \le EQ)$.

### Construction of \(f\)

On input \(\langle y, x\rangle\):

- If \(y \ne \text{code}(M)\): pick any fixed machine \(M′\), set  
  \(f(\langle y,x\rangle) = \langle \text{code}(M′), \text{code}(M′) \rangle\).
- Else \(y = \text{code}(M)\):
  - Construct \(M_1\): runs \(M(x)\), halts if \(M\) halts, loops otherwise.  
  - Construct \(M_2\): loops on all inputs.

Then

$$
\langle y,x\rangle \in HALT⁻ \iff f(\langle y,x\rangle) \in EQ
$$

Hence, EQ is **not recognisable**.

---

## 📐 9. Properties of Mapping Reduction

| Property          | Description                                           |
| ----------------- | ----------------------------------------------------- |
| **Reflexive**     | $(L \le L)$                                           |
| **Transitive**    | $(L_1 \le L_2)$ and $(L_2 \le L_3)$ ⇒ $(L_1 \le L_3)$ |
| **Not symmetric** | $(L_1 le L_2) does *not* imply (L_2 \le L_1)$         |
| **Complement**    | $(L_1 \le L_2 \iff L_1⁻ \le L_2⁻)$                    |

From the last property:  
\(HALT \le FL \le EQ \Rightarrow HALT⁻ \le EQ⁻\) ⇒ \(EQ⁻\) is not recognisable.

---

## 🏗️ 10. Reduction and Decidability

**Theorem 2:**  
If \(L\) is non-trivial (i.e. \(L \ne \Sigma^*, L \ne \emptyset\)), then  
for any decidable \(L′\), we have \(L′ \le L\).

**Proof idea:**  
Pick \(x \in L\) and \(y \notin L\), then define

$$
f(z) =
\begin{cases}
x, & z \in L' \\
y, & z \notin L'
\end{cases}
$$

Since \(L′\) is decidable, \(f\) is computable.  
Thus \(z \in L′ \iff f(z) \in L.\)

---

## 🧭 11. A Hierarchy of Problems

| Category | Property | Example |
|-----------|-----------|----------|
| **Decidable** | Algorithm exists | Regular languages, CFG membership |
| **Undecidable but recognisable** | Accepts some instances | HALT |
| **Unrecognisable but complement recognisable** | Only co-recognisable | HALT⁻ |
| **Neither recognisable nor co-recognisable** | Hardest level | EQ |

Relations:

$$
HALT \le FL \le EQ, \quad HALT⁻ \le EQ⁻
$$

and $(HALT \not\le HALT⁻$).

---

## 🔮 12. Beyond Mapping: Turing Reducibility

**Mapping reducibility** requires a one-shot computable translation \(f\).  
**Turing reducibility** is more general.

> \(L′\) is *Turing reducible* to \(L\)  
> if \(L′\) can be decided with the help of an **oracle** for \(L\).

- Mapping reducibility ⇒ Turing reducibility.  
- But not vice versa.

**Example:**  
\(HALT⁻\) is not mapping-reducible to \(HALT\),  
but it *is* Turing-reducible to \(HALT\)  
(since an oracle for HALT can decide \(HALT⁻\)).

---

## 🎓 13. Takeaways

- **Mapping reduction** lets us *transfer* undecidability and unrecognisability.  
- To show \(L\) undecidable: prove $(HALT \le L)$.  
- To show \(L\) unrecognisable: prove $(HALT⁻ \le L)$.  
- This creates a **hierarchy of computational hardness**.  
- **Turing reduction** generalises mapping reduction with oracle power.

---

> 💬 *“In computability theory, proving a problem undecidable is not the end.  
> It’s the beginning of understanding **why** it is hard — and how other problems inherit that hardness.”*
