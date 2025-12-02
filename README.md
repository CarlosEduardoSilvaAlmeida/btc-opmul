# Implementation and Analysis of OP_MUL (0x95) in Bitcoin Core

This repository documents the design, implementation, testing, and analysis of a new Bitcoin Script opcode:

**OP_MUL = 0x95**  
**Operation:** Signed 32-bit integer multiplication with explicit overflow validation.

The goal of this work is to extend the Bitcoin Core Script interpreter with a mathematically sound and consensus-compatible multiplication primitive, implemented according to the canonical `CScriptNum` numeric rules.

This repository contains **only documentation, patch files, auxiliary scripts, and explanatory material**.  
The **Bitcoin Core source code is not included**, following licensing requirements and academic best practices.

---

## 1. Research Objective

This work has two complementary objectives:

### 1.1 Technical Contribution

Introduce a deterministic and safely constrained multiplication opcode into Bitcoin Script, respecting:

- strict overflow semantics,  
- the 32-bit signed integer domain (`[-2³¹, 2³¹ − 1]`),  
- the `CScriptNum` canonical numeric format (4-byte limit).

The implementation follows the structure and conventions of the Bitcoin Core Script interpreter.

### 1.2 Pedagogical Contribution

Provide a clear and reproducible case study showing:

- how new opcodes can be integrated into Bitcoin Core,  
- how to design and validate arithmetic semantics for consensus-critical software,  
- how to construct unit tests, script tests, and functional tests in a controlled environment.

---

## 2. Semantics of OP_MUL

`OP_MUL` pops the top two stack elements, interprets them as 32-bit signed integers, multiplies them in 64-bit space, checks the result against the valid domain, and either pushes the result or fails the script.

### 2.1 Formal Definition

Let:

- `x1`, `x2` ∈ ℤ₃₂ (signed 32-bit domain)  
- multiplication performed in ℤ₆₄  
- `p = x1 × x2`  

Then:

```text
If -2³¹ ≤ p ≤ 2³¹ − 1:
    push(p)
Else:
    fail with SCRIPT_ERR_MUL
```

This behavior mirrors the deterministic integer semantics already used by other arithmetic opcodes in Bitcoin Script.

---

## 3. Execution Flow of OP_MUL

```text
       ┌───────────────────────────────────────────────────┐
       │                 Bitcoin Script Stack              │
       └───────────────────────────────────────────────────┘

                          Initial State
                          --------------
                         [..., x1, x2]

                                  │
                                  ▼

                         OP_MUL Dispatch
                         ---------------
                       interpreter.cpp:

                       case OP_MUL:
                           pop x2
                           pop x1
                           parse as CScriptNum(4 bytes)
                           promote to int64
                           p = x1 * x2
                           if p outside int32:
                               return SCRIPT_ERR_MUL
                           push(p)
                           continue

                                  │
                 ┌────────────────┴────────────────┐
                 │                                 │
                 ▼                                 ▼
         If p fits 32 bits               If p overflows 32 bits
         ------------------              ------------------------
         Script continues               Script evaluation fails
         [..., p]                        SCRIPT_ERR_MUL
```

This diagram highlights stack data flow, validation points, and consensus-relevant failure behavior.

---

## 4. Repository Structure

The repository excludes the Bitcoin Core source tree and instead provides:

- **`patches/op_mul.diff`**  
  A complete patch ready to apply to Bitcoin Core `master`.

- **`docs/`**  
  GitHub Pages documentation containing:
  - design rationale,  
  - development environment setup,  
  - testing methodology,  
  - theoretical notes.

- **`scripts/`**  
  PowerShell and Bash utilities to run functional tests.

- **`notes/`**  
  A development log summarizing the implementation process.

This separation preserves clarity, reproducibility, and licensing compliance.

---

## 5. Reproducing the Implementation

### 5.1 Clone Bitcoin Core

```bash
git clone https://github.com/bitcoin/bitcoin.git
```

### 5.2 Apply the patch

```bash
git apply patches/op_mul.diff
```

### 5.3 Build Bitcoin Core

Follow the official build documentation for your platform.  
For Windows (MSVC + CMake), see `docs/bitcoin-core-setup.md`.

### 5.4 Run Tests

```bash
python test/functional/script_op_mul.py
python test/functional/op_mul_numeric_overflow.py
```

---

## 6. Testing Summary

The implementation is validated through the following layers:

### 6.1 C++ Unit Tests

- Boundary cases:  
  - `INT32_MAX × 1` accepted  
  - `INT32_MAX × 2` rejected (overflow)  
- Positive, negative, and mixed-sign multiplication  
- Zero multiplication

### 6.2 Python Functional Tests

- Correct arithmetic behavior  
- Deterministic rejection of overflow and underflow  
- P2SH and interpreter-level failure propagation  
- Graceful skipping on environments lacking wallet RPC

The test suite is deterministic and reproducible across platforms.

---

## 7. Authors

- **Alberto Rômulo Nunes Campelo** (@romulocampelo)  
- **Antonio Barros Coelho** (@toninhobc)  
- **Carlos Eduardo da Silva Almeida** (@CarlosEduardoSilvaAlmeida)  
- **Giovanni Nogueira Catelli** (@Gigogas)  
- **Pedro Corbelino Melges Barrêto Sales** (@PedroCorbs)

---

## 8. License (MIT)

```text
MIT License

Copyright (c) 2025

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

[full license text unchanged]
```

---

## 9. Academic Significance

This work demonstrates:

- how to design safe arithmetic semantics for consensus-critical software,  
- how disabled opcodes can be reintroduced responsibly in controlled environments,  
- how to construct multi-layer testing pipelines in Bitcoin Core,  
- how overflow-sensitive arithmetic can be analyzed in decentralized systems.

The methodology follows established practices in secure systems engineering and reproducible research.
