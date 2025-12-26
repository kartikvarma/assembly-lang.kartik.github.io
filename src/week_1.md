# Week 1: Computer Architecture & Number Systems

## Overview
This week introduces the fundamental concepts needed for assembly programming: number systems, bitwise operations, and CPU architecture.

---

## Day 1-2: Number Systems

### Learning Objectives
- Master binary, decimal, and hexadecimal conversions
- Understand why computers use these systems
- Develop quick conversion skills

### Theory

#### Binary (Base-2)
- Uses only 0 and 1
- Each position represents a power of 2
- Example: `1011` = 1×2³ + 0×2² + 1×2¹ + 1×2⁰ = 8 + 0 + 2 + 1 = 11

#### Hexadecimal (Base-16)
- Uses 0-9 and A-F (A=10, B=11, ... F=15)
- Each hex digit represents 4 bits
- Example: `0x2F` = 2×16¹ + 15×16⁰ = 32 + 15 = 47
- Binary equivalent: `0010 1111`

#### Conversion Techniques

**Binary to Decimal:**
```
1101 = 1×8 + 1×4 + 0×2 + 1×1 = 13
```

**Decimal to Binary (Division Method):**
```
13 ÷ 2 = 6 remainder 1
6 ÷ 2 = 3 remainder 0
3 ÷ 2 = 1 remainder 1
1 ÷ 2 = 0 remainder 1
Read upwards: 1101
```

**Binary to Hex (Group by 4):**
```
11010110
= 1101 0110
= D    6
= 0xD6
```

**Hex to Binary:**
```
0xA3
= A    3
= 1010 0011
```

### Practical Exercises

#### Exercise 1: Basic Conversions
Convert these numbers:
1. Decimal 255 → Binary → Hex
2. Binary 11010110 → Decimal → Hex
3. Hex 0xDEADBEEF → Binary → Decimal
4. Decimal 1024 → Binary → Hex
5. Binary 10101010 → Decimal → Hex

#### Exercise 2: Quick Mental Math
Practice these patterns:
- Powers of 2: 1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024...
- Common hex values: 0xF = 15, 0xFF = 255, 0xFFFF = 65535
- Bit positions: Bit 0 = 1, Bit 3 = 8, Bit 7 = 128

#### Exercise 3: Real-World Application
1. An IPv4 address is 192.168.1.1. Convert each octet to binary and hex.
2. A color code is #FF5733. What are the RGB values in decimal?
3. A memory address is 0x1000. What is it in binary and decimal?

### Daily Practice
- Spend 15 minutes daily on random conversions
- Use calculator only to check answers
- Focus on pattern recognition

---

## Day 3-4: Bitwise Operations

### Learning Objectives
- Understand AND, OR, XOR, NOT operations
- Learn bit shifting and rotation
- Apply bitwise operations to practical problems

### Bitwise Operators

#### AND Operation (&)
```
Truth Table:
0 & 0 = 0
0 & 1 = 0
1 & 0 = 0
1 & 1 = 1

Example:
  1010
& 1100
------
  1000
```
**Use Case:** Masking bits, checking if bit is set

#### OR Operation (|)
```
Truth Table:
0 | 0 = 0
0 | 1 = 1
1 | 0 = 1
1 | 1 = 1

Example:
  1010
| 1100
------
  1110
```
**Use Case:** Setting bits, combining flags

#### XOR Operation (^)
```
Truth Table:
0 ^ 0 = 0
0 ^ 1 = 1
1 ^ 0 = 1
1 ^ 1 = 0

Example:
  1010
^ 1100
------
  0110
```
**Use Case:** Toggling bits, simple encryption, swap without temp variable

#### NOT Operation (~)
```
~1010 = 0101 (flips all bits)
```
**Use Case:** Inverting flags, creating masks

#### Shift Left (<<)
```
1010 << 1 = 10100
(Multiply by 2)

1010 << 2 = 101000
(Multiply by 4)
```

#### Shift Right (>>)
```
1010 >> 1 = 0101
(Divide by 2)

1010 >> 2 = 0010
(Divide by 4)
```

