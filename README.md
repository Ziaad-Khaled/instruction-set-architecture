# Project Overview

**Processor Design Simulation in Java**

Processor design is the engineering task of creating a processor, a crucial component of computer hardware. The design process includes selecting an instruction set and an execution paradigm, resulting in a microarchitecture. A processor operates by executing lists of instructions, which can include computations, data manipulation, memory access, and program flow control.

In this project, we simulate a fictional processor design and architecture using Java.

## Memory Architecture

### Architecture: Harvard

Harvard Architecture is a digital computer architecture with separate storage and buses (signal paths) for instructions and data. It was developed to overcome the limitations of the Von Neumann Architecture.

### Instruction Memory Size: 1024 * 16

**Instruction Memory**

| Parameter                | Value           |
|--------------------------|-----------------|
| Rows                     | 1024            |
| Bits / Row               | 16              |
| Address Range            | 0 to 210 − 1    |
| Word Size                | 16 bits (2 bytes) |
| Addressable Unit         | Word            |
| Purpose                  | Storage of program instructions |

### Data Memory Size: 2048 * 8

**Data Memory**

| Parameter                | Value           |
|--------------------------|-----------------|
| Rows                     | 2048            |
| Bits / Row               | 8               |
| Address Range            | 0 to 211 − 1    |
| Word Size                | 8 bits (1 byte) |
| Addressable Unit         | Word/Byte       |
| Purpose                  | Storage of data |

### Registers: 66

**Registers**

| Register                  | Size   | Purpose                      |
|---------------------------|--------|------------------------------|
| General-Purpose Registers (GPRS) R0 to R63 | 8 bits | General data storage and manipulation |
| Status Register (SREG)    | 8 bits | Flags for processor status   |
| Program Counter (PC)      | 16 bits | Address of the current instruction |

**Status Register (SREG) Flags**

| Flag          | Bit   | Description                                                |
|---------------|-------|------------------------------------------------------------|
| Carry Flag (C)| 0     | Indicates carry or borrow in arithmetic operations         |
| Overflow Flag (V)| 1  | Indicates overflow in signed arithmetic operations        |
| Negative Flag (N)| 2  | Indicates negative result in arithmetic or logic operations|
| Sign Flag (S) | 3     | Indicates expected sign of the result (not the actual sign)|
| Zero Flag (Z) | 4     | Indicates that the result of an operation was zero        |

## Instruction Set Architecture

### Instruction Size: 16 bits

### Instruction Types: 2

#### R-Format

| OPCODE | R1  | R2  |
|--------|-----|-----|
| 4 bits | 6 bits | 6 bits |

#### I-Format

| OPCODE | R1  | IMMEDIATE |
|--------|-----|-----------|
| 4 bits | 6 bits | 6 bits |

### Instruction Count: 12

The opcodes range from 0 to 11 according to the instructions order in the following table:

| Name                | Mnemonic | Type | Format | Operation                                   |
|---------------------|----------|------|--------|---------------------------------------------|
| Add                 | ADD      | R    | R-Format | R1 = R1 + R2                            |
| Subtract            | SUB      | R    | R-Format | R1 = R1 - R2                            |
| Multiply            | MUL      | R    | R-Format | R1 = R1 * R2                            |
| Load Immediate      | LDI      | I    | I-Format | R1 = IMM                                 |
| Branch if Equal Zero| BEQZ     | I    | I-Format | IF(R1 == 0) { PC = PC+IMM }             |
| And                 | AND      | R    | R-Format | R1 = R1 & R2                            |
| Or                  | OR       | R    | R-Format | R1 = R1 | R2                            |
| Jump Register       | JR       | R    | R-Format | PC = R1 || R2                           |
| Shift Left Circular | SLC      | I    | I-Format | R1 = (R1 << IMM) | (R1 >> 32 - IMM)   |
| Shift Right Circular| SRC      | I    | I-Format | R1 = (R1 >> IMM) | (R1 << 32 - IMM)   |
| Load Byte           | LB       | I    | I-Format | R1 = MEM[ADDRESS]                       |
| Store Byte          | SB       | I    | I-Format | MEM[ADDRESS] = R1                       |

**Note:** "||" symbol indicates concatenation (0100 || 1100 = 01001100).

### Status Register (SREG) Flags

- The Carry flag (C) is updated after ADD, SUB, and MUL instructions.
- The Overflow flag (V) is updated after ADD and SUB instructions.
- The Negative flag (N) is updated after ADD, SUB, MUL, AND, OR, SLC, and SRC instructions.
- The Sign flag (S) is updated after ADD and SUB instructions.
- The Zero flag (Z) is updated after ADD, SUB, MUL, AND, OR, SLC, and SRC instructions.

## Datapath

### Stages: 3

- All instructions pass through all 3 stages.
- **Instruction Fetch (IF):** Fetches the next instruction from main memory using the PC.
- **Instruction Decode (ID):** Decodes the instruction, calculates the next PC, and reads required operands from the register file.
- **Execute (EX):** Executes the instruction, performs ALU operations, and handles memory access. It writes results back to the register file.

### Pipeline: 3 instructions (maximum) running in parallel

- Number of clock cycles: 3 + ((n − 1) ∗ 1), where n = number of instructions
  - For example, in a program with 7 instructions: 3 + (6 ∗ 1) = 9 clock cycles
  #### Pipeline Execution Example

  To better visualize how instructions progress through the pipeline, consider the following execution example:
      
          | Package 2 Pipeline | Instruction Fetch (IF) | Instruction Decode (ID) | Execute (EX) |
          |--------------------|------------------------|--------------------------|--------------|
          | Cycle 1            | Instruction 1          |                        |              |
          | Cycle 2            | Instruction 2          | Instruction 1            |              |
          | Cycle 3            | Instruction 3          | Instruction 2            | Instruction 1 |
          | Cycle 4            | Instruction 4          | Instruction 3            | Instruction 2 |
          | Cycle 5            | Instruction 5          | Instruction 4            | Instruction 3 |
          | Cycle 6            | Instruction 6          | Instruction 5            | Instruction 4 |
          | Cycle 7            | Instruction 7          | Instruction 6            | Instruction 5 |
          | Cycle 8            |                        | Instruction 7            | Instruction 6 |
          | Cycle 9            |                        | Instruction 7            |              |
            
  This execution pattern optimizes instruction processing by allowing multiple instructions to advance through different stages concurrently, thereby enhancing overall processor performance.
      
