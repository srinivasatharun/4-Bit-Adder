# 4-Bit Adder in VHDL

This project implements a **4-bit ripple-carry adder** in VHDL. The design follows a **hierarchical structural approach** based on the provided lab manual: a `half_adder` is reused to build a `full_adder`, and four `full_adder` blocks are chained together to create the top-level `adder_4bit` module.

## Project Overview

The aim of the design is to add two 4-bit binary numbers with an optional carry input.

### Top-level inputs and outputs
- **A**: 4-bit input vector
- **B**: 4-bit input vector
- **C_in**: 1-bit carry input
- **Sum**: 5-bit output vector

The 5-bit result is necessary because adding two 4-bit numbers can produce a carry-out. For example:

- `1111 + 1111 = 11110`
- decimal `15 + 15 = 30`

## Design Hierarchy

The project is organized in three logical levels:

### 1. Half Adder
The half adder is the basic building block.

It adds two 1-bit inputs:
- `Sum = A xor B`
- `Carry = A and B`

### 2. Full Adder
The full adder adds:
- two input bits `A` and `B`
- one carry input `C_in`

It is built structurally from:
- **2 half adders**
- **1 OR gate**

Operation:
1. The first half adder adds `A` and `B`
2. The second half adder adds the intermediate sum to `C_in`
3. The carry outputs are ORed together to form `C_out`

### 3. 4-Bit Adder
The top-level `adder_4bit` uses **4 full adders** connected in series.

This is called a **ripple-carry adder** because the carry propagates from one stage to the next:
- Stage 0 adds `A(0)`, `B(0)`, and `C_in`
- Stage 1 uses the carry from stage 0
- Stage 2 uses the carry from stage 1
- Stage 3 uses the carry from stage 2
- The final carry becomes `Sum(4)`

## Module Files

A typical project structure may look like this:

```text
.
├── half_adder.vhd
├── full_adder.vhd
├── adder_4bit.vhd
├── adder_4bit_tbw.vhd
└── README.md
```

## Example Structural Behavior

### `full_adder.vhd`
The full adder uses two half adders and combines their carry outputs:

```vhdl
HA1: half_adder port map (A => A, B => B, Sum => S1, Carry => C1);
HA2: half_adder port map (A => S1, B => C_in, Sum => S, Carry => C2);
C_out <= C1 or C2;
```

### `adder_4bit.vhd`
The 4-bit adder chains four full adders:

```vhdl
FA1: full_adder port map (A=>A(0), B=>B(0), C_in=>C_in, S=>Sum(0), C_out=>C(0));
FA2: full_adder port map (A=>A(1), B=>B(1), C_in=>C(0), S=>Sum(1), C_out=>C(1));
FA3: full_adder port map (A=>A(2), B=>B(2), C_in=>C(1), S=>Sum(2), C_out=>C(2));
FA4: full_adder port map (A=>A(3), B=>B(3), C_in=>C(2), S=>Sum(3), C_out=>Sum(4));
```

## How It Works

Each full adder handles one bit position. Because the carry must move from the least significant bit to the most significant bit, the total result is produced step by step.

This makes the design:
- easy to understand
- modular and reusable
- suitable for learning structural VHDL design

## Simulation

A testbench should be used to verify the design with multiple input combinations.

Suggested test cases from the lab include:

| A  | B  | C_in |
|----|----|------|
| 1  | 1  | 0    |
| 2  | 7  | 0    |
| 2  | 7  | 1    |
| 4  | 15 | 0    |
| 4  | 15 | 1    |
| 14 | 15 | 0    |
| 15 | 15 | 0    |
| 15 | 15 | 1    |

These cases help confirm both normal addition and carry propagation.

## FPGA Interface

According to the lab setup, the design can be mapped to a development board with:
- switches for inputs `A`, `B`, and `C_in`
- LEDs for output `Sum`

### Example mapping from the lab
- `A(0..3)` -> `SW0..SW3`
- `B(0..3)` -> `SW4..SW7`
- `C_in` -> `SW8`
- `Sum(0..4)` -> `LD0..LD4`

## Learning Outcomes

This project demonstrates:
- structural VHDL design
- hierarchical module reuse
- construction of a full adder from half adders
- implementation of a ripple-carry adder
- simulation and verification of digital logic

## Tools

- **Language:** VHDL
- **Design Tool:** Xilinx Vivado
- **Target FPGA from lab:** `xc7a100tcsg324-1`

## Summary

This repository shows how a complete 4-bit adder can be built from simple reusable components. It is a good example of modular digital design and a practical introduction to hierarchical hardware description in VHDL.