### Practical Applications

#### Application 1: Checking if Bit is Set
```
Check if bit 3 is set in value:
value & (1 << 3)
If result != 0, bit is set

Example:
value = 0b1010 (10 in decimal)
mask = 1 << 3 = 0b1000
value & mask = 0b1010 & 0b1000 = 0b1000 (not zero, so bit 3 is set)
```

#### Application 2: Setting a Bit
```
Set bit 3:
value | (1 << 3)

Example:
value = 0b1010
mask = 1 << 3 = 0b1000
value | mask = 0b1010 | 0b1000 = 0b1010 (bit already set)

value = 0b0010
value | mask = 0b0010 | 0b1000 = 0b1010 (bit now set)
```

#### Application 3: Clearing a Bit
```
Clear bit 3:
value & ~(1 << 3)

Example:
value = 0b1010
mask = ~(1 << 3) = ~0b1000 = 0b11110111 (assuming 8-bit)
value & mask = 0b1010 & 0b11110111 = 0b0010 (bit 3 cleared)
```

#### Application 4: Toggling a Bit
```
Toggle bit 3:
value ^ (1 << 3)

Example:
value = 0b1010
value ^ 0b1000 = 0b0010 (bit 3 toggled off)
```

#### Application 5: XOR Swap (No Temp Variable)
```
a = 5 (0b0101)
b = 3 (0b0011)

a = a ^ b  // a = 0b0101 ^ 0b0011 = 0b0110
b = a ^ b  // b = 0b0110 ^ 0b0011 = 0b0101 (now 5)
a = a ^ b  // a = 0b0110 ^ 0b0101 = 0b0011 (now 3)

Result: a = 3, b = 5 (swapped!)
```

### Exercises

#### Exercise 1: Basic Operations
Calculate by hand:
1. `0b1010 & 0b1100`
2. `0b1010 | 0b1100`
3. `0b1010 ^ 0b1100`
4. `~0b1010` (assume 8-bit)
5. `0b1010 << 2`
6. `0b1010 >> 1`

#### Exercise 2: Extract RGB from Color
Given a 32-bit color value `0xAARRGGBB`, extract:
- Alpha (AA): `(color >> 24) & 0xFF`
- Red (RR): `(color >> 16) & 0xFF`
- Green (GG): `(color >> 8) & 0xFF`
- Blue (BB): `color & 0xFF`

Practice with: `0xFF5733AB`

#### Exercise 3: Set, Clear, Toggle
Given `value = 0b10110010`:
1. Set bit 0
2. Clear bit 5
3. Toggle bit 2
4. Check if bit 4 is set
5. Set bits 0, 1, and 2 simultaneously

#### Exercise 4: Count Set Bits
Write algorithm to count number of 1s in `0b10110101`:
```
Algorithm:
count = 0
while value != 0:
    if value & 1:
        count++
    value = value >> 1
```

#### Exercise 5: Check if Power of 2
A number is a power of 2 if it has only one bit set.
```
Check: value & (value - 1) == 0

Examples:
8 = 0b1000, 8-1 = 0b0111, 0b1000 & 0b0111 = 0 ✓
10 = 0b1010, 10-1 = 0b1001, 0b1010 & 0b1001 = 0b1000 ✗
```

### Challenge Problems
1. Reverse the bits in a byte (0b10110010 → 0b01001101)
2. Find the rightmost set bit position
3. Clear all bits except the rightmost set bit
4. Implement fast modulo for powers of 2: `x % 8 = x & 7`

---

## Day 5-7: CPU Architecture Fundamentals

### Learning Objectives
- Understand CPU components and their roles
- Learn the fetch-decode-execute cycle
- Master x86-64 register architecture

### CPU Components

#### 1. Arithmetic Logic Unit (ALU)
- Performs arithmetic operations (add, subtract, multiply, divide)
- Performs logical operations (AND, OR, XOR, NOT)
- Sets condition flags based on results

