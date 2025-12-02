---
title: OP_MUL for Bitcoin Core
layout: default
---
<link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">

# OP_MUL for Bitcoin Core

This site documents an implementation of a new opcode in Bitcoin Core:

```text
OP_MUL = 0x95
```
The opcode performs signed 32-bit integer multiplication in Bitcoin Script, with explicit overflow detection and strict overflow handling.

Contents

Design and rationale

Environment and build instructions

Testing strategy and test cases

Project status

Implementation: complete and tested locally.

Unit tests (C++): all Bitcoin Core tests pass, including a dedicated overflow test.

Functional tests (Python):

script_op_mul.py

op_mul_numeric_overflow.py

Overflow behaviour:

If the product fits in 32 bits: script succeeds.

Otherwise: script fails with SCRIPT_ERR_MUL.

For details, see the pages linked above.