# COMP0017 — Computability and Complexity Theory

---

## 🧭 Lecture Overview

- Lecture 1: **Turing Machines, Decidability, and Computability**
- Lecture 2: **Church–Turing Thesis & Equivalent Models**

---

## 🧩 Lecture 1 — The Foundations of Computability

### 1. Motivation

> What can — and cannot — be computed?

The course explores the **limits of algorithms** through formal models like the **Turing Machine (TM)**.

---

### 2. Decision Problems as Formal Languages

Every decision problem can be expressed as a **language** \( L \subseteq Σ^* \):
- \( x ∈ L \) → accept ("YES")
- \( x ∉ L \) → reject ("NO")

---

### 3. Turing Machine Definition

A **Turing Machine** \( \mathcal{M} = \langle Σ, Q, q_0, H, δ \rangle \) is a 5-tuple:

| Symbol | Meaning |
|---------|----------|
| Σ | Tape alphabet |
| Q | Finite set of states |
| q₀ | Initial state |
| H | Halting states |
| δ | Transition function \( (Q \setminus H) × Σ → Q × Σ × \{←, →\} \) |

---

### 4. Decidability & Recognisability

| Category | Description | TM behaviour |
|-----------|--------------|--------------|
| **Decidable** | TM halts on all inputs | Always halts with “yes/no” |
| **Recognisable (semi-decidable)** | TM halts on accepted inputs | Loops on others |

Example:  
Language of all programs that **eventually halt** is *recognisable* but not *decidable*.

---

### 5. Computability & Models of Computation

- Early models: λ-calculus (Church), recursive functions (Gödel), and Turing Machines (Turing).  
- All proved **equivalent** in computational power.

---

### 6. Church–Turing Thesis (Preview)

> “Every effectively computable function can be computed by a Turing Machine.”

This thesis will be the foundation for Lecture 2.

---

### 🧠 Key Takeaways — Lecture 1
- Computability = What can be solved algorithmically.
- Turing Machines formalise the notion of an **algorithm**.
- Some problems are **recognisable** but **undecidable**.
- The **Church–Turing Thesis** unifies all models of computation.

---

## 🧮 Lecture 2 — Church–Turing Thesis & Model Robustness

### 1. The Church–Turing Thesis

> Any problem solvable by a well-defined, step-by-step procedure  
> can be solved by a Turing Machine.

- It’s a **conjecture**, not a theorem.
- Supported by the equivalence of many computational models:
  - Enhanced TMs (multi-tape, non-deterministic, probabilistic…)
  - Register machines
  - High-level languages (Python, Java, C)
  - Physical computers
  - Even quantum computers

---

### 2. Why Turing Machines Matter

1. Provide a **rigorous mathematical foundation** of “algorithm”.
2. Can **simulate any computer** → basis for reasoning about computability.

> ⚠️ TMs are inefficient but conceptually universal.

---

### 3. Robustness of Definition

A definition is **robust** if it is invariant under changes.

> Multiple variants of TMs have identical expressive power:
> - Multi-tape  
> - Multi-head  
> - Two-way infinite tape  
> - Non-deterministic  
> - Probabilistic / Quantum

---

### 4. Multi-Tape Turing Machines

**Idea:**  
Each tape has its own head, all controlled by one state machine.

**Example:**  
Check if string `x` is a palindrome:
1. Copy input onto second tape.
2. Compare left-to-right vs right-to-left.

**Theorem:**  
Multi-tape TMs ≡ Single-tape TMs (same computational power).

Proof sketch:
- Encode all tapes into one via `#` delimiters.
- Simulate multiple steps through scanning and shifting.

---

### 5. Non-deterministic Turing Machines (NDTMs)

Allow **multiple possible transitions** from a configuration.  
→ Computation forms a **tree**, not a single path.

**Accept rule:**  
If **any** branch reaches an accepting state → accept.

**Example:**  
Check if a number `n` is *non-prime*:
- Non-deterministically guess an `m` such that `1 < m < n`.
- Accept if `n mod m == 0`.

---

**Theorem:**  
NDTMs ≡ Deterministic TMs (same expressiveness, different efficiency).

Proof sketch:
- Deterministic TM systematically explores NDTM’s computation tree.

---

### 6. TMs vs Programming Languages

> Despite appearance, high-level languages have no more power than TMs.

We introduce the **WHILE language** (a minimal imperative language):

#### Syntax (informal)
```while
X := 0
X := s(Y)   // successor
X := p(Y)   // predecessor
while X ≠ Y do PROGRAM
PROGRAM1 ; PROGRAM2
````

#### Semantics

A WHILE program computes a (partial) function ( \mathbb{N}^k → \mathbb{N}^k ).

#### Example

```while
begin
  begin Z := s(X); Z := p(Z) end;
  begin
    U := 0;
    while U ≠ Y do
      begin Z := s(Z); U := s(U) end
  end
end
```

→ Computes **Z = X + Y**

---

### 7. Equivalence Theorem

> A (partial) function is computable by a WHILE-program  
> **iff** it is computable by a Turing Machine.

**Proof idea:**  
Induction on program structure:

- Base: simple assignments.
    
- Inductive:
    
    - Sequential composition → cascade of TMs.
        
    - While loops → iterative TM simulation.
        

---

### 🧠 Key Takeaways — Lecture 2

- Church–Turing Thesis links all computational models.
    
- Turing Machines are **robust** under extensions.
    
- WHILE language ↔ Turing Machines: same computability class.
    
- Efficiency differs, expressiveness identical.
    