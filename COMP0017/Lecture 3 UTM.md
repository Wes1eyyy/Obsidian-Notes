## ⚙️ Lecture 3 — Universal Turing Machines & Undecidability


### 1. From Programs to Universal Programs

> Can one machine simulate _any_ other machine?

**Observation:**

- Programs are data.
    
- A program can take another program as input.
    
- Hence: possible to design a **universal program**.
    

Examples:

- OS (chooses which program to run)
    
- Python interpreter
    
- Virtual machines, compilers
    

---

### 2. Universal Turing Machine (UTM)

> A Turing Machine capable of simulating _any_ other TM.

Input: `y = code(ℳ) code(x)`

Output: same result as ℳ(x).

If ℳ halts on x → UTM halts with same output.  
If ℳ loops → UTM loops.

---

### 3. Encoding a Turing Machine

Each TM can be represented as a binary string via a **Gödel encoding**:

```
code(qi) = 1^(i+1)
code(σj) = 1^(j+1)
code(⟨qi, σn, qj, σm, σo⟩) = code(qi)0code(σn)0code(qj)0code(σm)0code(σo)0
```

Combine all tuples to form `code(δ)`  
→ Entire TM = concatenation of these binary segments.

✅ It’s **decidable** to check if a binary string encodes a valid TM.

---

### 4. UTM Construction

A **three-tape TM**:

|Tape|Content|Description|
|---|---|---|
|1|Encoded tape of ℳ|Simulated data|
|2|`code(ℳ)`|The program|
|3|`code(q)`|Current state|

Simulation steps:

1. Verify input is valid (`code(ℳ)code(x)`).
    
2. Copy `code(ℳ)` to tape 2.
    
3. Initialise tapes and heads.
    
4. Iteratively:
    
    - Find the rule matching `(current_state, scanned_symbol)`.
        
    - Update symbol & head position.
        
    - Update current state.
        
    - Halt when ℳ halts.
        

---

### 5. UTM Self-Reference

> A Universal TM can take **its own code** as input:  
> `ℳ_U(code(ℳ_U))`

This self-reference forms the basis of **undecidability proofs**.

---

### 6. The Halting Problem

**Definition:**

```
HALT = {⟨y, x⟩ | y = code(ℳ) and ℳ halts on x}
```

**Theorem:**  
HALT is **recognisable but not decidable**.

#### Proof Sketch

- Recognisable: simulate ℳ on x; if halts, accept.
    
- Not decidable: assume TM ℳ_H decides HALT.  
    Construct ℳ':
    
    ```
    ℳ'(z):
      run ℳ_H(⟨z,z⟩)
      if accepts → loop
      else → halt
    ```
    
    Then consider ℳ'(code(ℳ')) → contradiction.
    

---

### 7. Complement of HALT

```
HALT⁻ = {⟨y, x⟩ | y ≠ code(ℳ) or ℳ does not halt on x}
```

**Theorem:**  
If both HALT and HALT⁻ were recognisable, HALT would be decidable.  
Hence, HALT⁻ is **not recognisable**.

---

### 8. Decidability Hierarchy

|Category|Property|Example|
|---|---|---|
|**Decidable**|Halts on all inputs|e.g. Palindrome checker|
|**Recognisable (semi-decidable)**|Halts on “yes” inputs only|HALT|
|**Unrecognisable**|Halts on neither|HALT⁻|

---

### 🧠 Key Takeaways — Lecture 3

- Universal Turing Machine = theoretical ancestor of modern computers.
    
- Programs = data → self-reference possible.
    
- The **Halting Problem** proves limits of algorithmic reasoning.
    
- Complement of HALT unrecognisable ⇒ boundary of computation.
    

---

## 📚 Summary of Lecture 1–3

|Theme|Concept|Key Theorem|
|---|---|---|
|Computation|Turing Machines define “algorithm”|TM formalism|
|Robustness|Models equivalent in power|Church–Turing Thesis|
|Universality|One machine can simulate all|Existence of UTM|
|Limits|Some problems unsolvable|Halting Problem|

---

> “Turing’s 1936 paper not only defined the algorithm —  
> it defined the limits of human thought as computable action.”