#### 2. Control Unit (CU)
- Fetches instructions from memory
- Decodes instructions
- Coordinates execution across components
- Manages timing signals

#### 3. Registers
- Fastest memory in CPU
- Directly accessible by instructions
- Store temporary values, addresses, status

#### 4. Program Counter (PC/IP)
- Holds address of next instruction
- Called RIP in x86-64
- Automatically incremented after each instruction

#### 5. Stack Pointer (SP)
- Points to top of stack
- Called RSP in x86-64
- Stack grows downward

#### 6. Flags Register
- Stores CPU status bits
- Called RFLAGS in x86-64
- Includes Zero, Carry, Sign, Overflow flags

### Memory Hierarchy

```
Speed  ↑    Size ↓    Cost ↑
----------------------------
Registers (fastest, ~1 cycle)
   ↓
L1 Cache (~4 cycles)
   ↓
L2 Cache (~12 cycles)
   ↓
L3 Cache (~40 cycles)
   ↓
Main Memory/RAM (~100 cycles)
   ↓
SSD Storage (~50,000 cycles)
   ↓
HDD Storage (~5,000,000 cycles)
```

### Instruction Cycle

#### Fetch Stage
1. Read instruction from memory at address in RIP
2. Load into instruction register
3. Increment RIP to point to next instruction

#### Decode Stage
1. Determine operation type (add, move, jump, etc.)
2. Identify operands (registers, memory, immediate values)
3. Prepare control signals

#### Execute Stage
1. Perform the operation in ALU
2. Access memory if needed
3. Update registers

#### Store/Writeback Stage
1. Write results back to register or memory
2. Update flags if needed

**Example: ADD RAX, RBX**
```
Fetch: Get instruction bytes from memory[RIP]
Decode: Operation=ADD, Dest=RAX, Source=RBX
Execute: ALU adds RAX + RBX
Store: Result written to RAX, flags updated
```

### x86-64 Register Architecture

#### General-Purpose Registers (64-bit)

| 64-bit | 32-bit | 16-bit | 8-bit high | 8-bit low | Common Use |
|--------|--------|--------|------------|-----------|------------|
| RAX    | EAX    | AX     | AH         | AL        | Accumulator, return values |
| RBX    | EBX    | BX     | BH         | BL        | Base register |
| RCX    | ECX    | CX     | CH         | CL        | Counter, loop iterations |
| RDX    | EDX    | DX     | DH         | DL        | Data, I/O operations |
| RSI    | ESI    | SI     | -          | SIL       | Source index (strings) |
| RDI    | EDI    | DI     | -          | DIL       | Destination index (strings) |
| RBP    | EBP    | BP     | -          | BPL       | Base pointer (stack frame) |
| RSP    | ESP    | SP     | -          | SPL       | Stack pointer |

#### Extended Registers (64-bit only)
- R8, R9, R10, R11, R12, R13, R14, R15
- 32-bit: R8D, R9D, ... R15D
- 16-bit: R8W, R9W, ... R15W
- 8-bit: R8B, R9B, ... R15B

#### Special-Purpose Registers
- **RIP**: Instruction pointer (program counter)
- **RFLAGS**: Status and control flags
  - CF (Carry Flag, bit 0): Unsigned overflow/borrow
  - PF (Parity Flag, bit 2): Even parity
  - ZF (Zero Flag, bit 6): Result is zero
  - SF (Sign Flag, bit 7): Result is negative
  - OF (Overflow Flag, bit 11): Signed overflow

#### Segment Registers
- CS (Code Segment)
- DS (Data Segment)
- SS (Stack Segment)
- ES, FS, GS (Extra segments)

### Register Usage Examples

#### Accumulator (RAX)
```
; Used for arithmetic
mov rax, 10
add rax, 5      ; RAX = 15

; Used for return values
mov rax, 42     ; Return 42 from function
ret

; Used for syscalls
mov rax, 1      ; Syscall number (write)
syscall
```

#### Counter (RCX)
```
; Loop counter
mov rcx, 10
loop_start:
    ; body
    loop loop_start  ; Decrements RCX, jumps if not zero
```

