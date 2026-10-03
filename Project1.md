<div align="center">

# Mini 4-bit ALU

**A small combinational ALU designed and simulated in Logisim-evolution**

![Tool](https://img.shields.io/badge/Tool-Logisim--evolution-2563EB)
![Design](https://img.shields.io/badge/Design-Combinational%20Logic-16A34A)
![Status](https://img.shields.io/badge/Status-Completed-0F766E)

</div>

---

## Overview

This project is a simple 4-bit Arithmetic Logic Unit (ALU) designed and simulated using **Logisim-evolution**.

The ALU receives two 4-bit operands and performs one of four operations: **AND, OR, XOR, or ADD**. A 2-bit control signal selects which result is sent to the output.

This is my first Digital IC Design portfolio project. The main goal was to practice combinational logic, modular circuit design, verification, and debugging.

### Project Summary

| Item | Description |
|---|---|
| Design | Mini 4-bit ALU |
| Tool | Logisim-evolution |
| Input operands | `A[3:0]`, `B[3:0]` |
| Control input | `SEL[1:0]` |
| Main output | `Y[3:0]` |
| Supported operations | AND, OR, XOR, ADD |

---

## Features

- Two 4-bit input operands
- Four operations: AND, OR, XOR, and ADD
- Operation selection using a 4-to-1 multiplexer
- 4-bit Ripple Carry Adder built from four 1-bit Full Adders
- Separate and reusable subcircuits
- Block-level and integration testing
- Carry-out output for addition

---

## Inputs and Outputs

| Signal | Width | Direction | Description |
|---|---:|---|---|
| `A[3:0]` | 4 bits | Input | First operand |
| `B[3:0]` | 4 bits | Input | Second operand |
| `SEL[1:0]` | 2 bits | Input | Selects the ALU operation |
| `Y[3:0]` | 4 bits | Output | Result of the selected operation |
| `Cout` | 1 bit | Output | Final carry-out from `ADD_4bit`; meaningful during ADD |

---

## Operation Selection

| `SEL[1:0]` | Operation | Function |
|:---:|---|---|
| `00` | AND | `Y = A AND B` |
| `01` | OR | `Y = A OR B` |
| `10` | XOR | `Y = A XOR B` |
| `11` | ADD | `Y = A + B` |

For the ADD operation, the lower four result bits are sent to `Y[3:0]`. The final carry is provided through `Cout`.

---

## Architecture

The operands `A[3:0]` and `B[3:0]` are connected in parallel to four functional blocks:

- `AND_4bit`
- `OR_4bit`
- `XOR_4bit`
- `ADD_4bit`

Each block produces a 4-bit result. The results are connected to a **4-to-1 multiplexer**, and `SEL[1:0]` selects the result sent to `Y[3:0]`.

```text
A[3:0], B[3:0]
        |
        +--> AND_4bit ----+
        +--> OR_4bit -----+
        +--> XOR_4bit ----+--> 4-to-1 MUX --> Y[3:0]
        +--> ADD_4bit ----+
                           ^
                           |
                       SEL[1:0]
```

### Top-Level Circuit

![Mini 4-bit ALU Top-Level](images/alu_top_level.png)

---

## Design Blocks

### AND_4bit

The `AND_4bit` block performs a bitwise AND operation between the two input operands.

```text
Y[3:0] = A[3:0] AND B[3:0]
```

Example: `1010 AND 1101 = 1000`

![4-bit AND Block](images/and_4bit.png)

### OR_4bit

The `OR_4bit` block performs a bitwise OR operation between the two input operands.

```text
Y[3:0] = A[3:0] OR B[3:0]
```

Example: `1100 OR 0011 = 1111`

![4-bit OR Block](images/or_4bit.png)

### XOR_4bit

The `XOR_4bit` block performs a bitwise XOR operation between the two input operands.

```text
Y[3:0] = A[3:0] XOR B[3:0]
```

Example: `1001 XOR 0011 = 1010`

![4-bit XOR Block](images/xor_4bit.png)

### ADD_4bit

The `ADD_4bit` block is a 4-bit Ripple Carry Adder built from four 1-bit Full Adders.

`FA0` processes bit 0, the least significant bit. `FA3` processes bit 3, the most significant bit. The carry-out of each stage is connected to the carry-in of the next stage:

```text
FA0.Cout --> FA1.Cin
FA1.Cout --> FA2.Cin
FA2.Cout --> FA3.Cin
```

The four sum bits are combined to form `SUM[3:0]`. The carry-out from `FA3` becomes the final `Cout`. The initial `Cin` is set to `0` in the current ALU.

![4-bit Ripple Carry Adder](images/add_4bit.png)

### Full Adder 1-bit

The 1-bit Full Adder receives `A`, `B`, and `Cin`. It produces `Sum` and `Cout`.

It is built using:

- 2 XOR gates
- 2 AND gates
- 1 OR gate

The internal logic is:

```text
S1   = A XOR B
C1   = A AND B
Sum  = S1 XOR Cin
C2   = S1 AND Cin
Cout = C1 OR C2
```

![1-bit Full Adder](images/full_adder_1bit.png)

---

## Hierarchical Design

The project is divided into smaller subcircuits:

```text
Mini 4-bit ALU
|
|-- AND_4bit
|-- OR_4bit
|-- XOR_4bit
|
`-- ADD_4bit
    |
    |-- Full Adder 0
    |-- Full Adder 1
    |-- Full Adder 2
    `-- Full Adder 3
```

This structure made each function easier to design and test. It also allowed the same 1-bit Full Adder to be reused four times inside `ADD_4bit`.

Each block was tested separately before being integrated into the complete ALU.

---

## Verification

### Test Strategy

Verification was performed in two stages:

1. Test the AND, OR, XOR, Full Adder, and Ripple Carry Adder blocks separately.
2. Integrate the blocks and test all four operations through `SEL[1:0]`.

The test cases include zero inputs, different bit patterns, and additions with and without carry-out.

### Test Results

| Test | A | B | AND | OR | XOR | ADD | Cout |
|---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 | `1010` | `1100` | `1000` | `1110` | `0110` | `0110` | `1` |
| 2 | `0000` | `0000` | `0000` | `0000` | `0000` | `0000` | `0` |
| 3 | `1010` | `0101` | `0000` | `1111` | `1111` | `1111` | `0` |
| 4 | `1111` | `0001` | `0001` | `1111` | `1110` | `0000` | `1` |
| 5 | `1111` | `1111` | `1111` | `1111` | `0000` | `1110` | `1` |

### Integrated ALU Test

For `A = 1011` and `B = 1101`:

| SEL | Operation | Y | Cout |
|:---:|---|:---:|:---:|
| `00` | AND | `1001` | Not used |
| `01` | OR | `1111` | Not used |
| `10` | XOR | `0110` | Not used |
| `11` | ADD | `1000` | `1` |

---

## Debugging and Lessons Learned

### Symptom

The `ADD_4bit` block produced incorrect results after integration into the complete ALU. Some individual tests appeared correct, but other input combinations failed.

### Root Cause

The error was inside the 1-bit Full Adder subcircuit. Its carry logic was incomplete or connected incorrectly. Because `ADD_4bit` uses four Full Adders, the error affected the carry chain and the final ALU result.

### Debugging Process

I traced the incorrect result through the design hierarchy:

```text
Top-level ALU
    --> ADD_4bit
        --> Full Adder 1-bit
```

Testing the smaller blocks separately helped identify the Full Adder as the source of the error.

### Fix

I corrected the Full Adder carry logic and checked the Ripple Carry connections between all four stages.

### Regression Test

After the fix, I repeated the addition tests:

| A | B | SUM | Cout |
|:---:|:---:|:---:|:---:|
| `1001` | `1001` | `0010` | `1` |
| `1111` | `0001` | `0000` | `1` |
| `1111` | `1111` | `1110` | `1` |

This process helped me understand why both block-level testing and integration testing are important.

---

## Project Structure

```text
mini-4bit-alu/
|
|-- circuit/
|   `-- mini_4bit_alu.circ
|
|-- images/
|   |-- alu_top_level.png
|   |-- and_4bit.png
|   |-- or_4bit.png
|   |-- xor_4bit.png
|   |-- add_4bit.png
|   `-- full_adder_1bit.png
|
`-- README.md
```

---

## What I Learned

Through this project, I practiced:

- Building combinational logic circuits
- Working with 4-bit buses
- Implementing bitwise operations
- Selecting results with a multiplexer
- Building a 1-bit Full Adder from logic gates
- Connecting Full Adders as a Ripple Carry Adder
- Dividing a design into reusable subcircuits
- Testing individual blocks before integration
- Performing integration tests
- Tracing an error from the top-level circuit to a sub-block
- Repeating tests after correcting a design error

---

## Current Scope

This is a learning project designed and simulated in Logisim-evolution.

Its current scope is a small combinational 4-bit ALU with four operations: AND, OR, XOR, and ADD. The project focuses on basic digital logic, hierarchical design, verification, and debugging.

---

<div align="center">

**First Digital IC Design Portfolio Project**

</div>
