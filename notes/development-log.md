# Development Log for OP_MUL

This document records the primary steps undertaken during the design, implementation, and testing of the `OP_MUL` opcode for Bitcoin Core.  
It serves as a technical development diary, summarizing relevant decisions, environment details, and progressive milestones.

---

## Environment

- **Operating System:** Windows 11  
- **Toolchain:** MSVC 2022 + CMake  
- **Bitcoin Core Source:** Clean build from the `master` branch  
- **Binary Output Directory:**  
  `C:\Dev\btc-opmul\bitcoin\build\bin\Release`

---

## High-Level Milestones

- Implemented `OP_MUL = 0x95` in `src/script/interpreter.cpp`.
- Added new error code and human-readable message in:
  - `src/script/script_error.h`
  - `src/script/script.cpp`
- Updated `script_tests.json` to remove legacy test cases referencing the obsolete `MUL` opcode.
- Created C++ unit tests validating correct arithmetic behaviour and strict overflow handling.
- Implemented functional tests:
  - `test/functional/script_op_mul.py`
  - `test/functional/op_mul_numeric_overflow.py`
- Verified end-to-end behaviour:
  - valid multiplications are accepted,
  - overflow conditions trigger `SCRIPT_ERR_MUL`,
  - functional tests automatically skip wallet-dependent scenarios when needed.

---

## Notes

This development log summarizes the essential steps without reproducing full code listings.  
For a comprehensive explanation of rationale, design, semantics, and testing procedures, refer to the corresponding documents in `/docs`.
