# HSE ACOS Course — Part 1: Computer Architecture (part1ca)

**Course website:** https://andrewt0301.github.io/hse-acos-course/
**Instructor:** Andrei Tatarnikov (HSE Faculty of Computer Science)
**Tools used throughout:** RARS (RISC-V Assembler and Runtime Simulator), Ripes (visual pipeline simulator), Godbolt, Linux Ubuntu VM

---

## Complete Lecture List (16 Lectures)

| # | Folder Name | Title |
|---|-------------|-------|
| 1 | 01_Introduction | Introduction and Motivation |
| 2 | 02_DataTypes | Integer Data Formats and Operations |
| 3 | 03_CPU | Computer Architecture and Assembly Language |
| 4 | 04_Instructions | RISC-V Instruction Encoding |
| 5 | 05_MacroFunctions | Pseudo Instructions, Macros, Includes; Conditions, Loops, Loads, Stores |
| 6 | 06_CallStack | Subroutines, Call Stack, Calling Conventions |
| 7 | 07_FP | Floating-Point Format (IEEE 754) |
| 8 | 08_Caches | Memory Hierarchy and Caches |
| 9 | 09_VM | Virtual Memory |
| 10 | 10_Pipeline | Processor and Pipeline; Instruction-Level Parallelism |
| 11 | 11_Exceptions | Exceptions and Interrupts |
| 12 | 12_MMIO | Memory-Mapped I/O (MMIO) |
| 13 | 13_MultiIssue | Multiple Issue (Superscalar, VLIW) |
| 14 | 14_Multiprocessor | Thread-Level Parallelism |
| 15 | 15_Optimizations | Optimizations |
| 16 | 16_TPU | Domain-Specific Architectures and Tensor Processing Unit |

---

## Lecture 1: Introduction and Motivation

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/01_Introduction/lecture.html

### Theory Topics
- Historical perspective of computer architecture
- Course motivation and administration
- Stored program concept
- CPU performance measurement in different power modes (Linux example: performance vs. power-saving modes, MHz scaling)
- Overview of the course structure (Part 1: Computer Architecture, Part 2: Operating Systems)

### Workshop Content
- Setting up software tools: Linux Ubuntu VM, VirtualBox, SSH connectivity
- Introduction to Godbolt (online compiler explorer)
- Introduction to RARS (RISC-V Assembler and Runtime Simulator)
- Creating a private GitHub repository for coursework
- Downloading, compiling, and running matrix computation examples in C, Java, and Python to compare optimization approaches and language performance

### Key Concepts
- Computer as a layered abstraction: application → OS → hardware
- Performance metrics: clock frequency, CPI (cycles per instruction), execution time
- Why study computer architecture: understanding performance, writing efficient code

---

## Lecture 2: Integer Data Formats and Operations

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/02_DataTypes/lecture.html

### Theory Topics
- Number systems: binary (base 2), octal (base 8), hexadecimal (base 16), decimal (base 10)
- Base conversion algorithms (between binary, decimal, hexadecimal)
- Unsigned integer representation: range [0, 2^B - 1] for B bits
- Signed integer representations:
  - Sign-magnitude
  - One's complement
  - **Two's complement** (standard in modern CPUs): range [-2^(B-1), 2^(B-1) - 1]
- Two's complement conversion: invert all bits (one's complement), then add 1
- Integer overflow detection (signed and unsigned)
- Bitwise operations and their relation to data representation
- Character encoding (ASCII)

### Workshop Content
- Converting numbers between bases (binary, decimal, hexadecimal)
- Performing arithmetic in binary representation
- Detecting overflow in addition/subtraction
- Working with signed and unsigned integers in RISC-V assembly

