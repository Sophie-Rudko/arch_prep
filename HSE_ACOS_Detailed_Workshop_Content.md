# HSE ACOS Course — Detailed Workshop & Theory Content (Part 1: Computer Architecture)

**Source:** https://andrewt0301.github.io/hse-acos-course/
**Repository:** https://github.com/andrewt0301/hse-acos-course

This document contains the COMPLETE content extracted directly from the course lecture pages, including all theory explanations, workshop tasks with full descriptions, code examples, homework assignments, tables, and formulas.

---

## Lecture 2: Data Representation

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/02_DataTypes/lecture.html

### Lecture Outline (Theory)

- Bits, bytes, words, etc.
- Binary, decimal, hexadecimal
- Byte ordering: big-endian and little-endian
- Integer formats: Signed and unsigned
- Bitwise and integer operations

### Workshop Outline

- Discuss current issues with VirtualBox and Linux VM.
- Play with converting values to different numeric systems (binary, decimal, hexadecimal). *Hint:* Use "whiteboard" and calculator.
- Run the `dumpbytes.c` program that dumps byte-level representations of integers and strings. Try dumping your own values.
- Play with negation, sign-extension, and zero-extension.
- Play with addition and subtraction.
- Explain some bit tricks from Hacker's Delight.

### Workshop Tasks (Complete)

**Task 1.** Convert the following decimal numbers to 5-bit binary numbers.

- Unsigned: `0`, `1`, `2`, `4`, `7`, `15`, `16`, `31`
- Signed: `0`, `-1`, `-2`, `-4`, `-7`, `15`, `-16`

**Task 2.** Convert the following 5-bit values to decimal numbers. Consider both unsigned and two's complement formats.

- Values: `00101`, `01011`, `10101`, `11111`, `10000`

**Task 3.** Convert the following decimal values to 8-bit hexadecimal numbers.

- Values: `0`, `10`, `14`, `15`, `16`, `32`, `34`, `127`, `128`, `255`

**Task 4.** Convert the following hexadecimal numbers to 8-bit binary values:

- Values: `0x1`, `0x2`, `0x7`, `0x8`, `0x10`, `0x7F`, `0xFF`, `0x80`

**Task 5.** Negate the binary values from the previous task.

**Task 6.** What are the ranges (smallest..largest) for integer values that consist of 4, 5, 6, 7, and 8 bits? Consider both unsigned and two's complement formats.

**Task 7.** View and run the `dumpbytes.c` program.

```bash
cat dumpbytes.c
gcc dumpbytes.c -o dumpbytes
./dumpbytes
```

Pay attention to addresses and byte ordering. Is your machine big- or little-endian?

**Task 8.** Zero-extend and sign-extend the following 4-bit values to 8 bits. Convert the result to decimal numbers.

- Values: `0001`, `1111`, `1010`, `1000`, `0111`

**Task 9.** Shift the following 8-bit binary value 3-digits to the right and to the left. Consider logic and arithmetical shifts.

- Values: `0000_1010`, `1111_1111`, `1000_1010`

**Task 10.** Perform the bitwise AND and OR operations for the following pairs of values:

- Values: (`0011`, `1100`), (`1011`, `1101`), (`0101`, `1001`), (`1010`, `1110`)

**Task 11.** Perform the bitwise XOR operation for the following pairs of values from the previous task.

**Task 12.** (*) Explain the following bit trick. Swapping values `x` and `y` without using a temporary variable can be done in the following way:

```
x = x ^ y;
y = x ^ y;
x = x ^ y;
```

How does it work? Take values from the previous task as an example.

**Task 13.** Add the following pairs of 4-bit binary values. Check the result by converting values to decimal numbers.

- Values: (`0001`, `1110`), (`0111`, `0001`), (`1101`, `0011`), (`0101`, `1001`)
- Which additions cause an overflow?

**Task 14.** Subtract pairs of values from the first example.

**Task 15.** What do you need to do to set (assign 1) and reset (assign 0) to the N-th bit in value `x`?

**Task 16.** (*) Explain the following bit tricks:

- `x & (x - 1)` - turning off the rightmost 1-bit (e.g. `01011000` => `01010000`)
- `x | (x + 1)` - turning on the rightmost 0-bit (e.g. `10100111` => `10101111`)
- `x | (x - 1)` - turning on the trailing 0's (e.g. `10101000` => `10101111`)

### Code Example: dumpbytes.c

```c
#include <stdio.h>

typedef unsigned char * byte_pointer;

void show_bytes(byte_pointer start, size_t len) {
    printf("%p: ", start);
    int i;
    for (i = 0; i < len; i++) {
        printf("%02X ", start[i]);
    }
    printf("\n");
}

void show_int(int x) {
    show_bytes((byte_pointer) &x, sizeof(int));
}

void show_long(long x) {
    show_bytes((byte_pointer) &x, sizeof(long));
}

void show_float(float x) {
    show_bytes((byte_pointer) &x, sizeof(float));
}

void show_pointer(void *x) {
    show_bytes((byte_pointer) &x, sizeof(void*));
}

int main() {
    int ints[] = {0, 1, -1, 8, -8, 0x00001234, 0x56780000, 0x12345678, 0xDEADBEEF, 0x0A0B0C0D, 15213, -15213};
    long longs[] = {0, 1, -1, 0xDEADBEEFBAADF00D, 0x0102030405060708};
    for (int i = 0; i < sizeof(ints) / sizeof(int); ++i) {
        show_bytes((byte_pointer) &ints[i], sizeof(int));
    }
    for (int i = 0; i < sizeof(longs) / sizeof(long); ++i) {
        show_bytes((byte_pointer) &longs[i], sizeof(long));
    }
    char str[] = "Hello World!";
    show_bytes((byte_pointer) str, sizeof(str));
    show_pointer(ints);
    show_pointer(longs);
    return 0;
}
```

### Homework

Save the solution to a text document and hand it in to your Teaching Assistant (preferably using GitHub).

1. Convert the following decimal numbers to **6-bit** binary numbers (describe how you have done this).
   - Unsigned: `0`, `13`, `24`, `63`
   - Signed: `16`, `-2`, `31`, `-32`

2. Convert the following **6-bit** values to decimal numbers. Consider both unsigned and two's complement formats (provide a formula).
   - Values: `000101`, `101011`, `111111`, `100000`

3. Convert the following decimal values to 8-bit hexadecimal numbers.
   - Values: `7`, `240`, `171`, `126`

4. Convert the following hexadecimal numbers to 8-bit binary values:
   - Values: `0x3C`, `0x7E`, `0xFF`, `0xA5`

5. Negate the binary values (integer negation) from the previous task.

6. Describe how bytes of the `0xDEADBEEF` value would be located in memory for Big- and Little-Endian convention.

7. Convert the following decimal values to **5-bit** binary values. Then sign- and zero-extend them to 8-bit binary values.
   - Values: `7`, `15`, `-16`, `-5`

8. Convert the following pairs decimal numbers to 4-bit binaries and add them.
   - Values: unsigned (`7`, `9`), signed (`4`, `-5`)

### References