#### Source/Destination (RSI/RDI)
```
; String operations
mov rsi, source_string
mov rdi, dest_string
mov rcx, length
rep movsb        ; Copy RSI to RDI
```

#### Stack Operations (RSP/RBP)
```
; RSP points to top of stack
push rax         ; Decrements RSP, stores RAX
pop rbx          ; Loads into RBX, increments RSP

; RBP used for stack frame
push rbp
mov rbp, rsp     ; Set up stack frame
```

### Exercises

#### Exercise 1: Register Mapping
List all accessible forms of RAX:
- 64-bit: RAX
- 32-bit: EAX
- 16-bit: AX
- 8-bit high: AH
- 8-bit low: AL

Do the same for RBX, RCX, RDX, RSI, RDI.

#### Exercise 2: Instruction Cycle Trace
Trace these instructions through fetch-decode-execute:
```
mov rax, 5
add rax, 3
mov rbx, rax
```

#### Exercise 3: Flag Prediction
Predict flag states after these operations:
```
1. mov rax, 5
   sub rax, 5     ; CF=?, ZF=?, SF=?, OF=?

2. mov rax, 255
   add rax, 1     ; CF=?, ZF=?, SF=?, OF=? (8-bit context)

3. mov rax, -1
   add rax, 1     ; CF=?, ZF=?, SF=?, OF=?
```

#### Exercise 4: Memory Hierarchy
Calculate approximate time for:
1. Reading a value from L1 cache (4 cycles, 2 GHz CPU)
2. Reading from RAM (100 cycles, 2 GHz CPU)
3. Reading from SSD (50,000 cycles, 2 GHz CPU)

#### Exercise 5: Register Reference Card
Create a cheat sheet with:
- All general-purpose registers
- Their 64/32/16/8-bit forms
- Common uses
- Calling convention roles (we'll cover this later)

### Mini-Project: CPU Simulator (Paper Exercise)

Create a simple CPU simulation on paper:
1. 4 registers: A, B, C, D (8-bit each)
2. 16 bytes of memory
3. Instructions: MOV, ADD, SUB, JMP
4. Flags: Z (zero), N (negative)

Execute this program:
```
MOV A, 5        ; A = 5
MOV B, 3        ; B = 3
ADD A, B        ; A = A + B = 8
MOV [0], A      ; Memory[0] = 8
SUB A, B        ; A = A - B = 5
```

Track:
- Register values after each instruction
- Memory contents
- Flag states
- Instruction pointer

---

## Week 1 Summary

### Key Concepts Mastered
✓ Binary, decimal, and hexadecimal number systems
✓ Bitwise operations: AND, OR, XOR, NOT, shifts
✓ CPU architecture components
✓ Memory hierarchy
✓ Instruction cycle: fetch-decode-execute
✓ x86-64 register architecture

### Skills Developed
✓ Quick number conversions
✓ Bit manipulation techniques
✓ Understanding CPU operation
✓ Register usage patterns

### Preparation for Week 2
Next week, you'll:
- Set up your development environment
- Write your first assembly program
- Learn about program structure and sections
- Implement basic I/O operations

### Daily Practice Routine
- 15 min: Number conversions
- 15 min: Bitwise operation problems
- 30 min: Review register architecture

### Resources
- Khan Academy: Computer Science basics
- Wikipedia: x86 architecture
- Intel 64 and IA-32 Software Developer Manuals (online)

---

## Week 1 Assessment

Test your knowledge:

1. Convert 0xCAFE to binary and decimal
2. What is `0b10101010 ^ 0b11110000`?
3. Name all 8 main general-purpose x86-64 registers
4. What are the 4 stages of the instruction cycle?
5. What is the difference between RAX and EAX?
6. Which flag is set when subtraction results in zero?
7. Calculate: `(42 >> 2) << 1` in decimal
8. What is the fastest memory in the computer?
9. Which register is used as the stack pointer?
10. How do you check if bit 5 is set in a value?

Answers in next week's material!

**Congratulations on completing Week 1!**