### Key Formulas
- Unsigned range for N bits: `0` to `2^N - 1`
- Signed (two's complement) range for N bits: `-2^(N-1)` to `2^(N-1) - 1`
- Negation in two's complement: `~x + 1`

### Exam Topics Covered
- Topic 2: Integer data formats and operations

---

## Lecture 3: Computer Architecture and Assembly Language

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/03_CPU/lecture.html

### Theory Topics
- Assembly language fundamentals
- RISC vs. CISC architectures
- RISC-V architecture overview and philosophy
- Instruction Set Architecture (ISA) concept
- CPU performance and factors affecting it
- Processor and computer organization (datapath, control unit)
- Registers in RISC-V (x0–x31), register naming conventions

### Workshop Content — RARS System Calls and RISC-V Instructions
**System calls used:** ReadInt, PrintInt, PrintIntUnsigned, PrintBinary, PrintHex

**RISC-V instructions practiced:**
- **Arithmetic:** `add`, `sub`, `addi`
- **Multiplication/Division:** `mul`, `rem`, `div`, `divu`, `remu`
- **Comparison:** `slt`, `sltu`, `slti`, `sltiu`
- **Shifts:** `sll`, `srl`, `sra`, `slli`, `srli`, `srai`
- **Bitwise:** `and`, `or`, `xor`, `andi`, `ori`, `xori`

**Practical tasks:**
1. "Hello World" program in RISC-V assembly
2. Basic arithmetic operations (add two inputs, print result)
3. Input/output in multiple formats (decimal, hex, binary, unsigned)
4. Bitwise operations and bit manipulation
5. Integer overflow detection program

### Key Concepts
- RISC-V is a free, open ISA designed at UC Berkeley
- 32 general-purpose registers (x0 is hardwired to 0)
- Three-operand instruction format: `add rd, rs1, rs2`
- Immediate instructions: `addi rd, rs1, imm`

### Exam Topics Covered
- Topic 4: ISA and assembler language
- Topic 5: Assembler, assembly language. RISC-V
- Topic 6: ISA types: RISC, CISC, VLIW

---

## Lecture 4: RISC-V Instruction Encoding

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/04_Instructions/lecture.html

### Theory Topics
- RISC-V ISA base (RV32I) and extensions (M, A, F, D, C)
- **Instruction encoding formats** (all 32 bits wide in base ISA):
  - **R-type** (register-register): `add`, `sub`, `and`, `or`, `xor`, `sll`, `srl`, `sra`, `slt`, `sltu`
    - Fields: `funct7 | rs2 | rs1 | funct3 | rd | opcode`
  - **I-type** (immediate): `addi`, `ori`, `lw`, `jalr`
    - Fields: `imm[11:0] | rs1 | funct3 | rd | opcode`
  - **S-type** (store): `sw`, `sb`, `sh`
    - Fields: `imm[11:5] | rs2 | rs1 | funct3 | imm[4:0] | opcode`
  - **SB-type** (branch): `beq`, `bne`, `blt`, `bge`, `bltu`, `bgeu`
    - Fields: `imm[12|10:5] | rs2 | rs1 | funct3 | imm[4:1|11] | opcode`
  - **U-type** (upper immediate): `lui`, `auipc`
    - Fields: `imm[31:12] | rd | opcode`
  - **UJ-type** (jump): `jal`
    - Fields: `imm[20|10:1|11|19:12] | rd | opcode`
- Memory layout (text, data, heap, stack segments)
- Stored program concept: instructions and data in same memory

### Workshop Content
**Encoding practice tasks:**
- Encode instructions to binary: R-type (`add`), I-type (`ori`, `lw`), S-type (`sw`), SB-type (`beq`), U-type (`lui`), UJ-type (`jal`)
- Decode hexadecimal values back to assembly instructions

**Programming tasks:**
- Write a program that reads an integer and outputs -1, 0, or 1 based on whether it is negative, zero, or positive
- Find the minimum and maximum of two input values
- Working with pseudoinstructions: `mv`, `li`, `la`, `b`, `j`
- Using labels and branch instructions

**Homework:** Decompile machine-level programs from hexadecimal representation into RISC-V assembly using RARS.

### Key Concepts
- Every RISC-V instruction is exactly 32 bits (in base ISA)
- Opcode field (bits [6:0]) determines the instruction type
- Immediate values are sign-extended
- Branch offsets are relative to the current PC and always even (bit 0 is implicit 0)

### Exam Topics Covered
- Topic 7: Registers and memory
- Topic 8: Code addressing: conditionals, loops, and arrays

---

## Lecture 5: Pseudo Instructions, Macros, Conditions, Loops, Loads, Stores

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/05_MacroFunctions/lecture.html

### Theory Topics
- **Pseudoinstructions** — assembler-level instructions expanded into real instructions:
  - `mv rd, rs` → `addi rd, rs, 0`
  - `li rd, imm` → `lui` + `addi` (for large immediates)
  - `la rd, label` → `auipc` + `addi`
  - `j label` → `jal x0, label`
  - `b label` → `beq x0, x0, label`
  - `nop` → `addi x0, x0, 0`
  - `not rd, rs` → `xori rd, rs, -1`
  - `neg rd, rs` → `sub rd, x0, rs`
- **Macros** in RARS assembly: `.macro` / `.end_macro` directives for reusable code templates with parameters
- **Includes:** `.include` directive for modular code organization
- **Memory access instructions:**
  - Load: `lb`, `lbu`, `lh`, `lhu`, `lw`
  - Store: `sb`, `sh`, `sw`
- **Data segment directives:** `.data`, `.text`, `.word`, `.half`, `.byte`, `.ascii`, `.asciiz`, `.space`
- Implementing conditions (`if-else`) with branches
- Implementing loops (`for`, `while`) with branches and labels
- Array access patterns in assembly (base address + offset)

### Workshop Content
- Writing programs with conditional logic using branch instructions
- Implementing loops (counting, iterating over arrays)
- Using macros to simplify repetitive code
- Working with the data segment to store arrays and strings
- Load/store operations between memory and registers

### Key Concepts
- Pseudoinstructions make assembly code more readable but are not real machine instructions
- Macros are expanded at assembly time (not runtime)
- Arrays in RISC-V: base address in register, access via offset (`lw rd, offset(rs)`)
- Data alignment requirements for different data sizes

### Exam Topics Covered
- Topic 8: Code addressing: conditionals, loops, and arrays
- Topic 11: Call frame and macros (partially)

---

## Lecture 6: Subroutines, Call Stack, Calling Conventions

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/06_CallStack/lecture.html

### Theory Topics
- **Functions** (subroutines): caller and callee concepts
- **Jump-and-link instructions:**
  - `jal rd, label` — jump to label, save return address in rd
  - `jalr rd, rs, imm` — jump to address in rs+imm, save return in rd
- **Register conventions in RISC-V:**
  - **Caller-saved (temporary):** `ra` (x1), `a0`–`a7` (x10–x17), `t0`–`t6` (x5–x7, x28–x31)
  - **Callee-saved (saved):** `sp` (x2), `s0`–`s11` (x8–x9, x18–x27)
  - `a0`–`a7`: function arguments; `a0`–`a1`: return values
- **Stack frame (activation record):**
  - Stack grows downward (toward lower addresses)
  - `sp` (stack pointer) must be 16-byte aligned
  - Frame structure: saved `ra`, saved `s` registers, local variables, spilled arguments
- **Function prologue and epilogue:** saving/restoring registers and adjusting `sp`

### Workshop Content
- Writing nested and recursive functions in RISC-V assembly
- Using caller-saved and callee-saved registers correctly
- Allocating local variables and arrays on the stack
- Using development tools: Godbolt (compile C to RISC-V assembly), RISC-V GCC toolchain

**Example programs provided:**
- `swap.s` — swapping two values using a function
- `fact.s` — recursive factorial
- `ackermann.s` — Ackermann function (deep recursion)
- `euclid.s` — Euclidean algorithm (GCD)
- `macrolib.s` — macro library for common operations
- Frame pointer (`fp` / `s0`) usage examples

### Key Concepts
- Leaf functions (no further calls) can skip saving `ra`
- Non-leaf functions must save `ra` on the stack
- Recursive functions must save all used `s` registers
- Calling convention is a contract between caller and callee

### Exam Topics Covered
- Topic 9: Stack and subroutines
- Topic 11: Call frame and macros

---

## Lecture 7: Floating-Point Format (IEEE 754)

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/07_FP/lecture.html

### Theory Topics
- **IEEE 754 Standard** for floating-point representation
- **Single precision (float, 32-bit):**
  - 1 sign bit + 8 exponent bits (biased by 127) + 23 mantissa bits
  - Value = (-1)^S × 1.M × 2^(E-127)
- **Double precision (double, 64-bit):**
  - 1 sign bit + 11 exponent bits (biased by 1023) + 52 mantissa bits
- **Special values:** +0, -0, +∞, -∞, NaN (Not a Number), denormalized numbers
- Floating-point arithmetic: addition, multiplication algorithms
- Rounding modes
- Precision loss and representation errors

### RISC-V Floating-Point Instructions
- **F extension** (single-precision): `flw`, `fsw`, `fadd.s`, `fsub.s`, `fmul.s`, `fdiv.s`, `fsqrt.s`, `fmin.s`, `fmax.s`, `feq.s`, `flt.s`, `fle.s`, `fcvt.s.w`, `fcvt.w.s`
- **D extension** (double-precision): `fld`, `fsd`, `fadd.d`, `fsub.d`, `fmul.d`, `fdiv.d`, `fsqrt.d`
- **FP registers:** `f0`–`f31` (aliased as `ft0`–`ft11` for temporaries, `fs0`–`fs11` for saved, `fa0`–`fa7` for arguments)
- **FP calling conventions:** `fa0`–`fa7` for arguments, `fa0`–`fa1` for return values

### Workshop Content
- Converting between binary and decimal floating-point representations
- Writing assembly programs to print floating-point values in binary format
- Arithmetic operations with floating-point numbers
- Filtering and manipulating floating-point data (arrays)

**Example programs:**
- `e.s` — computing Euler's number e
- `sqrt.s`, `sqrtd.s` — square root calculations (single and double precision)
- `fahr1.s`, `fahr2.s` — Fahrenheit-to-Celsius conversion

**Homework assignments:**
- Fraction truncation program
- Cubic root calculation
- Calculating π using the Leibniz formula: π/4 = 1 - 1/3 + 1/5 - 1/7 + ...

### Key Formulas
- IEEE 754 single: `(-1)^S × (1 + 0.M) × 2^(E - 127)`
- IEEE 754 double: `(-1)^S × (1 + 0.M) × 2^(E - 1023)`
- Denormalized: exponent = 0, implicit leading bit = 0 (not 1)

### Exam Topics Covered
- Topic 3: Floating-point data formats and operations
- Topic 10: Mathematical coprocessor

---

## Lecture 8: Memory Hierarchy and Caches

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/08_Caches/lecture.html

### Theory Topics
- **Memory hierarchy:** registers → L1 cache → L2 cache → L3 cache → main memory (DRAM) → disk/SSD
- **Principle of locality:**
  - Temporal locality: recently accessed data will likely be accessed again soon
  - Spatial locality: data near recently accessed data will likely be accessed soon
- **Processor-memory performance gap:** CPU speed grows faster than memory speed
- **Types of memory devices:** SRAM (fast, expensive, used for cache), DRAM (slower, cheaper, used for main memory)
- **Cache memory types:**
  - **Direct-mapped cache:** each memory block maps to exactly one cache line
    - Address split: tag | index | block offset
  - **Set-associative cache:** each block maps to one of N lines in a set (N-way)
    - Address split: tag | set index | block offset
  - **Fully-associative cache:** any block can go to any line
    - Address split: tag | block offset
- **Write policies:**
  - Write-through: write to both cache and memory
  - Write-back: write only to cache, write to memory on eviction (uses dirty bit)
- **Replacement policies:** LRU (Least Recently Used), FIFO, Random
- **Multi-level caches:** L1 (small, fast), L2 (medium), L3 (large, shared)

### Real CPU Cache Examples
**Intel Core i7-13700 (12 cores):**
- L1 Data: 48 KB/core, 12-way set-associative, 64B line
- L1 Instruction: 32 KB/core, 8-way, 64B line
- L2: 2 MB/core, 16-way, 64B line
- L3: 30 MB shared, 12-way, 64B line

**Intel Core i7-1260P (8 cores):**
- L1 Data: 48 KB/core, 12-way
- L2: 1.25 MB/core, 10-way
- L3: 18 MB shared, 12-way

### Workshop Content
- Using RARS Data Cache Simulator to visualize cache behavior
- Configuring cache parameters (size, associativity, block size)
- Simulating memory reference patterns
- Analyzing cache hit/miss rates for different access patterns
- Comparing direct-mapped vs. set-associative vs. fully-associative caches

### Key Formulas
- Cache size = number of sets × associativity × block size
- Number of sets = cache size / (associativity × block size)
- Tag bits = address bits - index bits - offset bits
- Offset bits = log₂(block size)
- Index bits = log₂(number of sets)
- Hit rate = hits / total accesses
- Average Memory Access Time (AMAT) = hit time + miss rate × miss penalty

### Exam Topics Covered
- Topic 16: Cache

---

## Lecture 9: Virtual Memory

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/09_VM/lecture.html

### Theory Topics
- **Virtual vs. physical addresses:** processes use virtual addresses; MMU translates to physical
- **Page-based virtual memory:**
  - Virtual address = virtual page number (VPN) + page offset
  - Physical address = physical page number (PPN) + page offset
  - Page size is typically 4 KB (12-bit offset)
- **Page table:** maps VPN → PPN, stored in memory
  - Each entry contains: PPN, valid bit, dirty bit, permission bits (read/write/execute)
  - Multi-level page tables to reduce memory usage
- **TLB (Translation Lookaside Buffer):** cache for page table entries
  - TLB hit: fast translation (1 cycle)
  - TLB miss: walk the page table (multiple memory accesses)
- **Page faults:** when a page is not in physical memory
  - OS handles page fault: load page from disk, update page table, retry instruction
- **Memory protection:** each process has its own virtual address space, cannot access other processes' memory
- **Benefits of virtual memory:**
  - Process isolation and protection
  - Virtual address space larger than physical memory
  - Demand paging (only load pages when needed)
  - Memory-mapped files
  - Copy-on-write optimization

### Workshop Content
- Address translation exercises (VPN → PPN via page table)
- TLB lookup simulations
- Understanding page fault handling sequence
- Calculating page table sizes for different configurations

### Key Formulas
- Virtual address bits = VPN bits + offset bits
- Page offset bits = log₂(page size)
- Number of pages = 2^(VPN bits)
- Page table size = number of pages × page table entry size
- For RISC-V Sv39: 3-level page table, 39-bit virtual address, 56-bit physical address

### Exam Topics Covered
- Topic 17: Virtual Memory (virtual and physical addresses, page tables, TLB)

---

## Lecture 10: Processor and Pipeline; Instruction-Level Parallelism

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/10_Pipeline/lecture.html

### Theory Topics
- **Single-cycle processor:** each instruction completes in one clock cycle
  - Simple but slow (clock period = longest instruction)
- **5-stage pipeline:**
  1. **IF** (Instruction Fetch): read instruction from memory
  2. **ID** (Instruction Decode): decode instruction, read registers
  3. **EX** (Execute): ALU operation or address calculation
  4. **MEM** (Memory Access): read/write data memory
  5. **WB** (Write Back): write result to register file
- **Pipeline speedup:** ideally N times faster (N = number of stages), limited by hazards
- **Pipeline hazards:**
  - **Structural hazards:** two instructions need the same hardware resource simultaneously
    - Solution: separate instruction and data memories/caches
  - **Data hazards:** instruction depends on result of prior instruction still in pipeline
    - RAW (Read After Write) — most common
    - Solution: **forwarding/bypassing** (route result directly from pipeline register)
    - Solution: **stalling** (insert bubble/NOP when forwarding insufficient, e.g., load-use hazard)
  - **Control hazards:** branch/jump changes PC, but next instructions already in pipeline
    - Solution: **branch prediction** (static: always not taken; dynamic: branch history table)
    - Solution: **stalling** (flush pipeline on branch)
    - Solution: **delayed branching**
- **Hazard detection unit:** hardware that detects conflicts and triggers stalls/forwarding
- **Branch prediction:**
  - Static prediction (always predict not taken)
  - Dynamic prediction (1-bit, 2-bit predictors, branch history buffer)

### Workshop Content (using Ripes Simulator)
1. Identify and describe methods to overcome the 3 types of pipeline hazards
2. Trace code execution on a pipeline without hazard handling
3. Insert NOP instructions to manually resolve data hazards
4. Analyze structural hazards in code accessing shared memory
5. Optimize code to minimize stalls on a 5-stage pipeline:
   - Without forwarding (only stalling)
   - With forwarding enabled
6. Compare single-cycle vs. 5-stage pipelined processor performance
7. Experiment with different hazard handling configurations in Ripes

### Key Formulas
- Pipeline speedup (ideal) = number of stages
- CPI (pipelined, ideal) = 1
- CPI (with stalls) = 1 + stall cycles per instruction
- Execution time = instruction count × CPI × clock period
- Speedup = time_unpipelined / time_pipelined

### Exam Topics Covered
- Topic 15: Pipeline and branch prediction

---

## Lecture 11: Exceptions and Interrupts

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/11_Exceptions/lecture.html

### Theory Topics
- **Exception:** unscheduled event that disrupts normal program execution
- **Interrupt:** an exception originating from outside the processor (e.g., I/O device, timer)
- Some architectures use "interrupt" as general term for all exceptions
- **Causes of exceptions:**
  - Arithmetic errors (overflow, divide by zero)
  - Invalid instructions (undefined opcode)
  - Memory access violations (page fault, misaligned access)
  - System calls (`ecall`)
  - Breakpoints (`ebreak`)
  - External interrupts (timer, I/O devices)

### RISC-V Control and Status Registers (CSRs)
**User Trap Setup:**
| Register | Address | Description |
|----------|---------|-------------|
| `ustatus` | 0x000 | User status register |
| `uie` | 0x004 | User interrupt-enable register |
| `utvec` | 0x005 | User trap handler base address |

**User Trap Handling:**
| Register | Address | Description |
|----------|---------|-------------|
| `uscratch` | 0x040 | Scratch register for trap handlers |
| `uepc` | 0x041 | User exception program counter |
| `ucause` | 0x042 | User trap cause code |
| `utval` | 0x043 | User bad address or instruction |
| `uip` | 0x044 | User interrupt pending |

**Floating-Point CSRs:** `fflags`, `frm`, `fcsr`
**Counters:** `cycle`, `time`, `instret` (instructions retired)

### System Instructions
- **CSR manipulation:** `csrrw`, `csrrs`, `csrrc`, `csrrwi`, `csrrsi`, `csrrci`
  - `csrrw rd, csr, rs` — read CSR into rd, write rs into CSR
  - `csrrs rd, csr, rs` — read CSR into rd, set bits specified by rs
  - `csrrc rd, csr, rs` — read CSR into rd, clear bits specified by rs
- **System calls:** `ecall` — transfer control to OS (system call)
- **Breakpoint:** `ebreak` — pause execution at breakpoint
- **Return from trap:** `uret` — return from user-level exception handler

### Workshop Content
- Understanding exception handling flow: save state → identify cause → handle → restore state → return
- Writing simple exception handlers in RISC-V assembly
- Working with CSRs to configure and handle interrupts
- Timer interrupt handling

**Example programs:**
- `simple_handler.s` — basic exception handler demonstrating CSR usage
- `timer.s` — timer interrupt handler using cycle counter

### Exam Topics Covered
- Topic 13: Exceptions and traps
- Topic 14: Interrupts

---

## Lecture 12: Memory-Mapped I/O (MMIO)

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/12_MMIO/lecture.html

### Theory Topics
- **I/O devices** and how the CPU communicates with them
- **Memory-Mapped I/O (MMIO):** I/O device registers are mapped to specific memory addresses
  - CPU uses regular load/store instructions to communicate with devices
  - Device registers appear as regular memory locations
- **Port-mapped I/O (PMIO):** separate I/O address space with special instructions (e.g., x86 `in`/`out`)
- **Polling vs. Interrupts:**
  - Polling: CPU repeatedly checks device status register (busy-waiting)
  - Interrupts: device notifies CPU when ready (more efficient)
- **Direct Memory Access (DMA):** device transfers data directly to/from memory without CPU involvement
  - CPU sets up DMA controller (source, destination, size)
  - DMA controller performs transfer, interrupts CPU when done
- **RARS MMIO device emulators:**
  - Keyboard and Display MMIO Simulator
  - Bitmap Display
  - Digital Lab Sim (7-segment display and keypad)

### Workshop Content
**Example programs:**
- `mmio_poll.s` — keyboard input using polling (checking receiver control register)
- `mmio_interrupt.s` — keyboard input using interrupt-driven I/O
- `bitmap_display.s` — drawing graphics on the RARS Bitmap Display
- `digit_lab1.s`, `digit_lab2.s`, `digit_lab3.s` — working with Digital Lab Sim display

**Homework assignments:**
1. Display hex key presses as decimal values on Digital Sim Lab
2. Build a program to enter and compare 32-bit integers using Digital Sim Lab
3. Draw a 64×64 square in a 128×128 Bitmap Display with user-specified color and line thickness

### Key Concepts
- MMIO addresses in RARS:
  - `0xFFFF0000` — Receiver Control (bit 0 = ready)
  - `0xFFFF0004` — Receiver Data (ASCII character)
  - `0xFFFF0008` — Transmitter Control (bit 0 = ready)
  - `0xFFFF000C` — Transmitter Data (write character to display)

### Exam Topics Covered
- Topic 12: Polling and MMIO

---

## Lecture 13: Multiple Issue (Superscalar and VLIW)

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/13_MultiIssue/lecture.html

### Theory Topics
- **Multiple issue:** issuing more than one instruction per clock cycle → CPI < 1
- **Superscalar processors:**
  - Issue a variable number of instructions per cycle
  - **Statically scheduled:** in-order execution, compiler arranges instructions
  - **Dynamically scheduled:** out-of-order execution, hardware reorders instructions
  - Must check for instruction dependencies at runtime
  - Examples: modern Intel/AMD x86 processors (issue 4-6+ instructions/cycle)
- **VLIW (Very Long Instruction Word):**
  - Issue a fixed number of operations packed into one "bundle"
  - Compiler responsible for scheduling and dependency checking
  - One PC per bundle (not per operation)
  - Simpler hardware (no dependency checking, no out-of-order logic)
  - Compiler must use techniques: loop unrolling, software pipelining, trace scheduling
  - Examples: Intel Itanium (IA-64), TI C6x DSP, Elbrus (Russian)
- **Comparison:**
  | Feature | Superscalar | VLIW |
  |---------|------------|------|
  | Scheduling | Hardware (dynamic) or compiler (static) | Compiler only |
  | Issue width | Variable | Fixed |
  | Hardware complexity | High | Low |
  | Compiler complexity | Lower | Higher |
  | Binary compatibility | Better | Worse |

### Workshop Content
- Analyzing instruction-level parallelism in code sequences
- Scheduling instructions for VLIW architectures
- Understanding the trade-offs between superscalar and VLIW approaches

### Exam Topics Covered
- Topic 6: ISA types: RISC, CISC, VLIW (partially)

---

## Lecture 14: Thread-Level Parallelism

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/14_Multiprocessor/lecture.html

### Theory Topics
- **Multicore processors:** multiple CPU cores on a single chip
- **Symmetric Multiprocessing (SMP):** all cores share memory and have equal access
- **Flynn's Taxonomy:**
  - SISD (Single Instruction, Single Data) — traditional uniprocessor
  - SIMD (Single Instruction, Multiple Data) — vector/GPU processing
  - MISD (Multiple Instruction, Single Data) — rare
  - MIMD (Multiple Instruction, Multiple Data) — multiprocessors
- **Shared memory vs. distributed memory**
- **Cache coherence problem:** multiple cores cache the same memory location
  - Snooping protocols (e.g., MESI: Modified, Exclusive, Shared, Invalid)
  - Directory-based protocols (for many cores)
- **Memory consistency models:** sequential consistency, relaxed consistency
- **Amdahl's Law:** speedup limited by the sequential fraction of the program
  - Speedup = 1 / (S + P/N), where S = serial fraction, P = parallel fraction, N = cores
- **Synchronization primitives:** locks, barriers, atomic operations
- **Data-level parallelism:**
  - Vector processing (SIMD)
  - GPU computing

### Workshop Content
- Analyzing the potential for parallelism in code
- Applying Amdahl's Law to estimate speedup
- Understanding cache coherence scenarios

### Key Formulas
- **Amdahl's Law:** Speedup(N) = 1 / ((1 - P) + P/N)
  - P = parallelizable fraction, N = number of processors
  - Maximum speedup (N → ∞) = 1 / (1 - P)

### Exam Topics Covered
- Topic 18: Data-level parallelism (Vector, SIMD, GPU)
- Topic 20: Thread-level parallelism (memory coherency)

---

## Lecture 15: Optimizations

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/15_Optimizations/lecture.html

### Theory Topics
- **Compiler optimizations:**
  - Constant folding and propagation
  - Dead code elimination
  - Common subexpression elimination
  - Loop optimizations: unrolling, strength reduction, loop invariant code motion
  - Inlining
  - Register allocation
- **Memory access optimizations:**
  - Cache-friendly access patterns (row-major vs. column-major)
  - Loop tiling / blocking for cache reuse
  - Prefetching
- **Instruction-level optimizations:**
  - Instruction scheduling to avoid pipeline stalls
  - Branch optimization
  - SIMD vectorization
- **Profiling and performance measurement:**
  - Hardware performance counters
  - Benchmarking methodology
- **Matrix multiplication optimization** as a case study:
  - Naive O(n³) implementation
  - Cache-blocked version
  - Vectorized version
  - Comparison across languages (C with -O2/-O3, Java, Python)

### Workshop Content
- Measuring performance of different implementations
- Applying optimization techniques to improve code
- Analyzing compiler output with different optimization levels (Godbolt)
- Cache behavior analysis for different access patterns

### Exam Topics Covered
- Topic 21: Optimizations

---

## Lecture 16: Domain-Specific Architectures and Tensor Processing Unit (TPU)

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/16_TPU/lecture.html

### Theory Topics
- **Performance limitations of general-purpose processors:**
  - Diminishing returns from Moore's Law
  - Power wall, memory wall, ILP wall
  - Need for specialized hardware for specific workloads
- **Domain-Specific Architectures (DSAs):** hardware optimized for specific application domains
  - More energy efficient and higher performance for target workloads
  - Less flexible than general-purpose CPUs
- **Neural networks** overview:
  - Perceptron, multi-layer networks
  - Inference vs. training
  - Matrix multiplication as the core operation
- **Systolic arrays:**
  - Regular array of processing elements (PEs)
  - Data flows rhythmically between PEs
  - Highly efficient for matrix multiplication
  - Each PE performs multiply-accumulate (MAC)
- **Google's Tensor Processing Unit (TPU):**
  - Custom ASIC designed for neural network inference (and training in later versions)
  - Core component: 256×256 systolic array
  - Operates on 8-bit integers (INT8) for inference
  - Much higher throughput/watt than GPUs for ML workloads
  - TPU v1 (2015): inference only, 92 TOPS
  - Later versions: TPU v2, v3, v4 with training support

### Workshop Content
- TPU Lab in Google Colab: hands-on experimentation with TPU
- Running neural network training/inference on TPU
- Comparing performance: CPU vs. GPU vs. TPU

**Homework:** Experiment with TPU at Google Colab (training a model, measuring performance).

### Exam Topics Covered
- Topic 19: DSL architectures (Domain-Specific)

---

## Exam Topics — Full List (Computer Architecture, 21 Topics)

For reference, the complete list of exam topics for the Computer Architecture portion:

1. Intro: Computer architecture concept. Stored program.
2. Integer data formats and operations.
3. Floating-point data formats and operations.
4. ISA and assembler language.
5. Assembler, assembly language. RISC-V.
6. ISA types: RISC, CISC, VLIW.
7. Registers and memory.
8. Code addressing: conditionals, loops, and arrays.
9. Stack and subroutines.
10. Mathematical coprocessor.
11. Call frame and macros.
12. Polling and MMIO.
13. Exceptions and traps.
14. Interrupts.
15. Pipeline and branch prediction.
16. Cache.
17. Virtual Memory (virtual and physical addresses, page tables, TLB).
18. Data-level parallelism (Vector, SIMD, GPU).
19. DSL architectures.
20. Thread-level parallelism (memory coherency).
21. Optimizations.

---

## Homework Tasks (20 Programming Assignments)

All assignments are in RISC-V assembly, submitted via EJudge system:

1. **Palindrome** — check if a string is a palindrome
2. **LargestSum** — find largest sum in a sequence
3. **EightSectors** — determine which sector of a circle a point falls in
4. **NoError** — error detection/correction
5. **CrtDraw** — drawing on a CRT-style display
6. **ReverseString** — reverse a string in memory
7. **KeySort** — sort by key
8. **ASCIIGrid** — generate an ASCII grid
9. **LeibPi** — compute π using the Leibniz series (floating-point)
10. **FractionTruncate** — truncate fractions (floating-point)
11. **CubicRoot** — compute cubic root (floating-point)
12. **LeftDigits** — extract leftmost digits
13. **RecursiveGCD** — recursive greatest common divisor (subroutine with stack)
14. **FuncSort** — sorting function (pass array start/end via a0, a1)
15. **CheckTriangles** — validate triangle properties
16. **NoDups** — remove duplicates
17. **EvenBack** — process even numbers backward
18. **PlusMinus** — alternating addition/subtraction
19. **DigitSum** — sum of digits
20. **DoubleSum** — sum of double-precision values

---

## Course Software and Tools

| Tool | Purpose |
|------|---------|
| **RARS** | RISC-V Assembler and Runtime Simulator (Java-based, cross-platform) |
| **Ripes** | Visual RISC-V pipeline simulator (used in Lecture 10) |
| **Godbolt** | Online compiler explorer (C → RISC-V assembly, various optimization levels) |
| **Linux Ubuntu VM** | Development environment (VirtualBox or native) |
| **GCC RISC-V toolchain** | Cross-compiler for RISC-V |
| **Google Colab** | TPU experiments (Lecture 16) |

---

## Textbook References

- **Patterson & Hennessy** — *Computer Organization and Design: RISC-V Edition*
- **Patterson & Hennessy** — *Computer Architecture: A Quantitative Approach*
- **Harris & Harris** — *Digital Design and Computer Architecture: RISC-V Edition*
- Related courses: UC Berkeley CS61C, MIT 6.004, MIT 6.172