- [Machine word](https://en.wikipedia.org/wiki/Word_%28computer_architecture%29) (Wikipedia)
- [Endianness](https://en.wikipedia.org/wiki/Endianness) (Wikipedia)
- Representing information. Chapter 2 in [CSPP]
- Arithmetic for computers. Chapters 3 in [CODR]
- Henry Warren. Hacker's Delight. 2nd Edition. 2013.

---

## Lecture 4: RISC-V Instructions

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/04_Instructions/lecture.html

### Lecture Outline (Theory)

- Stored program concept
- Memory layout
- RISC-V ISA base and extensions
- Instruction encoding (R, I, S, SB, U, and UJ formats)

### Workshop Outline

- Discuss the tasks from the previous seminar
- Get familiar with the RISC-V ISA Specification (instruction types, encodings, etc.)
- Practice with encoding and decoding instructions
- Get familiar with pseudoinstructions
- Understand the use of labels and branch instructions
- Practice writing programs with conditions and loops

### Workshop Tasks (Complete)

See "Chapter 35. RV32/64G Instruction Set Listings" (page 608, pdf page 628) of the RISC-V ISA Manual for instruction encodings.

**Task 1.** Encode the following instructions to the binary format (see the rules in the ISA specification):

- **R-type:**
  ```
  add  t3, t1, t2
  ```

- **I-type:**
  ```
  ori  s0, t1, 0x123
  lw   t1, 0x8(t0)
  ```

- **S-type:**
  ```
  sw   t1, 0x8(t0)
  ```

- **SB-type:**
  ```
  beq t2, t3, 16
  beq t2, t3, -8
  ```

- **U-type:**
  ```
  lui  t0, 0x12345
  ```

- **UJ-type:**
  ```
  jal zero, 16
  ```

**Task 2.** Decode the following hexadecimal values to instructions:

```
0x98765437
0x00744433
0x0080006f
0xfff37293
0x00000463
0x00032823
```

**Task 3.** Get familiar with RISC-V pseudoinstructions (`mv`, `li`, `la`, `b`, `j`, etc). What instructions are used to replace them when a program is assembled:

```
mv t0, t1
li t0, 0x12345678
li t0, 16
b  label
j  label
la t0, label
```

Note: Add `label` to some place in your code. See what code will be generated in the `Execute` panel of RARS.

**Task 4.** Write a program that inputs an integer value `x` and prints `-1` if it is negative, `0` if it equals `0`, and `1` if it is positive.

One of possible solutions:

```assembly
main:
    li   a7, 5
    ecall

    mv   t0, zero
    beqz a0, done
    li   t0, 1
    blt  zero, a0, done
    li   t0, -1
done:
    li a7, 1
    mv a0, t0
    ecall
```

**Task 5.** Write a program that inputs two integer values `x` and `y` and prints first the smallest of them and then the largest of them.

### Homework

Decompile the machine-level program provided below. Convert instructions from the hexadecimal representation into assembly source code. Put the source code into RARS, assemble and run it. Ensure that the code is identical to the one provided below. Submit the resulting assembly text into Ejudge (task "RiscvEncoding1").

```
0x10010437
0x00c04493
0x00942023
0x300002b7
0x00300313
0x0102d293
0x00042483
0x00931333
0x00628463
0x0100006f
0x00000397
0xffc38393
0x00038067
0x00a06893
0x00000073
```

### References

- Machine Language. Section 6.4 in [DDCA]
- [RISC-V Technical Specifications](https://lf-riscv.atlassian.net/wiki/x/kYD2)
- [C++ library for RISC-V instruction encoding](https://github.com/andrewt0301/project_X/blob/main/src/isa/riscv64/riscv64.h)
- [Formal specification of RISC-V ISA](https://github.com/andrewt0301/ispras-microtesk-riscv/blob/master/microtesk-riscv/src/main/arch/riscv/model/riscv_rv32i.nml)

---

## Lecture 5: Pseudo Instructions, Macros, Includes. Conditions, Loops, Loads, and Stores.

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/05_MacroFunctions/lecture.html
(Note: actual folder is `05_MacrosBranchesArrays`)

### Lecture Outline (Theory)

- Program structure: segments and labels
- Branch instructions and their use in conditions and loops
- Data segment and data directives (`.byte`, `.half`, `.word`, `.space`, `.align`, `.ascii`, `.asciz`)
- Load and store instructions and their use for accessing static data
- Macros (directives `.macro`, `.end_macro`, `.include`, and `.eqv`)
- Examples of programs processing static variables stored in the data segment

### Code Examples (Complete)

#### hello.s — Hello World
```assembly
    .data
hello:
    .asciz "Hello, world!"
    .text
main:
    li a7, 4
    la a0, hello
    ecall
```

#### if_then_else.s — Conditional Branching
```assembly
# if (t0 == 0) {
#     t1 = 1;
# } else if (t0 < 0) {
#     t1 = 2;
# } else if (t0 >= 10) {
#     t1 = 3;
# } else {
#     t1 = 4;
# }
main:
   li   a7, 5
   ecall
   mv   t0, a0
if_0:
   bnez t0, if_less_0
   li   t1, 1
   j    end_if
if_less_0:
   bgtz t0, if_greater_10
   li   t1, 2
   j    end_if
if_greater_10:
   li   t3, 10
   ble  t0, t3, else
   li   t1, 3
   j    end_if
else:
   li   t1, 4
end_if:
   li   a7, 1
   mv   a0, t1
   ecall
```

#### while.s — While Loop
```assembly
# while((t0 = read_int()) != 0) {
#    print_int(t0)
#    print_char('\n')
# }
while:
   li   a7, 5
   ecall
   mv   t0, a0
   beqz a0, end_while
   li   a7, 1
   ecall
   li   a7, 11
   li   a0, '\n'
   ecall
   j    while
end_while:
```

#### for.s — For Loop
```assembly
# for (t0 = 0; t0 < t1; ++t0) {
#     print_int(t0)
#     print_char('\n')
# }
for:
   li   a7, 5
   ecall
   mv   t1, a0
   mv   t0, zero
next:
   beq  t0, t1, end_for
   mv   a0, t0
   li   a7, 1
   ecall
   li   a7, 11
   li   a0, '\n'
   ecall
   addi t0, t0, 1
   j    next
end_for:
```

#### for_nested.s — Nested For Loops (uses macrolib)
```assembly
   .include "macrolib.s"
main:
    read_int(s0)
    read_int(s1)
    mv t0, zero
next_t0:
    beq t0, s0, end_for_t0
    mv  t1, zero
next_t1:
   beq t1, s1, end_for_t1
   print_int(t0)
   print_char(':')
   print_int(t1)
   print_char(' ')
   addi t1, t1, 1
   j    next_t1
end_for_t1:
   print_char('\n')
   addi t0, t0, 1
   j    next_t0
end_for_t0:
```

#### macros1.s — Defining and Using Macros
```assembly
   .macro print_int (%x)
   li a7, 1
   mv a0, %x
   ecall
   .end_macro

   .macro print_imm_int (%x)
   li a7, 1
   li a0, %x
   ecall
   .end_macro

   .macro newline
   li a7, 11
   li a0, '\n'
   ecall
   .end_macro

main:
    li t0, 123
    print_int (t0)
    newline
    print_imm_int (456789)
```

#### macros2.s — Using Macros from a Library
```assembly
    .include "macrolib.s"
main:
    print_str("Please enter an integer number:")
    newline
    read_int  (t0)
    print_str("Your number is:")
    newline
    print_int (t0)
```

#### macros3.s — Using the .eqv Directive
```assembly
    .eqv VAL 0x123
    .eqv X t0
    .eqv Y t1
    .eqv SUM addi Y, X, VAL
main:
    li X, 0x111
    SUM
```

#### macrolib.s — Example Macro Library
```assembly
   .macro print_int (%x)
   li a7, 1
   mv a0, %x
   ecall
   .end_macro

   .macro print_imm_int (%x)
   li a7, 1
   li a0, %x
   ecall
   .end_macro

   .macro read_int(%x)
   li a7, 5
   ecall
   mv %x, a0
   .end_macro

   .macro print_str (%x)
   .data
str:
   .asciz %x
   .text
   li a7, 4
   la a0, str
   ecall
   .end_macro

   .macro print_char(%x)
   li a7, 11
   li a0, %x
   ecall
   .end_macro

   .macro newline
   print_char('\n')
   .end_macro
```

#### fibonacci.s — Fibonacci Sequence
```assembly
main:
    mv   t0, zero
    li   t1, 1

    li   a7, 5
    ecall
    mv   t3, a0
fib:
    beqz t3, finish
    add  t2, t1, t0
    mv   t0, t1
    mv   t1, t2
    addi t3, t3, -1
    j    fib
finish:
    li   a7, 1
    mv   a0, t0
    ecall
```

#### data.s — Data and Alignment
```assembly
   .data
   .space 3
word1:
   .word  0x12345678
half1:
   .half  0x1234
byte1:
   .byte  0x12
   .align 4
word2:
   .word 0x12345678
   .align 3
half2:
   .half  0x1234
   .align 3
byte2:
   .byte  0x12
   .align 0
word3:
   .word 0x12345678
```

#### loadstore.s — Load/Store with Memory Variables
```assembly
# x, y, z are stored in memory.
# x = read_int()
# y = read_int()
# z = x + y
   .include "macrolib.s"
   .data
x:
   .word 0
y:
   .word 0
z:
   .word 0
   .text
main:
    read_int(t0)
    la  t2, x
    sw  t0, 0(t2)

    read_int(t0)
    la  t2, y
    sw  t0, 0(t2)

    la  t2, x
    lw  t0, 0(t2)
    la  t2, y
    lw  t1, 0(t2)
    add t3, t0, t1
    la  t2, z
    sw  t3, 0(t2)
```

#### min_max.s — Find Min/Max in Memory Array
```assembly
     .include "macrolib.s"
     .data
data_start:
     .word 11,  2, -1,  5
     .word  3, 10,  7, -3
     .word -5,  4,  0,  9
data_end:
     .text

main:
    la t0, data_start
    la t1, data_end

    lw t2, (t0)
    mv s0, t2 # min = data[0]
    mv s1, t2 # max = data[0]

loop:
    addi t0, t0, 4
    beq  t0, t1, done
    lw   t2, (t0)

test_min:
    bge  t2, s0, test_max
    mv   s0, t2
    j    loop

test_max:
    ble  t2, s1, loop
    mv   s1, t2
    j    loop

done:
   print_str("min=")
   print_int(s0)
   print_str(", max=")
   print_int(s1)
```

#### euclid.s — GCD (Euclidean Algorithm, without functions)
```assembly
# function gcd(a, b)
#    while a ≠ b
#        if a > b
#            a := a − b
#        else
#            b := b − a
#    return a

    .include "macrolib.s"
main:
    read_int (t1)
    read_int (t2)

loop:
    beq t1, t2, finish

    slt t0, t1, t2
    bne t0, zero, if_less

    sub t1, t1, t2
    j   loop

if_less:
    sub t2, t2, t1
    j   loop

finish:
    print_int (t1)
```

### Workshop Tasks (Complete)

**Task 1.** Write a program that inputs two integer values `x` and `y` and prints all the values in the range `min(x, y)..max(x, y)`.

**Task 2.** Write a program that fills an array of 32 integers from values from the standard input. It reads values in a loop and finishes reading when all 32 values are read or when value `0` is read.

**Task 3.** Write a program that inputs two positive integer values `N` and `D`, finds their quotient (`Q`) and remainder (`R`) using the algorithm below, prints the result.

```
function divide_unsigned(N, D)
    Q := 0; R := N
    while R ≥ D do
       Q := Q + 1
       R := R − D
    end
   return (Q, R)
end
```

**Task 4.** Write your own macros `print_hex` and `print_bin` for printing values in hexadecimal and binary formats respectively. What if you want to print immediate values? What kind of macro do you need in this case?

**Task 5.** Write a program that inputs two unsigned integer values `x` and `y`, calculates `x ** y` (`x` raised to the power of `y`), and prints the result. The exponentiation should be implemented as a multiplication in a loop. If an overflow occurs, the program must exit the loop and print an error message.

### Homework

Solve the following tasks and submit them into Ejudge:

1. **DoubleSum** — Enter four integers, one in line, and add unconditionally the first one to the third one, and the second one to the fourth one. Print the results in two lines.

   Input: `234`, `-23`, `23`, `64` → Output: `257`, `41`

2. **DigitSum** — Input an integer (can be negative), and output the sum of its digits.

   Input: `-12345` → Output: `15`

3. **PlusMinus** — Input a cardinal N, then input N integers a(i); output the result of a(0)-a(1)+a(2)-...±a(N-1).

   Input: `4`, `22`, `13`, `14`, `15` → Output: `8`

4. **EvenBack** — Input a cardinal N, then N integers. Output line by line only even ones, in reversed order.

   Input: `6`, `12`, `-11`, `3`, `88`, `0`, `1` → Output: `0`, `88`, `12`

5. **NoDups** — Input a cardinal N, then N integers. Output all the integers, skipping duplicated ones.

   Input: `8`, `12`, `34`, `-12`, `23`, `12`, `-12`, `56`, `9` → Output: `12`, `34`, `-12`, `23`, `56`, `9`

### References

- RARS help system (click in the main menu `Help > Help` or press `F1`)
- Multiplication and division algorithms. Sections 3.3 and 3.4 in [CODR]
- [Macros](https://en.wikipedia.org/wiki/Macro_%28computer_science%29) (Wikipedia)
- [Memory Alignment](https://en.wikipedia.org/wiki/Data_structure_alignment) (Wikipedia)

---

## Lecture 6: Subroutines. Call Stack. Calling Conventions.

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/06_CallStack/lecture.html

### Lecture Outline (Theory)

- Functions, caller, callee
- Jump-and-link instructions
- Register conventions
- Stack pointer
- Stack frame
- Caller-saved and callee-saved registers

### Workshop Theory

#### Saving Registers

- The caller does not know what registers are used by the callee.
- The callee does not know what registers are used by the caller.
- Therefore, the callee is likely to overwrite registers that are important for the caller.
- To avoid this situation, a programmer must follow the calling conventions.

#### Register Conventions

1. **Callee-saved registers:** `sp`, `s0`-`s11`
2. **Caller-saved registers:** `ra`, `a0`-`a7`, `t0`-`t6`

#### What is Done by the Caller?

1. Save all caller-saved registers, which must be preserved, to the stack.
2. Put the arguments into registers `a0`-`a7`.
3. Call the function.
4. When the function returns, read the return values from `a0` and `a1`.
5. Restore all the previously saved caller-saved registers from the stack.

#### What is Done by the Callee?

1. Save all callee-saved registers, which will be modified, to the stack.
2. Perform some operations (if the callee wants to call a function, it becomes the caller for this callee function).
3. Save the result to registers `a0` and `a1`.
4. Restore all the previously saved callee-saved registers from the stack.
5. Return back to the caller.

### Code Examples (Complete)

#### leaf.s — Leaf Function Example
```assembly
# int leaf_example (int g, int h, int i, int j) {
#   int f = (g + h) - (i + j);
#   return f;
# }
# Requirements:
# - arguments g, ..., j in a0(x10)...a3(x13)
# - f in s4 (x20)
# - temporaries t0(x5), t1(x6)
# - need to save t0, t1, s4 on stack
    .text
    .include "macrolib.s"
main:
    read_int(t0) # read g
    read_int(t1) # read h
    read_int(t2) # read i
    read_int(t3) # read j

    mv a0, t0
    mv a1, t1
    mv a2, t2
    mv a3, t3

    jal ra, leaf_example
    mv  t4, a0

    print_int(t0)
    print_char(' ')
    print_int(t1)
    print_char(' ')
    print_int(t2)
    print_char(' ')
    print_int(t3)
    print_char(' ')
    print_int(t4)

    li a7, 10
    ecall

leaf_example:
    addi sp, sp, -12
    sw   t0, 8(sp)
    sw   t1, 4(sp)
    sw   s4, 0(sp)
    add  t0, a0, a1
    add  t1, a2, a3
    sub  s4, t0, t1
    mv   a0, s4
    lw   s4, 0(sp)
    lw   t1, 4(sp)
    lw   t0, 8(sp)
    addi sp, sp, 12
    jalr x0, 0(x1)
```

#### fact.s — Recursive Factorial
```assembly
# int fact (int n) {
#    if (n < 1) {
#        return 1;
#     } else {
#        return n * fact(n - 1);
#     }
# }
    .text
main:
    li a7, 5
    ecall

    jal ra, fact

    li a7, 1
    ecall
exit:
    li a7, 10
    ecall

fact:
    addi t0, a0, -1
    bge  t0, zero, fact_else
    li   a0, 1
    jalr zero, 0(ra)

fact_else:
    addi sp, sp, -8
    sw   ra,  4(sp)
    sw   a0,  0(sp)

    addi a0, a0, -1
    jal  ra, fact
    mv   t1, a0

    lw   a0, 0(sp)
    lw   ra, 4(sp)
    addi sp, sp, 8

    mul  a0, a0, t1
    jalr zero, 0(ra)
```

#### ackermann.s — Ackermann's Three-Argument Function (Recursive)
```assembly
# int alpha(int a, int n) {
#   if (n == 0) return 0;
#   else if (n == 1) return 1;
#   else return a;
# }
#
# int ack(int a, int b, int n) {
#     if (n == 0) return a + b;
#     else if (b == 0) return alpha(a, n - 1);
#     else return ack(a, ack(a, b-1, n), n-1);
# }

.text

.macro read_int(%x)
    li a7, 5
    ecall
    mv %x, a0
.end_macro

main:
    read_int(t0)
    read_int(a1)
    read_int(a2)
    mv a0, t0
    jal ra, ack
    li a7, 1
    ecall
    li a7, 10
    ecall

# a -> a0, b -> a1, n -> a2, return -> a0
ack:
    bne  a2, zero, ack_alpha
    add  a0, a0, a1
    jalr zero, 0(ra)
ack_alpha:
    addi sp, sp, -4
    sw   ra, 0(sp)
    bne  a1, zero, ack_ack
    addi a1, a2, -1
    jal  ra, alpha
    lw   ra, 0(sp)
    addi sp, sp, 4
    jalr zero, 0(ra)
ack_ack:
    addi sp, sp, -8
    sw   a0, 0(sp)
    sw   a2, 4(sp)
    addi a1, a1, -1
    jal ra, ack

    mv   a1, a0
    lw   a0, 0(sp)
    lw   a2, 4(sp)
    addi a2, a2, -1
    jal ra, ack
    addi sp, sp, 8

    lw   ra, 0(sp)
    addi sp, sp, 4
    jalr zero, 0(ra)

# a -> a0, n -> a1, return -> a0
alpha:
    beq a1, zero, alpha_ret_n
    li t0, 1
    beq a1, t0, alpha_ret_n
    jalr zero, 0(ra)
alpha_ret_n:
    mv a0, a1
    jalr zero, 0(ra)
```

#### euclid.s — GCD with Function Call
```assembly
# function gcd(a, b)
#    while a ≠ b
#        if a > b: a := a − b
#        else: b := b − a
#    return a

    .include "macrolib.s"
main:
    read_int (t0)
    read_int (t1)

    mv  a0, t0
    mv  a1, t1
    jal euclid

    li  a7, 1
    ecall
    li a7, 10
    ecall

euclid:
    beq a0, a1, finish
    blt a0, a1, if_less
    sub a0, a0, a1
    j   euclid
if_less:
    sub a1, a1, a0
    j   euclid
finish:
    jr ra
```

### Workshop Tasks (Complete)

**Task 1.** Translate the following C code into the RISC-V assembly language:

```c
int f(int x, int y) {
    return 2 * x + y;
}

int g(int x, int y) {
    return 3 * y - x;
}

int main() {
    int x = read_int();
    int y = read_int();
    int z = f(x, y) + x + g(x, y) - y;
    print_int(z);
}
```

**Task 2.** Translate the following C code into the RISC-V assembly language:

```c
int f(int x, int y) {
    return 2 * x + y;
}

int g(int a, int b, int c, int d) {
    return f(a, c) - f(b, d);
}

int main() {
    int a = read_int();
    int b = read_int();
    int c = read_int();
    int d = read_int();
    int x = g(a, b, c, d);
    print_int(x);
}
```

**Task 3.** Write program `divide.s` that inputs two positive integer values `N` and `D`, finds their quotient (`Q`) and remainder (`R`) using the algorithm below, and prints the result. The algorithm must be implemented as a function.

```pascal
function divide_unsigned(N, D)
    Q := 0; R := N
    while R ≥ D do
        Q := Q + 1
        R := R − D
    end
    return (Q, R)
end
```

**Task 4.** Write program `gcd.s` that inputs two positive integer values `a` and `b`, finds their greatest common divisor using the algorithm below, and prints the result. The algorithm must be implemented as a **recursive function**.

```pascal
function gcd(a, b)
    if b = 0
        return a
    else
        return gcd(b, a mod b)
```

**Task 5.** Write program `fib.s` that inputs integer value `n`, computes n-th Fibonacci number using the algorithm below, and prints the result. The algorithm must be implemented as a **recursive function**.

```c
int fib(int n) {
    if (n < 2)
        return n;
    else
        return fib(n-1) + fib(n-2);
}
```

**Task 6.** Write program `sum.s` that first inputs integer value `n`, after that inputs `n` integer elements and stores them in the stack, then calls function `sum` adding all the elements, and, finally, prints the sum.

### Homework

Solve the following tasks and submit them into Ejudge:

1. **ASCIIGrid** — Write a program that inputs ordinals `M` and `N`, and outputs `M x N` grid made with `+` and `-`. Write a macro `printline` that accepts three parameters: a number of cells and two characters.

   Input: `3`, `4` → Output:
   ```
   +-+-+-+
   | | | |
   +-+-+-+
   | | | |
   +-+-+-+
   | | | |
   +-+-+-+
   | | | |
   +-+-+-+
   ```

2. **CheckTriangles** — Write a program that inputs three integers and calls a function that checks if they can form a triangle (`a <= b+c` is valid; order of values can be any). The function returns: `1` if they can; `2` if they cannot; `0` if all equal `0`. Input values and check them until the check returns `0`.

3. **FuncSort** — Write a program that inputs `N`, then N integer numbers, and outputs them sorted. Write a function for sorting numbers, passing array start and end addresses via `a0` and `a1`.

4. **KeySort** — Write a program of key sorting. Write a subroutine that accepts: (1) array size in words, (2) array address, and (3) comparison subroutine. Write two comparing subroutines: first for `a0 < a1`, second for `a0 % 10 > a1 % 10`. Use bubble sort or any stable sorting.

5. **BinarySearch** — Write a function `binsearch.s` that accepts pointer to sorted array (a0), value to find (a1), start and end indices (a2 and a3). Implement as a **recursive function**:

   ```c
   int binary_search(int *data, int value, int start, int end) {
       int mid = start + (end - start) / 2;
       if (start > end) return -1;
       if (data[mid] == value) return mid;
       if (data[mid] > value) return binary_search(data, value, start, mid - 1);
       return binary_search(data, value, mid + 1, end);
   }
   ```

### References

- Functions in RISC-V. Section 2.8 in [CODR]
- [RISC-V Calling Conventions](https://pdos.csail.mit.edu/6.828/2024/lec/l-riscv-cc-slides.pdf) (MIT slides)
- [Call stack](https://en.wikipedia.org/wiki/Call_stack) (Wikipedia)
- [Calling convention](https://en.wikipedia.org/wiki/Calling_convention) (Wikipedia)

---

## Lecture 7: Floating-Point Format

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/07_FP/lecture.html

### Lecture Outline (Theory)

- Floating-point format
- Standard IEEE 754
- Floating-point instructions
- Programs with floating-point operations

### Floating-Point Register Calling Conventions

| Register | ABI Name | Description               | Saver  |
|----------|----------|---------------------------|--------|
| f0–7     | ft0–7    | FP temporaries            | Caller |
| f8–9     | fs0–1    | FP saved registers        | Callee |
| f10–11   | fa0–1    | FP arguments/return values| Caller |
| f12–17   | fa2–7    | FP arguments              | Caller |
| f18–27   | fs2–11   | FP saved registers        | Callee |
| f28–31   | ft8–11   | FP temporaries            | Caller |

Visualization of floating-point format in RARS: menu `Tools | Floating Point Representation`.

### Code Examples (Complete)

#### fahr1.s — Fahrenheit to Celsius Conversion
```assembly
    .data
consts:
    .float 5.0f
    .float 9.0f
    .float 32.0f

    .text
main:
    li     a7, 6         # read float (into fa0)
    ecall

    jal    ra, f2c       # fa0 = f2c(fa0)

    li     a7, 2         # print float (from fa0)
    ecall
    li     a7, 10        # exit
    ecall

    # float f2c(float fahr) {
    # return ((5.0/9.0)*(fahr - 32.0));
    # }
f2c:
    la     t0, consts
    flw    ft0, 0(t0)    # ft0 = 5.0f
    flw    ft1, 4(t0)    # ft1 = 9.0f
    fdiv.s ft0, ft0, ft1 # ft0 = 5.0f / 9.0f
    flw    ft1, 8(t0)    # ft1 = 32.0f
    fsub.s fa0, fa0, ft1 # fa0 = fahr - 32.0
    fmul.s f10, f0, f10  # fa0 = (5.0f / 9.0f) * (fahr - 32.0f)
    jalr   zero,0(ra)    # return
```

#### e.s — Calculate e (Euler's number) with Specified Precision
```assembly
    .data
one:
    .double 1
ten:
    .double 10

    .text
main:
    fld     f2, one, t0   # 1
    fsub.d  f4, f4, f4    # n = 0
    fmv.d   f6, f2        # n!
    fmv.d   f8, f2        # here will be e
    fld     f10, ten, t0  # here will be ε
    fmv.d   f0, f2        # decimal length k

    li      a7, 5
    ecall
enext:
    blez    a0, edone     # 10 ** (k+1)
    fmul.d  f0, f0, f10
    addi    a0, a0, -1
    j       enext
edone:
    fdiv.d  f10, f2, f0   # ε

loop:
    fadd.d  f4, f4, f2    # n = n+1
    fmul.d  f6, f6, f4    # n! = (n-1)! * n
    fdiv.d  f0, f2, f6    # next summand
    fadd.d  f8, f8, f0
    flt.d   t0, f0, f10   # next summand < ε
    beqz    t0, loop

    li      a7, 3         # output a double
    fmv.d   fa0, f8
    ecall
```

### Workshop Tasks (Complete)

**Task 1.** Find decimal values for the following binary values:

```
0.0
0.01
0.010
0.0011
0.00110
0.001101
0.0011010
0.00110011
```

**Task 2.** Find binary values for the following fractions:

```
1/2
1/8
3/4
5/16
11/32
```

**Task 3.** Find binary values for the following decimal values:

```
0.5
0.25
0.125
1.125
5.875
3.1875
```

**Task 4.** Write program `fprint.s` that inputs a single and double floating-point value and prints them in the binary format.

**Task 5.** Write program `fprint2.s` that separately prints fields (sign, fraction, exponent) of single and double floating-point values. The code of the previous program can be partially reused.

**Task 6.** Write program `farithm.s` that inputs three double values `a`, `b`, and `c`, calculates the result of expression `a + b - c`, and prints the result.

**Task 7.** Write program `even_back.s` that does the following:

Input an integer value `N` and then `N` float values. Output line by line only even ones, in reversed order. To decide whether a float number is even, it must be converted (rounded) to an integer value.

Input:
```
6
12.3
-11.0
3.25
88.01
0.0
1.25
```
Output:
```
0.0
88.01
12.3
```

**Task 8.** Write program `no_dups.s` that does the following:

Inputs an integer `N` value and then N double values. Outputs all the doubles, skipping duplicated ones.

Input:
```
8
12.025
34.5
-12.0
23.25
12.025
-12.0
56.75
9.125
```
Output:
```
12.025
34.5
-12.0
23.25
56.75
9.125
```

### Homework

**1. fraction_truncate.s**

Input three cardinals — `A`, `B` and `n`. Output double float `F` that has exact `n` decimal places of `A/B`. You need to write a subroutine that accepts double `f=A/B` in `fa0` and integer `n` in `a0` and returns rounded double `F` in `fa0`.

Hint: 10^n * A/B < 2^31

Input: `123`, `456`, `7` → Output: `0.2697368`

Spoiler: 10^n * A/B < 2^31 means that you can just take an integer part of it, then divide the result back to 10^n.

**2. cubic_root.s**

Input double (positive or negative) values 1 <= |A| <= 1000000 and 0.00001 <= ε <= 0.01. Calculate a cubical root of A with closeness <= ε (you do not need to round the result).

Input: `1000`, `0.0001` → Output: `9.99995`

Spoiler: suppose solution is between M and N (M < N). Select K=(M+N)/2 and if |K^3| > |A| then solution is between M and K, else it is between K and N.

**3. leibpi.s (Bonus task, +4 bonus points)**

Calculate π value using the [Leibniz formula for π](https://en.wikipedia.org/wiki/Leibniz_formula_for_π) accurate to `N` decimal places. Input `N`, output the result. Use the function defined in FractionTruncate to truncate other digits. Keep in mind that the exact formula calculates π/4 — start with 4 instead of 1 to gain exact accuracy. Warning: the algorithm is slow.

Input: `4` → Output: `3.1416`

### References

- [Standard IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) (Wikipedia)
- Floating point. Section 3.5 in [CODR]
- Floating point. Section 2.4 in [CSPP]
- [RISC-V Assembly Programmer's Manual](https://github.com/riscv-non-isa/riscv-asm-manual/releases/download/v0.0.1/riscv-asm.pdf)

---

## Lecture 8: Memory and Caches

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/08_Caches/lecture.html

### Lecture Outline (Theory)

- Processor-memory performance gap
- Types of memory devices
- Principle of locality and memory hierarchy
- Cache memory (direct-mapped, set-associative, fully-associative)
- Writing and replacement policies
- Multi-level caches
- Performance considerations

### Real CPU Cache Examples

Use `lscpu` to get CPU information.

**Core i7-13700, 12 cores (Lenovo ThinkCenter):**

| Cache Level    | Capacity (Per Core) | Associativity            | Line Size |
|----------------|---------------------|--------------------------|-----------|
| L1 Data        | 48 KB               | 12-way set associative   | 64 bytes  |
| L1 Instruction | 32 KB               | 8-way set associative    | 64 bytes  |
| L2 Cache       | 2 MB                | 16-way set associative   | 64 bytes  |
| L3 Cache       | 30 MB (Total Shared)| 12-way set associative   | 64 bytes  |

**Core i7-1260P, 8 cores (Huawei MateBook):**

| Cache Level    | Capacity (Per Core) | Associativity            | Line Size |
|----------------|---------------------|--------------------------|-----------|
| L1 Data        | 48 KB               | 12-way set associative   | 64 bytes  |
| L1 Instruction | 32 KB               | 8-way set associative    | 64 bytes  |
| L2 Cache       | 1.25 MB             | 10-way set associative   | 64 bytes  |
| L3 Cache       | 18 MB (Total Shared)| 12-way set associative   | 64 bytes  |

**Core i7-8665U, 4 cores (Lenovo ThinkPad):**

| Cache Level    | Capacity (Per Core) | Associativity            | Line Size |
|----------------|---------------------|--------------------------|-----------|
| L1 Data        | 32 KB               | 8-way set associative    | 64 bytes  |
| L1 Instruction | 32 KB               | 8-way set associative    | 64 bytes  |
| L2 Cache       | 256 KB              | 4-way set associative    | 64 bytes  |
| L3 Cache       | 8 MB (Total Shared) | 16-way set associative   | 64 bytes  |

### Workshop Code Examples (Complete)

#### Linear Memory Accesses
```assembly
    .eqv  START 0x10010000
    .eqv  SZ    512
    .text
    li    s0, START
    addi  s1, s0, SZ
loop:
    lw    t0, 0(s0)
    addi  s0, s0, 4
    blt   s0, s1, loop
```

#### Gaped Memory Accesses
```assembly
    .eqv  START 0x10010000
    .eqv  HSZ   256
    # Direct mapping burns out
    # Associative captures
    .text
    li    s0, START
    addi  s1, s0, HSZ
    mv    s2, s1
loop:
    lw    t0, 0(s0)
    lw    t1, 0(s1)
    addi  s0, s0, 4
    addi  s1, s1, 4
    blt   s0, s2, loop
```

#### Equidistance (Try to Vary Step)
```assembly
    .eqv  START 0x10010000
    .eqv  SZ    256
    .eqv  GAP   3   # Try 5, 11
    .text

    li    s0, START  # start address
    addi  s1, s0, SZ # end address
    li    s2, GAP    # gap in words
    slli  s3, s2, 2  # gap in bytes

    mv    t0, zero
loop_gap:
    slli  t1, t0, 2
    add   t1, s0, t1
loop:
    lw    t2, 0(t1)

    add   t1, t1, s3
    blt   t1, s1, loop

    addi  t0, t0, 1
    blt   t0, s2, loop_gap
```

### Workshop Tasks (Complete)

**Task 1.** Assume the miss rate of an instruction cache is 2% and the miss rate of the data cache is 4%. If a processor has a CPI of 2 without any memory stalls, and the miss penalty is 100 cycles for all misses, determine how much faster a processor would run with a perfect cache that never missed. Assume the frequency of all loads and stores is 36%.

**Task 2.** Find the AMAT for a processor with a 1 ns clock cycle time, a miss penalty of 20 clock cycles, a miss rate of 0.05 misses per instruction, and a cache access time (including hit detection) of 1 clock cycle. Assume that the read and write miss penalties are the same and ignore other write stalls.

**Task 3.** Use the system with access times of 1, 10, and 100 cycles for the L1 cache, L2 cache, and main memory, respectively. Assume that the L1 and L2 caches have miss rates of 5% and 20%, respectively. Specifically, of the 5% of accesses that miss the L1 cache, 20% of those also miss the L2 cache. What is the average memory access time (AMAT)?

**Task 4.** Assuming a cache of 4096 blocks, a four-word block size, and a 64-bit address, find the total number of sets and the total number of tag bits for caches that are direct-mapped, two-way and four-way set associative, and fully associative.

**Task 5.** Try the above examples with following cache configurations (`Tool | Data Cache Simulator`):

- Placement policy: Direct Mapping / Fully Associative / 2-Way associative
- Block replacement policy: LRU / Random

2×3=6 experiments in total. Report the cache hit rate.

**Task 6.** Write a program that:
- Burns out default fully associative cache with 100% misses
- Does this in a cycle (if previously not)
- Fills only 256 bytes of memory without a gap

**Task 7.** Write a program that utilizes memory sparsely, so that its footprint is 100% misses on a 2-way associative cache. However, it fits (almost) into a 4-way associative cache with 16 blocks.

### Homework

Write a function with label `multiply_matrices:`, which multiplies two matrices of double values (i.e. performs `C = A * B`). The function must accept:

- `a0` - matrix size (elements in rows and columns)
- `a1` - A matrix address (input)
- `a2` - B matrix address (input)
- `a3` - C matrix address (output)

The function will be merged with test program `matrix.s` (generates random matrices of the given size, multiplies them, and prints the result).

Input (matrix size): `4`

Output (matrices A, B, and C):
```
-1.0 1.0 1.0 8.0
8.0 -5.0 1.0 7.0
4.0 -6.0 2.0 -3.0
-5.0 -5.0 5.0 9.0

-2.0 -4.0 -7.0 -3.0
5.0 8.0 -6.0 -4.0
5.0 -8.0 7.0 -4.0
-4.0 4.0 1.0 7.0

-20.0 36.0 16.0 51.0
-64.0 -52.0 -12.0 41.0
-16.0 -92.0 19.0 -17.0
-26.0 -24.0 109.0 78.0
```

*Self-study activity:* Try different implementations (orders of loops `i`, `j`, `k`) and different cache configurations (RARS Data Cache Simulator). See how this affects miss/hit rate.

#### matrix.s — Test Program for Matrix Multiplication
```assembly
.macro print_char(%x)
     li  a7, 11
     li  a0, %x
     ecall
.end_macro

    .text
    .globl main
main:
    li   a7, 5
    ecall
    mv   s0, a0         # s0 = matrix size
    call allocate_matrix # s1 = matrix A
    mv   s1, a0
    mv   a1, s0
    li   a2, 13
    call generate_matrix

    mv   a0, s0          # s2 = matrix B
    call allocate_matrix
    mv   s2, a0
    mv   a1, s0
    li   a2, 17
    call generate_matrix

    mv   a0, s0          # s3 = matrix C
    call allocate_matrix
    mv   s3, a0
    mv   a1, s0
    call reset_matrix

    mv   a0, s0          # C = A * B
    mv   a1, s1
    mv   a2, s2
    mv   a3, s3
    call multiply_matrices

    # Print matrices A, B, C
    mv   a0, s1
    mv   a1, s0
    call print_matrix
    print_char('\n')
    mv   a0, s2
    mv   a1, s0
    call print_matrix
    print_char('\n')
    mv   a0, s3
    mv   a1, s0
    call print_matrix
    print_char('\n')
    li a7, 10
    ecall

allocate_matrix:
    mul  a0, a0, a0
    slli a0, a0, 3
    li   a7, 9
    ecall
    ret

generate_matrix:
    mv   t0, a0
    mul  t1, a1, a1
    li   t3, 10
    li   a7, 40
    mv   a0, zero
    mv   a1, a2
    ecall
generate_matrix.next:
    beqz  t1, generate_matrix.end
    li   a7, 41
    mv   a0, zero
    ecall
    rem  a0, a0, t3
    addi a0, a0, 1
    fcvt.d.w ft0, a0
    fsd  ft0, 0(t0)
    addi t0, t0, 8
    addi t1, t1, -1
    j    generate_matrix.next
generate_matrix.end:
    ret

reset_matrix:
    mul  a1, a1, a1
    fcvt.d.w ft0, zero
reset_matrix.next:
    beqz a1, reset_matrix.end
    fsd  ft0, 0(a0)
    addi a0, a0, 8
    addi a1, a1, -1
    j    reset_matrix.next
reset_matrix.end:
    ret

print_matrix:
    mv   t2, a0
    mv   t0, zero
print_matrix.next_row:
    beq  t0, a1, print_matrix.end_row
    mv   t1, zero
print_matrix.next_col:
    beq  t1, a1, print_matrix.end_col
    fld  fa0, 0(t2)
    li   a7, 3
    ecall
    addi t2, t2, 8
    print_char(' ')
    addi t1, t1, 1
    j    print_matrix.next_col
print_matrix.end_col:
    print_char('\n')
    addi t0, t0, 1
    j    print_matrix.next_row
print_matrix.end_row:
    ret
```

### References

- Large and Fast: Exploiting Memory Hierarchy. Chapter 5 in [CODR]
- Ulrich Drepper. What Every Programmer Should Know About Memory
- [CPU cache](https://en.wikipedia.org/wiki/CPU_cache) (Wikipedia)

---

## Lecture 9: Virtual Memory

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/09_VM/lecture.html

### Lecture Outline (Theory)

- Virtual memory and physical memory
- Virtual and physical addresses
- Address translation and page table
- Translation lookaside buffer (TLB)

### Address Sizes for Real CPUs

```
AMD Ryzen AI 9 HX 370 w/ Radeon 890M:
  Address sizes: 48 bits physical, 48 bits virtual

Intel Core i7-13700:
  Address sizes: 46 bits physical, 48 bits virtual

Intel Core i7-1260P:
  Address sizes: 39 bits physical, 48 bits virtual

Intel Core i7-8665U:
  Address sizes: 39 bits physical, 48 bits virtual
```

Modern CPUs are typically limited to 48-bit virtual addresses because this is more than enough for modern data volumes (2^48 = 256 TB). Using 48 bits rather than 64 simplifies hardware (smaller cache tags and TLBs) and page tables.

### Multi-level Page Tables (Intel)

Intel supports both 4-level and 5-level paging. To check which paging option is enabled in Linux kernel:

```bash
cat /boot/config-$(uname -r) | grep -E "X86_[45]LEVEL|PGTABLE_LEVELS"
CONFIG_PGTABLE_LEVELS=5
CONFIG_X86_5LEVEL=y
```

### Address Types Used for Caching

| Cache Level | Addressing Type | Explanation |
|-------------|----------------|-------------|
| L1 Cache | VIPT (Virtually Indexed, Physically Tagged) | Uses virtual address for fast indexing while simultaneously performing TLB lookup for physical tag. Allows cache access to start before address translation finishes. |
| L2 Cache | PIPT (Physically Indexed, Physically Tagged) | Addressed entirely by physical address. More accurate for larger cache but requires translation to complete first. |
| L3 Cache | PIPT (Physically Indexed, Physically Tagged) | Since L3 is shared across all cores (Smart Cache), it must use physical addresses for consistency. |

### TLB Configuration for i7-8665U

Each of the 4 cores has its own dedicated Level 1 TLB and shares a Level 2 TLB:

- **L1 Instruction TLB (iTLB):**
  - 4 KB Pages: 64 entries (8-way set associative)
  - 2 MB / 4 MB Pages: 8 entries (fully associative)

- **L1 Data TLB (dTLB):**
  - 4 KB Pages: 64 entries (4-way set associative)
  - 2 MB / 4 MB Pages: 32 entries (4-way set associative)
  - 1 GB Pages: 4 entries (4-way set associative)

- **L2 Shared TLB (STLB):**
  - Large Unified Buffer: 1,536 entries (12-way set associative)
  - Shared between instructions and data to catch L1 misses. Supports 4 KB and 2 MB page sizes.

### Workshop Outline

- Memory configuration (check in different machines)
- Memory layout (RISC-V)
- System calls (practical tasks)

### System Calls in RARS

*System calls* are actions requested by user code and serviced by the environment (operating system). The environment executes the service code in kernel mode (has full access to all resources).

**Standard system calls supported by RARS:**

1. **open** (1024): opens a file with the specified path
   - Input: `a0` = Null terminated string for the path, `a1` = flags
   - Output: `a0` = the file descriptor or -1 if an error occurred
   - Supported flags: read-only (0), write-only (1), and write-append (9)

2. **close** (57): closes a file
   - Input: `a0` = the file descriptor to close
   - Output: N/A

3. **read** (63): reads from a file descriptor into a buffer
   - Input: `a0` = the file descriptor, `a1` = address of the buffer, `a2` = maximum length to read
   - Output: `a0` = the length read or -1 if error

4. **write** (64): writes to a file from a buffer
   - Input: `a0` = the file descriptor, `a1` = the buffer address, `a2` = the length to write
   - Output: `a0` = the number of characters written

5. **sbrk** (9): allocates heap memory
   - Input: `a0` = amount of memory in bytes
   - Output: `a0` = address to the allocated block

### Code Examples (Complete)

#### file_write.s — Writing Text to a File
```assembly
  .data
fout:
  .asciz "testout.txt"
buffer:
  .asciz "The quick brown fox jumps over the lazy dog."
  .text

  li   a7, 1024     # system call for open file
  la   a0, fout     # output file name
  li   a1, 1        # Open for writing (flags are 0: read, 1: write)
  ecall             # open a file (file descriptor returned in a0)
  mv   s6, a0       # save the file descriptor

  li   a7, 64       # system call for write to file
  mv   a0, s6       # file descriptor
  la   a1, buffer   # address of buffer from which to write
  li   a2, 44       # hardcoded buffer length
  ecall             # write to file

  li   a7, 57       # system call for close file
  mv   a0, s6       # file descriptor to close
  ecall             # close file

  li a7, 10         # system call to exit program
  ecall
```

#### file_read.s — Reading Text from a File
```assembly
  .data
fin:
  .asciz "testout.txt"
error:
  .asciz "Error: failed to open a file."
buffer:
  .space 33

  .text
main:
  la   s0, buffer
  li   s1, 32

  li   a7, 1024
  la   a0, fin
  li   a1, 0        # open for reading
  ecall

  bltz a0, main.error
  mv   s6, a0

main.loop:
  li   a7, 63       # system call for read from file
  mv   a0, s6
  mv   a1, s0
  mv   a2, s1
  ecall

  bltz a0, main.close
  mv   t0, a0
  add  t1, s0, a0
  sb   zero, 0(t1)  # write zero terminator

  li   a7, 4
  mv   a0, s0
  ecall

  beq  t0, s1, main.loop

main.close:
  li   a7, 57
  mv   a0, s6
  ecall

main.exit:
  li   a7, 10
  ecall

main.error:
  li   a7, 4
  la   a0, error
  ecall
```

#### heap_alloc.s — Allocating Memory in the Heap
```assembly
  .macro new_line
    li a7, 11
    li a0, '\n'
    ecall
  .end_macro

  .macro sbrk(%bytes)
    li a7, 9
    li a0, %bytes
    ecall
  .end_macro

  .text
  sbrk(16)          # Allocates 16 bytes in the heap
  li a7, 34
  ecall
  new_line

  sbrk(32)          # Allocates 32 bytes in the heap
  li a7, 34
  ecall
  new_line

  sbrk(64)          # Allocates 64 bytes in the heap
  li a7, 34
  ecall
  new_line

  li a7, 10
  ecall
```

### Workshop Tasks (Complete)

**Task 1 (VM).** Consider a virtual memory system that can address a total of 32 GB (2^35 bytes). You have unlimited hard drive space, but are limited to 2 GB (2^31 bytes) of semiconductor (physical) memory. Assume that virtual and physical pages are each 4 KB (2^12 bytes) in size.

- How many bits is the physical address?
- What is the maximum number of virtual pages in the system?
- How many physical pages are in the system?
- How many bits are the virtual and physical page numbers?
- How many page table entries will the page table contain?

**Task 2 (Syscalls).** Write a program that creates a copy of the specified file. Input arguments:
- The name of the source and target files are read from the standard input (use system call 8).
- The buffer to store data being copied is allocated in the heap (use system call 9). The buffer size is specified in standard input.
- Buffers for storing source and target names are also allocated in the heap (their size is 256 bytes).

### Homework: PseudoVM

*NOTE: Need to cover exceptions first to be able to solve this task.*

Write an exception handler that imitates "virtual memory" for "forbidden" addresses. A "forbidden" address is any address that causes exceptions `LOAD_ACCESS_FAULT` and `STORE_ACCESS_FAULT` when we try to access it. This is not supported for address `0x0` (reserved).

Create a table (array) that stores records `"virtual address":value` (pairs of 4-byte values). The capacity of the table is 16 records (i.e. 2×4×16=128 bytes). Address `0x0` is used to specify an empty record.

"Virtual memory" works only with instructions `lw` and `sw` that use register `t0` as source/destination.

**Reading from an address:**
- If the address is present in the table, the value stored in the table is returned.
- If the address is missing from the table, `0` is returned.

**Writing to an address:**
- If the address is present in the table, the value stored in the record is updated.
- If the address is missing from the table but the table has free records, a new record is placed.
- If the address is missing from the table and it's full, nothing happens.

#### PseudoVM.s — Main Test Program
```assembly
.globl	main
.text
main:
    la      t1, handler
    csrw    t1, utvec
    csrsi   ustatus, 1

loop:
    li      a7, 5       # Input address
    ecall
    beqz    a0, done    # If 0, then done
    andi    t0, a0, 3   # Is address multiple of 4?
    beqz    t0, read    # If yes, then read

write:
    andi    a0, a0, -4  # Write, erase 2 lower bits of address
    mv      t1, a0      # This is "virtual address"
    li      a7, 5       # Input value
    ecall
    mv      t0, a0      # Prepare t0
    sw      t0, (t1)    # Write a value to "virtual memory"
    b       loop

read:
    lw      t0, (a0)    # Read a value from "virtual memory"
    mv      a0, t0
    li      a7, 1       # Print the value
    ecall
    li      a0, '\n'
    li      a7, 11
    ecall
    b       loop

done:
    li      a7, 10      # Exit
    ecall
```

Input:
```
21
123
22
1234
20
1001
100500
1000
100
-70001
-70001
-70000
-70004
0
```

Output:
```
1234
100500
0
0
-70001
```

### References

- Virtual Memory. Section 8.4 in [DDCA]
- Large and Fast: Exploiting Memory Hierarchy. Chapter 5 in [CODR]
- Virtual Memory. Chapter 9 in [CSPP]
- [Translation lookaside buffer](https://en.wikipedia.org/wiki/Translation_lookaside_buffer) (Wikipedia)
- [Intel 5-level paging](https://en.wikipedia.org/wiki/Intel_5-level_paging) (Wikipedia)

---

## Lecture 11: Exceptions and Interrupts

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/11_Exceptions/lecture.html

### Lecture Outline (Theory)

- Exceptions and interrupts

### Workshop Theory

#### Exceptions and Interrupts

**Exception** is an unscheduled event that disrupts program execution.
**Interrupt** is an exception that comes from outside of the processor. (Some architectures use the term interrupt for all exceptions.)

Exceptions require dealing with special system instructions and registers.

### Control and Status Registers (CSRs)

The CSRs are system registers provided by RISC-V to control and monitor system states. CSRs can be read, written and bits can be set/cleared. Each CSR has a special name and is assigned a unique function. In this course, we focus on the user privilege level.

#### User-level CSRs

| Number | Privilege | Name     | Description                                          |
|--------|-----------|----------|------------------------------------------------------|
| **User Trap Setup** |
| 0x000  | URW       | ustatus  | User status register                                 |
| 0x004  | URW       | uie      | User interrupt-enable register                       |
| 0x005  | URW       | utvec    | User trap handler base address                       |
| **User Trap Handling** |
| 0x040  | URW       | uscratch | Scratch register for user trap handlers              |
| 0x041  | URW       | uepc     | User exception program counter                       |
| 0x042  | URW       | ucause   | User trap cause                                      |
| 0x043  | URW       | utval    | User bad address or instruction                      |
| 0x044  | URW       | uip      | User interrupt pending                               |
| **User Floating-Point CSRs** |
| 0x001  | URW       | fflags   | Floating-Point Accrued Exceptions                    |
| 0x002  | URW       | frm      | Floating-Point Dynamic Rounding Mode                 |
| 0x003  | URW       | fcsr     | Floating-Point Control and Status Register           |
| **User Counter/Timers** |
| 0xC00  | URO       | cycle    | Cycle counter for RDCYCLE instruction                |
| 0xC01  | URO       | time     | Timer for RDTIME instruction                         |
| 0xC02  | URO       | instret  | Instructions-retired counter for RDINSTRET instruction |
| 0xC80  | URO       | cycleh   | Upper 32 bits of cycle, RV32 only                    |
| 0xC81  | URO       | timeh    | Upper 32 bits of time, RV32 only                     |
| 0xC82  | URO       | instreth | Upper 32 bits of instret, RV32 only                  |

### System Instructions

#### CSR Instructions

| Instruction              | Description |
|--------------------------|-------------|
| `csrrc  t0, fcsr, t1`   | Read/Clear CSR: read from the CSR into t0 and clear bits of the CSR according to t1 |
| `csrrci t0, fcsr, 10`   | Read/Clear CSR Immediate: read from the CSR into t0 and clear bits of the CSR according to a constant |
| `csrrs  t0, fcsr, t1`   | Read/Set CSR: read from the CSR into t0 and logical or t1 into the CSR |
| `csrrsi t0, fcsr, 10`   | Read/Set CSR Immediate: read from the CSR into t0 and logical or a constant into the CSR |
| `csrrw  t0, fcsr, t1`   | Read/Write CSR: read from the CSR into t0 and write t1 into the CSR |
| `csrrwi t0, fcsr, 10`   | Read/Write CSR Immediate: read from the CSR into t0 and write a constant into the CSR |

#### CSR Pseudo Instructions

| Instruction          | Description |
|----------------------|-------------|
| `csrc  t1, fcsr`    | Clear bits in control and status register |
| `csrci fcsr, 100`   | Clear bits in control and status register |
| `csrr  t1, fcsr`    | Read control and status register |
| `csrs  t1, fcsr`    | Set bits in control and status register |
| `csrsi fcsr, 100`   | Set bits in control and status register |
| `csrw  t1, fcsr`    | Write control and status register |
| `csrwi fcsr, 100`   | Write control and status register |

#### System Instructions

| Instruction | Description |
|-------------|-------------|
| `ebreak`    | Pause execution (at a breakpoint) |
| `ecall`     | Issue a system call: Execute the system call specified by value in a7 |
| `uret`      | Return from handling an interrupt or exception (to uepc) |
| `wfi`       | Wait for interrupt |

### Exceptions Supported in RARS

#### INSTRUCTION_ADDR_MISALIGNED (ucause = 0)
```assembly
    j main
end:
    li a7, 10
    ecall
main:
    la t0, end
    addi t0, t0, 2
    jr t0
```
Result: `Error: Instruction load alignment error, ucause = 0x0, uepc = 0x00400006`

#### INSTRUCTION_ACCESS_FAULT (ucause = 1)
```assembly
  .data
data:
  .word 99
  .word 100
.text
  la t0, data
  jr t0
```
Result: `Error: Instruction load access error, ucause = 0x00000001, uepc = 0x10010000`

#### ILLEGAL_INSTRUCTION (ucause = 2)
```assembly
  .text
main:
  li    t0, 8
  csrrs zero, cycle, t0
```
Result: `Error: Attempt to write to read-only CSR, ucause = 0x00000002, uepc = 0x00400004`

#### LOAD_ADDRESS_MISALIGNED (ucause = 4)
```assembly
  .data
  .space 2
  .align 0
data:
  .word 0xDEADBEEF
  .text
main:
  la t0, data
  lw t1, 0(t0)
```
Result: `Error: Load address not aligned to word boundary 0x10010002, ucause = 0x00000004`

#### LOAD_ACCESS_FAULT (ucause = 5)
```assembly
.text
main:
  la t0, main
  lw t1, 0(t0)
```
Result: `Error: Cannot read directly from text segment!0x00400000, ucause = 0x00000005`

#### STORE_ADDRESS_MISALIGNED (ucause = 6)
```assembly
  .data
  .space 2
  .align 0
data:
  .word 0
  .text
main:
  la t0, data
  li t1, 0xDEADBEEF
  sw t1, 0(t0)
```
Result: `Error: Store address not aligned to word boundary 0x10010002, ucause = 0x00000006`

#### STORE_ACCESS_FAULT (ucause = 7)
```assembly
.text
main:
  la t0, main
  li t1, 0xDEADBEEF
  sw t1, 0(t0)
```
Result: `Error: Cannot write directly to text segment!0x00400000, ucause = 0x00000007`

#### ENVIRONMENT_CALL (ucause = 8)
```assembly
  .text
main:
  li a7, 100
  ecall
```
Result: `Error: invalid or unimplemented syscall service: 100, ucause = 0x00000004`

### Exception Handling

When an exception occurs the following actions are performed:

1. The `uie` (interrupt enable) bit in the status word is set to `0`
2. The `ucause` register is set to indicate which event has occurred
3. The `uepc` is set to the last instruction that was executing when system trapped
4. The PC is set to `utvec` value; in case of vectored exception handling, the PC is set `utvec` base address + 4 × `ucause`

In order to have a working exception handler, the program must:

1. Set `utvec` to the address of the handler code (the lowest two bits are special)
2. Set the bits corresponding to the handled interrupts in `uie`
3. Set the interrupt enable (lowest) bit in `ustatus` to enable the handler

#### Simplest Exception Handler
```assembly
handler:
     csrrw t0, uepc, zero
     addi  t0, t0, 4
     csrrw zero, uepc, t0
     uret
```

#### Handler Registration
```assembly
     la     t0, handler      # load handler address to t0
     csrrw  zero, utvec, t0  # set utvec to the handler's address
     csrrsi zero, ustatus, 1 # set interrupt enable bit in ustatus
```

### Code Examples (Complete)

#### simple_handler.s — Trivial Exception Handler
```assembly
     .text
     j main
handler:
     csrrw  t0, uepc, zero  # load exception PC into t0
     addi   t0, t0, 4       # increment t0
     csrrw  zero, uepc, t0  # update exception PC
     uret                   # return to uepc
main:
     la     t0, handler
     csrrw  zero, utvec, t0  # set utvec (5) to the handler's address
     csrrsi zero, ustatus, 1 # set interrupt enable bit in ustatus (0)
     lw     zero, 0(zero)    # trigger trap for Load access fault

     li     a7, 10
     ecall
```

#### timer.s — Timer Interrupt Handling
```assembly
.eqv CUR 0xFFFF0018 # current time
.eqv NEW 0xFFFF0020 # time for new interrupt

.macro timer(%timeout)
    lw     t0, CUR
    addi   t0, t0, %timeout
    sw     t0, NEW, t1
.end_macro

.text
    j main
handler:
    timer  2000 # generate new timer interrupt after 2000 ms
    uret        # return to uepc
main:
    la     t0, handler
    csrrw  zero, utvec, t0    # set utvec to the handler's address
    csrrsi zero, ustatus, 0x1 # set interrupt enable bit in ustatus
    csrrsi zero, uie, 0x10   # timer interrupts are enabled (UTIE bit)
    timer  2000               # generate new timer interrupt after 2000 ms
loop:
    wfi
    j loop
    nop
```

### Workshop Tasks (Complete)

**Task 1.** Study the theory and examples on the current workshop.

**Task 2.** Implement an exception handler that prints a message that explains the reason of an exception (the list of exceptions with descriptions is above).

**Task 3.** Imagine how the `try-catch` construct is implemented in high-level languages. Then write a program that implements a simple function with an exception handler. The function takes an argument that specifies what exception it will raise (`0` - no exception, `1` - some exception from list above, `2` - some other exception from the list). The function must return the exception cause or `0` if no exception has occurred.

Pseudocode:
```c++
int testFunc(int arg) {
  try {
    if (arg == 1) {
      // divide by zero (or some other)
    }
    else if (arg == 2) {
      // read unaligned address (or some other)
    }
  } catch(const KnownException& e) {
    return e.cause();
  }
  return 0;
}

int main() {
  int a = readInt();
  int b = testFunc(a);
  printInt(a);
  return 0;
}
```

**Task 4.** See example `timer.s`. Write a program that waits for timer interrupts and counts them. Input data: `m` is the limit on number of interrupts to process, `t` is the interval between interrupts in milliseconds. The program exits when the number of handled interrupts reaches the limit.

Hints: Use the `Tools | Timer Tool` RARS extension. MMIO address to get the current time: `0xFFFF0018`; MMIO address to set the time for the next interrupt: `0xFFFF0020`. Setup requirements:

- Store address of interrupt handler in the `utvec` CSR
- Set the fourth bit of the `uie` CSR to `1` (i.e. `ori uie, uie, 0x10`)
- Set the zeroth bit of the `ustatus` CSR to `1` (i.e. `ori ustatus, ustatus, 0x1`)
- Write the time for the next interrupt to `0xFFFF0020`
- When an interrupt is handled, update the time for the next interrupt

### Homework

*NOTE: Handling exception `ENVIRONMENT_CALL` requires a patch in RARS.*

**1. NoError** — Write a program that inputs 10 integers, not taking in account failed inputs. When all 10 integers are read, the program outputs them. Use an array to store successful inputs and a universal exception handler that marks a specific memory variable to skip unsuccessful ones.

Input: `zz`, `20`, `fwewefqwe`, `.654`, `71`, `-124`, `0.1`, `82`, `6.`, `334423`, `-94`, `VII`, `7535`, `6`, `.`, `-`, `17`, `8968`

Output: `20`, `71`, `-124`, `82`, `334423`, `-94`, `7535`, `6`, `17`, `8968`

**2. NewEcall** — Write an exception handler with label `handler:`, which implements three "new system calls" (`100`, `101`, and `102`) for working with "hidden registers":

- `100` (a0 = size): Called only once. Allocates memory with size of `size` machine words ("hidden registers") and saves the size internally.
- `101` (a0 = number): Reads a value of a "hidden register" with index `number` and saves it into `a0`. If `number >= size`, the index is calculated as `number % size`.
- `102` (a0 = number, a1 = value): Writes the value into "hidden register" with index `number % size`.

Implement an exception handler that checks whether `ucause` equals `ENVIRONMENT_CALL` and gets system call ID from `a7`. The handler must preserve the state of registers it uses.

#### new_ecall.s — Test Program for NewEcall
```assembly
.text
.globl	main
main:	la	t1, handler
	csrw	t1, utvec
	csrsi	ustatus, 1

        li      a7, 5       # Read 'size' and allocate "hidden registers"
        ecall
        li      a7, 100
        ecall

rloop:  li      a7, 5       # Read a number
        ecall
        beqz    a0, rdone   # Zero means done
        bltz    a0, rread   # Negative means read
        mv      t0, a0      # Positive means write
        li      a7, 5       # Read value
        ecall
        mv      a1, a0
        mv      a0, t0
        li      a7, 102
        ecall
        b       rloop

rread:  neg     a0, a0      # Negative means read
        li      a7, 101
        ecall
        li      a7, 1
        ecall
        li      a0, '\n'
        li      a7, 11
        ecall
        b       rloop

rdone:	li	a7, 10
	ecall
```

Input:
```
8
1
1234
-9
-2
1
4213
2
-1
-7
-2
-1
0
```
Output:
```
1234
0
0
-1
4213
```

### References

- Krste Asanović. Interrupts (RISC-V presentation)
- Chapter 6: "N" Standard Extension for User-Level Interrupts (RISC-V Privileged Architecture)
- The Processor. Chapter 4 in [CODR]
- [Interrupt](https://en.wikipedia.org/wiki/Interrupt) (Wikipedia)

---

## Lecture 12: Memory-Mapped I/O (MMIO)

**URL:** https://andrewt0301.github.io/hse-acos-course/part1ca/12_MMIO/lecture.html

### Lecture Outline (Theory)

- I/O devices
- Direct memory access (DMA)
- Memory-mapped I/O (MMIO)
- Drivers
- RARS MMIO device emulators

### Code Examples (Complete)

#### digit_lab1.s — Seven Segment Display
```assembly
    .text
main:
    lui   t3, 0xffff0    # MMIO address high half
    li    t1, 0xdb
    sb    t1, 0x10(t3)   # (0xffff0000+0x10)
    li    t2, 0x66
    sb    t2, 0x11(t3)   # (0xffff0001+0x10)
```

#### digit_lab2.s — Hexadecimal Keyboard
```assembly
.macro exit
    li      a7, 10
    ecall
.end_macro

.macro print_hex(%x)
    mv      a0, %x
    li      a7, 34
    ecall
.end_macro

.macro newline
    li      a0, '\n'
    li      a7, 11
    ecall
.end_macro

    .text
main:
    lui     s0, 0xffff0   # MMIO base
    mv      s1, zero      # counter
    mv      s2, zero      # previous value
    li      s3, 20        # counter limit
loop:
    li      t0, 1         # check first row
    sb      t0, 0x12(s0)  # scan
    lbu     t1, 0x14(s0)  # get result
    bnez    t1, pressed   # process key pressed

    li      t0, 2         # check second row
    sb      t0, 0x12(s0)
    lbu     t1, 0x14(s0)
    bnez    t1, pressed

    li      t0, 4         # check third row
    sb      t0, 0x12(s0)
    lbu     t1, 0x14(s0)
    bnez    t1, pressed

    li      t0, 8         # check fourth row
    sb      t0, 0x12(s0)
    lbu     t1, 0x14(s0)
    bnez    t1, pressed

    mv      s2, zero  # reset previous value
    j       loop      # nothing is pressed (t1 == 0) - repeat
pressed:
    beq     t1, s2, loop # repeat if the same key value
    mv      s2, t1       # save current value

    print_hex(t1)
    newline

    addi    s1, s1, 1    # counter increment
    ble     s1, s3, loop # repeat if s1 <= s3
end:
    exit
```

#### bitmap_display.s — Bitmap Display with Random Pixels
```assembly
    .eqv   ALLSIZE 0x20000      # videomemory size (in words)
    .eqv   BASE    0x10010000   # MMIO base
    .text
    li s0, BASE
again:
    mv   a0, zero
    li   a1, ALLSIZE             # Max 512*Y+X + 1
    li   a7, 42
    ecall                        # random 512*Y+X

    slli t2, a0, 2               # make an address by multiplying to 4
    add  t2, s0, t2

    mv   a0, zero
    li   a1, 0x1000000           # MAX RGB value + 1
    li   a7, 42
    ecall                        # random color

    sw   a0, 0(t2)
    j    again
```

#### mmio_poll.s — Polling-based I/O
```assembly
  .text
  .eqv IN_CTRL  0xffff0000
  .eqv IN       0xffff0004
  .eqv OUT_CTRL 0xffff0008
  .eqv OUT      0xffff000C
main:
  li   s0, IN_CTRL
  li   s1, IN
  li   s2, OUT_CTRL
  li   s3, OUT

poll_in:
  lw   t0, 0(s0)
  beqz t0, poll_in
  lw   t0, 0(s1)

poll_out:
  lw   t1, 0(s2)
  beqz t1, poll_out
  sw   t0, 0(s3)

  j poll_in
```

#### mmio_interrupt.s — Interrupt-driven I/O
```assembly
  .data
buffer:
  .space 32
  .text
  .eqv IN_CTRL  0xffff0000
  .eqv IN       0xffff0004
  .eqv OUT_CTRL 0xffff0008
  .eqv OUT      0xffff000C
  j main

handler:
  lw   t1, 0(s0)
  andi t1, t1, 0x1
  beqz t1, handler_out

  lw   t0, 0(s1)
  sw   t0, 0(s4)
  addi s4, s4, 4

handler_out:
  lw   t2, 0(s2)
  andi t2, t2, 0x1
  beqz t2, handler_ret

  la   t0, buffer
  beq  s4, t0, handler_ret

  addi s4, s4, -4
  lw   t0, 0(s4)
  sw   t0, 0(s3)

handler_ret:
  uret

main:
  la       t0, handler
  csrrw  zero, utvec, t0  # set utvec to the handler's address
  csrrsi zero, ustatus, 1 # set interrupt enable bit in ustatus
  li       t0, 257
  csrrw  zero, uie, t0    # enable handling of the specific interrupt

  li s0, IN_CTRL
  li s1, IN
  li s2, OUT_CTRL
  li s3, OUT
  la s4, buffer

  li t0, 2
  sw t0, 0(s0)  # set interrupt flag in the IN device
  sw t0, 0(s2)  # set interrupt flag in the OUT device
loop:
  wfi
  j loop
```

### Workshop Tasks (Complete)

*NOTE: The tasks from this workshop may take time. If one workshop is not enough, you may take some time from the next workshop.*

**Task 1.** Study and run the examples from the lecture.

**Task 2.** Input a floating-point number, round it to one digit to the right of decimal point (e.g. `3.5`) and display it in the `Digital Sim Lab` display. Use `digit_lab1.s` as a starting program.

**Task 3.** Modify the `digit_lab2.s` program to display keys pressed in the hexadecimal keyboard in the digital display (as decimal values, e.g. `F` == `15`). Use the workshop task as a basis.

**Task 4.** Write a program (based on `digit_lab2.s`) that inputs an integer value `X` (with the `ReadInt` system call) and then uses `Digital Sim Lab` to enter 32-bit integers (8 hexadecimal digits) `Y`. It repeats entering integers until the entered `Y` is equal to `X`. Then it prints `FOUND` and exits. If `Y` equals `0` the program must print `STOPPED` and exit.

**Task 5.** Write a program that draws a square with size 64×64 dots in the middle of `Bitmap Display` (see the `Tools` menu of RARS) with size 128×128 dots. The color and the thickness of lines in dots is specified by the user (use the `ReadInt` system call). See `bitmap_display.s` as an example.

### Homework

**1. FullRainbow** — Write a program that fills `RARS Bitmap Display` with vertical stripes of equal width (+1 pixel), which are painted with the specified colors. Colors are input as decimal numbers one per line until `0` is met. `0` means the end of the input and is not a color. The total number of colors is N <= 100.

- Parameters of Bitmap Display: unit size is 2×2; display size is 512×256.
- Formula for the starting position of the k-th stripe is `256 * k / N` (integer arithmetic).

Input: `1249394`, `7864115`, `5601041`, `43775`, `6706551`, `0`

**2. TheCircle** — Write a program that draws a circle in `RARS Bitmap Display`. To draw a circle means to paint all points whose distance from point `X`/`Y` is less than `R`. The program inputs 5 non-negative numbers: `X`, `Y`, `R`, color of the circle, and color of the background.

- Parameters of Bitmap Display: unit size is 2×2; display size is 512×256.
- It is not guaranteed that the circle fits into the display's address range or is located within this range at all.

Input: `100`, `60`, `50`, `15641122`, `5575048`

NOTE: Display 256×512 with unit size 2×2 means 32768 ((256 / 2) × (512 / 2)) pixels each having size of 4 bytes.

### References

- Input/Output. Chapter 8 in [COA]
- Memory-mapped I/O. Section 9.2 in [DDCA]
- [Memory-mapped I/O](https://en.wikipedia.org/wiki/Memory-mapped_I/O) (Wikipedia)
- [Direct memory access](https://en.wikipedia.org/wiki/Direct_memory_access) (Wikipedia)

---

## Summary of All Homework Assignments Referenced

| Lecture | Homework Tasks |
|---------|---------------|
| Lec 2 (Data Types) | Manual binary/hex conversion exercises (text document) |
| Lec 4 (Instructions) | RiscvEncoding1: Decompile hex machine code to assembly |
| Lec 5 (Macros) | DoubleSum, DigitSum, PlusMinus, EvenBack, NoDups |
| Lec 6 (Call Stack) | ASCIIGrid, CheckTriangles, FuncSort, KeySort, BinarySearch |
| Lec 7 (FP) | FractionTruncate, CubicRoot, LeibPi (bonus) |
| Lec 8 (Caches) | Matrix multiplication (multiply_matrices function) |
| Lec 9 (VM) | PseudoVM (exception-based virtual memory handler) |
| Lec 11 (Exceptions) | NoError, NewEcall (custom system calls via exception handler) |
| Lec 12 (MMIO) | FullRainbow, TheCircle (Bitmap Display programs) |
