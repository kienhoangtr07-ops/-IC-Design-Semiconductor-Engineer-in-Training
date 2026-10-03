
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
