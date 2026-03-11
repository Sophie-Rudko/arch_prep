# HSE ACOS Course - Part 2: Operating Systems
# Complete Lecture Content (Theory, Workshop, Code Examples, Homework)

---

# Lecture 1: Operating System Architecture. Linux

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/01_OS_Architecture/lecture.html

## Workshop

### Outline

- Installing VirtualBox
- Installing and using Linux Ubuntu in VirtualBox
- Setting up a GitHub repository
- Learning main Bash commands

### Practice: Linux Kernel Modules

#### Operating System Architecture Theory

An _operating system_ is software that controls hardware and provides an environment under which programs can run. The software responsible for this task is commonly called a _kernel_. In addition to the kernel, an operating system provides a set of _system programs_ that solve different tasks of managing the operating system.

The interface to the kernel is called _system calls_. System libraries and system software are built on top of this interface.

The kernel solves the following main tasks:
- Process management
- Memory management
- Filesystem management
- Device control
- Networking

Two opposite views on the structure of an operating system kernel:
- **Monolithic** - the kernel is a single library loaded in a single address space
- **Microkernel** - the kernel contains only a small set of essential components, while other components move to user-level programs (provides better modularity but works much slower)

**Linux** combines both approaches: the Linux kernel is loaded into a single address space, but it consists of _modules_ that can be dynamically loaded to the kernel address space.

#### Operating System Modules

In Linux, it is possible to extend at runtime the set of features provided by the kernel. A piece of code added to the kernel is called a _module_. Linux kernel supports several types (classes) of modules including device drivers. Each module is an object file that can be dynamically linked to the running kernel by the `insmod` tool and unlinked by the `rmmod` tool. Code of modules is executed in the kernel mode.

#### Simple "Hello World" Module

**hello.c:**
```c
#include <linux/init.h>
#include <linux/module.h>
 
MODULE_LICENSE("Dual BSD/GPL");
 
static int hello_init(void) {
    printk(KERN_ALERT "Hello, world\n");
    return 0;
}

static void hello_exit(void) {
    printk(KERN_ALERT "Goodbye, cruel world\n");
}
 
module_init(hello_init);
module_exit(hello_exit);
```

**Makefile:**
```
obj-m += hello.o
all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

**Build and run:**
```bash
acos@acos-vm:~/seminar2$ make
acos@acos-vm:~/seminar2$ sudo insmod ./hello.ko
acos@acos-vm:~/seminar2$ sudo rmmod hello
acos@acos-vm:~/seminar2$ sudo dmesg
[19179.657083] Hello, world
[19190.587959] Goodbye, cruel world
```

The `dmesg` command prints the kernel ring buffer containing messages printed by `printk`.

### Workshop Task 01: Jiffies Module

The Linux kernel keeps track of the number of timer interrupts since system boot in the `jiffies` variable declared in `<linux/jiffies.h>`. Write a module that prints the value of `jiffies` before installing and removing the module.

**simple_time.c (solution):**
```c
#include <linux/init.h>
#include <linux/jiffies.h>
#include <linux/module.h>

MODULE_LICENSE("Dual BSD/GPL");

unsigned long start;

static int simple_init(void) {
    start = jiffies;
    printk(KERN_ALERT "Module is installed\n");
    return 0;
}

static void simple_exit(void) {
    unsigned long end = jiffies;
    printk(KERN_ALERT "Module is removed\n");
    unsigned long delta = end - start;
    printk(KERN_ALERT "Jiffies start time: %lu\n", start);
    printk(KERN_ALERT "Jiffies end time: %lu\n", end);
    printk(KERN_ALERT "Jiffies elapsed time: %lu\n", delta);
}

module_init(simple_init);
module_exit(simple_exit);
```

### Workshop Task 02: /proc/jiffies Virtual File

Implement a kernel module that creates a virtual file `/proc/jiffies` that provides the current value of `jiffies` when read.

**jiffies.c (solution):**
```c
#include <linux/init.h>
#include <linux/jiffies.h>
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/proc_fs.h>
#include <asm/uaccess.h>

#define BUFFER_SIZE 128
#define PROC_NAME "jiffies"

ssize_t proc_read(
    struct file *file,
    char __user *usr_buf,
    size_t count,
    loff_t *pos
);

static struct file_operations proc_ops = {
    .owner = THIS_MODULE,
    .read = proc_read,
};

int proc_init(void) {
    proc_create(PROC_NAME, 0666, NULL, (const struct proc_ops *) &proc_ops);
    return 0;
}

void proc_exit(void) {
    remove_proc_entry(PROC_NAME, NULL);
}

ssize_t proc_read(
        struct file *file,
        char __user *usr_buf,
        size_t count,
        loff_t *pos) {
    int rv = 0;
    char buffer[BUFFER_SIZE];
    static int completed = 0;
    if (completed) {
        completed = 0;
        return 0;
    }
    completed = 1;
    rv = sprintf(buffer, "Jiffies time: %lu\n", jiffies);
    raw_copy_to_user(usr_buf, buffer, rv);
    return rv;
}

module_init(proc_init);
module_exit(proc_exit);
```

### Homework

Finish all the tasks unfinished in the class.

### References

- Operating System Structures. Chapter 2 in [OSC]
- Bash (Wikipedia)
- Linux man pages online
- Practical Guide to Linux Commands, Editors, and Shell Programming [PGLC]

---

# Lecture 2: C Programming Language

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/02_C/lecture.html

## Lecture Outline

- The C Language
- History
- Data types (built-in, structures)
- Functions
- Pointers, arrays, address arithmetic
- Memory allocation
- Strings

## Theory

### Built-in Data Types

```c
char
unsigned char
short
unsigned short
int 
unsigned int
long
unsigned long
float
double
void
```

### Pointer Data Types

```c
void *
char *
int *
long *
```

### Type Aliases

```c
typedef unsigned long int size_t;
```

### Structures

```c
struct point {
    int x;
    int y;
};
```

### Bitfields

```c
struct point {
    int x;
    int y1 : 16;
    int y2 : 16;
};
```

### Size of Various Data Types (Code Example)

```c
#include <stdio.h>

typedef struct {
    int x;
    int y;
} point_t;

int main() {
    printf("sizeof(char)   = %ld\n", sizeof(char));
    printf("sizeof(int)    = %ld\n", sizeof(int));
    printf("sizeof(long)   = %ld\n", sizeof(long));
    printf("sizeof(float)  = %ld\n", sizeof(float));
    printf("sizeof(double) = %ld\n", sizeof(double));
    printf("sizeof(void *) = %ld\n", sizeof(void *));
    printf("sizeof(point)  = %ld\n", sizeof(point_t));
    return 0;
}   
```

### Unions (Code Example)

```c
#include <stdint.h>
#include <stdio.h>

int main(void) {
    union S {
        uint32_t u32;
        uint16_t u16[2];
        uint8_t  u8[4];
    } s = {0x12345678};

    printf("sizeof(s)=%zu\n", sizeof(s));
    printf("address(s)=%p\n", &s);
    printf("address(s.u32)=%p\n", &s.u32);
    printf("address(s.u16)=%p\n", &s.u16);
    printf("address(s.u8)=%p\n\n", &s.u8);
    printf("s.u32=%x\n", s.u32);
    printf("s.u16[0]=%x, s.u16[1]=%x\n", s.u16[0], s.u16[1]);
    printf("s.u8[0]=%x, s.u8[1]=%x, s.u8[2]=%x, s.u8[3]=%x\n\n", s.u8[0], s.u8[1], s.u8[2], s.u8[3]);

    s.u16[0] = 0x0011;
    printf("s.u32=%x\n", s.u32);
    printf("s.u16[0]=%x, s.u16[1]=%x\n", s.u16[0], s.u16[1]);
    printf("s.u32=%x\n", s.u32);
    printf("s.u8[0]=%x, s.u8[1]=%x, s.u8[2]=%x, s.u8[3]=%x\n\n", s.u8[0], s.u8[1], s.u8[2], s.u8[3]);

    union pad {
        char  c[5];
        float f;
    } p = { .f = 1.23 };
    printf("sizeof(pad)=%zu\n", sizeof(p));
    printf("address(p)=%p\n", &p);

    return 0;
}
```

### Input/Output

```c
int x;
scanf("%d", &x);
printf("%d", x);
```

### Functions and Function Pointers (Code Example)

```c
#include <stdio.h>

void print(int x, int y) {
    printf("%d %d\n", x, y);
}

typedef void (* func_t)(int, int);

void test(func_t func) {
    (*func)(10, 20);
}

int main() {
    void (* func )(int, int) = &print;
    (*func)(10, 20);
    test(func);
    return 0;
}
```

### Dynamic Memory Allocation (Code Example)

```c
#include <stdio.h>

int main() {
    int i, n;
    printf("Enter array size:\n");
    scanf("%d", &n);

    int* array = malloc(sizeof(int) * n);
    printf("Enter array:\n");
    for (i = 0; i < n; i++) {
        scanf("%d", &array[i]);
    }
    printf("Array:\n");
    for (i = 0; i < n; i++) {
        printf("array[%d] = %d\n", i, array[i]);
    }
    free(array);

    return 0;
}
```

### Preprocessor (Code Example)

**lib.h:**
```c
#ifndef LIB
#define LIB

#ifdef NO_MACRO
  inline int max(int a, int b) { return a < b ? a : b; }
#else
  #define max(a, b) ((a) < (b) ? (a) : (b))
#endif

#endif
```

**prog.c:**
```c
#include "lib.h"

int main() {
   int x = max(0, -5);
   return x;
}
```

**Preprocessing:**
```bash
gcc prog.c -E            # NO_MACRO is not defined
gcc prog.c -E -DNO_MACRO # NO_MACRO is defined
```

## Workshop

### Outline

- Discuss main features of C and differences with other languages
- Discuss questions from discussion document
- Learn how to use GDB to debug simple programs
- Write and debug small programs covering different features of C language

### Using GDB Debugger

Compiling for debug: `cc -O0 -g program.c -o binaryprog`
- `-O0` - turn off optimization
- `-g` - include C source references in binary file
- `-o binaryprog` - set result binary file name

**Example program:**
```c
#include <stdio.h>
#define SIZE 10

int A[SIZE] = {7,6,5,4,3,2,1,0,1,2};
int B[SIZE];

void fun(int *a, int *b, int len) {
    int i;
    for(i=0; i<len; i++)
        b[i] = a[i];
}

int main(int argc, char *argv[]) {
    fun(A, B, 100500);
    return 0;
}
```

**GDB commands:**
- `b main` - set breakpoint at main
- `r` - run program until breakpoint
- `s` - step into function call
- `n` - next C code line
- `p variable` - print a variable
- `display variable` - display on every step
- `bt` - show backtrace
- `c` - continue
- `delete N` - delete breakpoint N
- `u` - execute until block or function ends
- `q` - quit

### Workshop Task

Write a program in C, which does the following:
- defines the point structure
- inputs an integer value `N`
- allocates an array of `N` points (assume the coordinates are non-negative)
- inputs `N` points
- inputs a point
- finds the point closest to the specified point in the array of points

Notes: use `malloc` and `free` to allocate and deallocate the array respectively.

## Homework

_Write and submit to GitHub the programs described below._

### Task 1
Write a program in C that inputs two integer values `x` and `y`, calls function `swap` that takes the values as arguments and swaps them, prints the values after the swap.

### Task 2
Write a program in C, which does the following:
- inputs an integer value `N`
- allocates an array of `N` integer elements
- fills the array with integer values from the standard input
- reverses the array
- prints the resulting array
- deallocates the array

Notes: use `malloc` and `free`.

### Task 3
Write a program in C, which does the following:
- inputs two integer values `N` and `M`
- allocates a matrix of size `N * M` and fills it with values from standard input
- transposes the matrix
- prints the resulting matrix
- deallocates the matrices

Note: the matrices must be allocated with `malloc` and deallocated with `free`.

### Task 4
Write a program in C, which does the following:
- creates a singly-linked list
- adds to the list numbers from the standard input until user inputs `0`
- reverses the list
- prints the resulting list
- deallocates the list

Note: use `malloc` and `free` to allocate and deallocate list entries respectively.

### References

- Brian Harvey. CS 61C: C: Introduction, Pointers, & Arrays
- [KRC] Brian W. Kernighan, Dennis Ritchie. C Programming Language. 2nd Edition. 1988.
- C programming language (Wikipedia)
- C data types (Wikipedia)
- C preprocessor (Wikipedia)
- Function Pointer in C
- Dynamic 2-D arrays in C
- C reference on cppreference.com

---

# Lecture 3: System Calls

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/03_SystemCalls/lecture.html

## Lecture Theory

### System Call Types

There are several types of system calls. Each type solves a specific kind of task:

- **Process control** - creating and terminating processes, loading and executing programs, getting and setting process attributes, allocating and freeing memory, waiting and signaling events
- **File management** - creating and deleting files, opening and closing files, reading/writing/repositioning, getting and setting file attributes
- **Device management** - requesting and releasing devices, reading/writing/repositioning, getting and setting device attributes, attaching and detaching devices
- **Information maintenance** - getting and setting date/time, getting and setting system data, getting and setting process/file/device attributes
- **Communications** - create and delete communication connection, send and receive messages, transfer status information, attach and detach remote devices
- **Protection** - getting file permissions, setting file permissions

## Workshop

### Outline

- General idea of system calls
- System calls `open`, `close`, `read`, and `write`
- System call `sbrk`
- System calls in C

### Theory

_System calls_ are operations (functions) provided by the operating system kernel, which are available to user applications. They are designed and documented by operating system kernel developers. System calls are typically executed with the help of so-called wrapper functions, which can be conveniently used in user-mode applications (e.g. the glibc library in Linux).

System calls allow executing kernel tasks upon user's requests. Modern operating systems isolate kernel memory from user applications. Therefore, when a user application needs to make a request to the kernel (open a file, create a process, send data to network, etc.), a switch between kernel and user modes is required. This makes system calls much slower than regular function calls.

System calls are made in architecture-dependent way, employing specific features of the instruction set architecture. A basic solution for system calls is employing the processor interrupt feature. However, a processor can also provide special instructions for this job. Arguments are passed via registers if available, extra arguments are passed via stack. The OS kernel saves and restores execution state when switching between modes.

### System calls in Linux API (C language)

Linux provides the following facilities to execute system calls:

1. **POSIX functions** - mapped directly to Linux system calls. E.g., `open()`, `read()`, `write()`, etc.
2. **glibc functions** - operating-system independent wrappers around system calls. E.g., `fopen()`, `scanf()`, `printf()`, etc.
3. **`syscall()`** - special glibc function to perform an indirect system call by number.

### API-functions that perform system calls

**The "open" function from POSIX:**
```c
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *path, int oflag, ...);
```

**The `fopen` function from glibc:**
```c
#include <stdio.h>

FILE *fopen(const char *pathname, const char *mode);
```

**The "syscall" function:**
```c
#include <unistd.h>
#include <sys/syscall.h>

long syscall(long number, ...);
```

### Code Examples

**Example 1: hello1.c - using the `printf` glibc function:**
```c
#include <stdio.h>
int main () {
    printf("Hello World\n");
    return 0;
}
```

**Example 2: hello2.c - using the `write` POSIX function:**
```c
#include <fcntl.h>
#include <unistd.h>
int main() {
    write(1, "Hello World\n", 12);
    return 0;
}
```

**Example 3: hello3.c - using the `syscall` function:**
```c
#include <unistd.h>
#include <sys/syscall.h>
int main() {
    syscall(1, 1, "Hello World\n", 12);
    return 0;
}
```

All three examples print "Hello World". Compile and run:
```bash
gcc hello1.c -o hello
./hello
```

### Workshop Tasks

**Task 1:** Read documentation on the `read` and `write` system calls. Note descriptor standard numbers for `stdin`, `stdout`, and `stderr`. Write a program that reads chars from `stdin`, increments them by 1, and writes them to `stdout`. To close `stdin` from the terminal, use the `^D` key combination.

**Task 2:** Read documentation on the `open` system call. Take notice of flags:
- `O_RDONLY` flag for opening a file for reading
- `O_WRONLY|O_CREAT|O_TRUNC` combination for opening a file for writing
- Mode parameter specifies file access rights: `S_IRUSR`, `S_IRGRP`, `S_IROTH`

Write a program that reads 100 words from `stdin` and writes them to a file named `outfile`. Do not forget to close the file.

**Task 3:** Modify the previous program to accept command-line arguments (`argc/argv`). Pass via command-line arguments the number of words (use `sscanf` to get an integer from `argv[1]`) and the name of output file (`argv[2]`).

## Homework

_TODO (not specified in original)_

### References

- System call (Wikipedia)
- System call open, close, read, write (Wikipedia)
- System call sbrk (Wikipedia)
- The GNU C Library (glibc)
- POSIX (Wikipedia)
- C POSIX library (Wikipedia)

---

# Lecture 4: Linking and Loading

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/04_Linking/lecture.html

## Lecture

### Outline

- Linking
- Relocatable object files
- Executable and Linkable Format (ELF)
- Shared libraries
- Linked libraries
- Library interpositioning
- Position-independent code
- Address space layout randomization (ASLR)

### Theory: Linking and Loading

Programs are stored on disk as binary executable files. To be run, a program must be loaded into memory and placed into the context of a process.

Source files are compiled by the _compiler_ into _object files_ designed to be loaded into any physical location. This format is called _relocatable object file_.

The _linker_ combines these files to produce a single binary _executable file_. During the linking stage, external library object files are included as well.

When a program is run, the executable file and all necessary libraries are loaded into memory with the help of the _loader_. There are two kinds of libraries:
- **Static** (`.a` extension) - Code is included into the executable file by the linker
- **Shared/Dynamic** (`.so` extension) - Loaded into the process by the loader at runtime. Shared among processes using shared memory.

Object and executable files have standard formats that cover compiled machine code and a symbol table containing metadata about functions and variables. Linux uses the **ELF** (Executable and Linkable Format). The most important information about an executable file is its _entry point_, the address of the first instruction to be executed.

### Tools for Manipulating Object Files

- **ar** - creates static libraries, inserts, deletes, lists, and extracts members
- **strings** - lists all printable strings in an object file
- **strip** - deletes symbol table information
- **nm** - lists symbols defined in the symbol table
- **size** - lists names and sizes of sections
- **readelf** - displays the complete structure of an object file (subsumes `size` and `nm`)
- **objdump** - can display all information in an object file; most useful for disassembling `.text` section
- **ldd** - lists shared libraries an executable needs at run time

### ELF Examples

**Relocatable object file (main.o):**
```
riscv64-unknown-linux-gnu-gcc -c main.c -O1 -march=rv64g
riscv64-unknown-linux-gnu-objdump -r -d main.o

main.o:     file format elf64-littleriscv
Disassembly of section .text:

0000000000000000 <main>:
   0:	ff010113   addi	sp,sp,-16
   4:	00113423   sd	ra,8(sp)
   8:	00200593   li	a1,2
   c:	00000537   lui	a0,0x0
			c: R_RISCV_HI20	array
  10:	00050513   mv	a0,a0
			10: R_RISCV_LO12_I	array
  14:	00000097   auipc	ra,0x0
			14: R_RISCV_CALL	sum
  18:	000080e7   jalr	ra
  1c:	00813083   ld	ra,8(sp)
  20:	01010113   addi	sp,sp,16
  24:	00008067   ret
```

**After linking (executable):**
```
riscv64-unknown-linux-gnu-gcc -o main main.o sum.o
riscv64-unknown-linux-gnu-objdump -x -d main

0000000000010430 <main>:
   10430:	ff010113   addi	sp,sp,-16
   10434:	00113423   sd	ra,8(sp)
   10438:	00200593   li	a1,2
   1043c:	83018513   addi	a0,gp,-2000 # 12030 <array>
   10440:	010000ef   jal	ra,10450 <sum>
   ...
```

### Library Interpositioning

Three types:
- Compile-time
- Link-time
- Load-time/run-time

## Workshop

### Outline

- Creating static libraries
- Creating shared libraries
- Library interpositioning
- Make scripts

### Part 1: Static and Shared Libraries

#### Creating a Static Library

**fred.c:**
```c
#include <stdio.h>

void fred(int arg)
{
    printf("fred: you passed %d\n", arg);
}
```

**bill.c:**
```c
#include <stdio.h>

void bill(char *arg)
{
    printf("bill: you passed %s\n", arg);
}
```

**lib.h:**
```c
void bill(char *);
void fred(int);
```

**program.c:**
```c
#include <stdlib.h>
#include "lib.h"

int main()
{
    bill("Hello World!");
    exit(0);
}
```

**Steps:**
```bash
# Compile to object files
gcc -c fred.c bill.c

# Create static library
ar crv libfoo.a bill.o fred.o

# Build program with static library
gcc -o program program.o -L. -lfoo
```

#### Creating a Shared Library

```bash
# Compile with position-independent code
gcc -c -Wall -fPIC fred.c bill.c

# Build shared library
gcc -shared -o libfoo.so fred.o bill.o

# Build program using shared library
gcc -Wall -o program program.c -lfoo -L.

# Run (set library path)
export LD_LIBRARY_PATH=.
./program

# View loaded libraries
ldd ./program
```

### Part 1 Task

Create two additional source files `john.c` and `sam.c`. Compile `fred` and `john` as a static library. Compile `bill` and `sam` as a shared library. Modify the main program to use functions from both libraries. `john` and `sam` must use the math library.

### Part 2: Make Files

GNU Make determines which pieces of a large program need to be recompiled. Build stages:
- Preprocessor: `.c` -> `gcc -E` -> `.c`
- Translator: `.c` -> `gcc -S` -> `.s`
- Assembler: `.s` -> `gcc -c` -> `.o`
- Linker: `.o` -> `gcc` or `ld` -> binary executable

**Simple Makefile:**
```make
hello:  hello.c
    gcc hello.c -o hello
clean:
    rm hello
```

**Makefile with special symbols and macros:**
```make
JUNK=*~ *.bak *.old
GENERATES=*.o prog
all:    prog

prog:   f1.o f2.o
    cc $^ -o $@

f1.o:   f1.c
        cc $< -c

f2.o:   f2.c
        cc $< -c

clean:
        rm -f $(JUNK) $(GENERATES)
```

Special symbols: `$@` (target), `$^` (all sources), `$<` (source that initiated recipe), `$?` (all new sources).

**Make Task 1:** Write a Make script that builds "Hello World" using macros and special symbols.

**Make Task 2:** Write a Make script going through all stages (preprocessor, translator, assembler, linker) with a clean target.

**Make Task 3:** Write a Make script for a program with `prog.c`, `lib.c`, and the standard math library.

**lib.c:**
```c
#include <stdio.h>

extern int var;

int func(int arg) {
   printf("### %d/%d ###\n", arg, var);
   return arg*2+1;
}
```

**proc.c:**
```c
#include <math.h>
#include <stdio.h>

int func(int);

int var = 33;

int main(int argc, char *argv[]) {
   int val = func(42);
   printf("sqrt(%d)=%lf\r\n", val, sqrt(val));
   return 0;
}
```

## Homework

Improve Task 1 from Part 1:
- Provide _link-time_ interpositioning for the `fred` and `john` functions
- Provide _load-time_ interpositioning for the `bill` and `sam` functions
- Create a Make script to build the program

Note: interposed functions can just print a message like "fred is called".

### References

- Linking. Chapter 7 in [CSPP]
- Section 2.5 in [OSC]
- Chapters 41 and 42 in [TLPI]
- Address space layout randomization (Wikipedia)

---

# Lecture 5: Dynamic Memory Allocation

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/05_Strings/lecture.html

## Lecture

### Outline

- Heap management and dynamic memory allocation
- `malloc` package
- Internal and external fragmentation
- Implicit and explicit lists, segregated lists, sorting blocks by size
- Headers and footers
- Placement policies
- Splitting and coalescing

### Example

Simple `malloc` implementation based on an implicit list and the first-fit policy. Try using it to allocate memory:

```cpp
#include <iostream>
#include <string>
#include <vector>

int main() {
  std::string s = "Hello World!";
  std::cout << s << std::endl;
  std::vector<int> v(20, 0);
  int *pp = new int[10];
  delete pp;
  std::string text = "This is some random very long text!";
  return 0;
}
```

```bash
make runcpp
gcc -Wall -g -shared -fpic -o libmalloc.so malloc.c
g++ test.cpp -o testcpp
LD_PRELOAD="./libmalloc.so" ./testcpp
malloc(73728)
malloc is initialized
start=0x56f5dd354000
end=  0x56f5dd355000
head= 0x56f5dd354008
malloc(73728) = 0x56f5dd354008
malloc(1024)
malloc(1024) = 0x56f5dd366010
Hello World!
malloc(80)
malloc(80) = 0x56f5dd366418
malloc(40)
malloc(40) = 0x56f5dd366470
free(0x56f5dd366470)
malloc(36)
malloc(36) = 0x56f5dd366470
free(0x56f5dd366470)
free(0x56f5dd366418)
```

### Memory Allocation Exercise Tasks

1. Change the implementation to support the **next-fit** strategy. How will this affect memory utilization?
2. Improve the `realloc` implementation:
   - If the new size is smaller, split the block (remaining part becomes empty block)
   - If the next block is empty and sufficiently large, extend the current block instead of freeing/allocating/copying
3. Improve memory utilization: footers are used for coalescing adjacent free blocks and required only for free blocks. For allocated blocks, they can be part of the payload. Use lower bits of the current block's header to know whether the previous block is allocated or free. Block size is always multiple of 8 (3 lower bits are 0). The 0th bit stores allocated/free status. Two remaining bits are vacant.
4. Improve performance: maintain an explicit double-linked list of free nodes. Skip allocated nodes when searching. Pointers to previous and next free node stored inside the payload of a free block. Minimal block size: 24 bytes (4 header + 8 prev pointer + 8 next pointer + 4 footer). Recommended: 32 bytes with alignment padding.

## Workshop: Strings, Patterns, and Regular Expressions

### Theory: Strings in C

**In C, there are no such data type as string.** Conventional LibC string is:
- Sequence of bytes
- Zero-terminated (ASCIIZ)
- No metadata

#### C Functions for Handling Strings

Most commonly used functions:
- `strlen` - calculate the length of a string
- `strcpy` - copy a string
- `strncpy` - copy `n` symbols of a string
- `strcat` - concatenate two strings
- `strdup` - duplicate a string
- `strcmp` - compare two strings
- `atoi` - convert a string to an integer

#### Working with Command-Line Arguments

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    int i;
    for (i = 0; i < argc; i++)
        printf("%s\n", argv[i]);
    return 0;
}
```

#### String Handling Principles (mystrlen example)

```c
#include <stdio.h>

int mystrlen(const char* str) {
   int len = 0;
   while (*str++) {
      len++;
   }
   return len;
}

int main(int argc, char *argv[]) {
    int i;
    for (i = 0; i < argc; i++) {
        char* arg = argv[i];
        printf("%s (%d)\n", arg, mystrlen(arg));
    }
    return 0;
}
```

#### String Comparison (mystrcmp example)

```c
#include <stdio.h>

int mystrcmp(const char *s1, const char *s2) {
    const unsigned char *p1 = ( const unsigned char * )s1;
    const unsigned char *p2 = ( const unsigned char * )s2;
    while (*p1 && *p1 == *p2 ) {
        ++p1;
        ++p2;
    }
    return (*p1 > *p2) - (*p2 > *p1);
}

int main(int argc, char *argv[]) {
    int i;
    for (i = 2; i < argc; i++) {
      char* arg = argv[i];
      printf("%s (compared to %s = %d)\n", arg, argv[1], mystrcmp(arg, argv[1]));
    }
    return 0;
}
```

#### Concatenation Example (catargs.c)

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    int i;
    int length = 0;
    for(i = 1; i < argc; i++)
        length += strlen(argv[i]);
    char *buf = malloc(length + 3);
    strcpy(buf, "<");
    for(i = 1; i < argc; i++)
        strcat(buf, argv[i]);
    strcat(buf, ">");
    printf("%s\n", buf);
    free(buf);
    return 0;
}
```

Output:
```bash
./catargs qwe ASD "1 2 3"
<qweASD1 2 3>
```

### Theory: Shell Patterns

Patterns are used in Linux Shell to filter files by their names:
```bash
$ ls a*       # all files starting with 'a'
$ ls a?b      # files: a<any char>b
$ ls [ab]*    # starting with 'a' or 'b'
$ ls *[a-z]   # ending with lowercase letter
$ ls *[^a-z]  # ending with non-lowercase
```

Pattern matching with `find`:
```bash
$ find . -name "OS_*.pdf"
```

Pattern matching in C with `fnmatch`:
```c
#include <stdio.h>
#include <fnmatch.h>

int main(int argc, char *argv[]) {
    const char* pattern = argv[1];
    const char* name = argv[2];
    if (fnmatch(pattern, name, FNM_PATHNAME) == 0) {
        printf("'%s' matches '%s'\n", name, pattern);
    } else {
        printf("'%s' mismatches '%s'\n", name, pattern);
    }
    return 0;
}
```

### Theory: Regular Expressions

Regular expressions describe patterns in the narrowest Chomsky hierarchy formal language class.

#### Regexp Examples

1. **Atomic regexp:**
   - Any non-special character matches exactly same character: `E` -> `E`
   - `.` matches any one character
   - `[quack!]` matches any character from the set
   - `[a-z]` matches any small letter
   - `[^quack!]` matches any character NOT from the set
   - `a*` matches zero or more `a`s (repeater)
   - `.*` matches any string

2. **Complex regexp:**
   - `boo` -> `boo`
   - `r....e` -> `riddle`
   - `[0-9][0-9]*` -> any non-negative integer
   - `[A-Za-z_][A-Za-z0-9]*` -> C identifier
   - Leftmost longest rule (greedy matching)

3. **Positioning marks:**
   - `^regexp` - matches at beginning of line
   - `regexp$` - matches at end of line

#### Regexp Tools

**grep** - filtering strings that contain regexp:
```bash
$ cal | grep 18
$ cal | grep '9.*4'
```

**sed** - search and replace:
```bash
$ cal | sed 's/[12][23]/@@/'     # replace once
$ cal | sed 's/[12][23]/@@/g'    # replace all (globally)
```

Group recall with `\(` and `\)`:
```bash
$ echo '15 16 17 18 19 20 21' | sed 's/\(15\)\(.*\)\(20\)/\3\2\1/'
20 16 17 18 19 15 21
```

#### Regexp in C

The C language provides library functions in `<regex.h>`:
- `regcomp` - compiles a regular expression
- `regexec` - performs a search
- `regfree` - frees the compiled regular expression

**Match example:**
```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <regex.h>

int match(const char *string, char *pattern) {
    int    status;
    regex_t    re;

    if (regcomp(&re, pattern, REG_EXTENDED|REG_NOSUB) != 0) {
        return 0;
    }
    status = regexec(&re, string, (size_t) 0, NULL, 0);
    regfree(&re);
    if (status != 0) {
        return 0;
    }
    return 1;
}

int main(int argc, char *argv[]) {
    char* regex = argv[1];
    char* name = argv[2];
    if (match(name, regex)) {
        printf("'%s' matches '%s'\n", name, regex);
    } else {
        printf("'%s' mismatches '%s'\n", name, regex);
    }
    return 0;
}
```

**Find all matches example:**
```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <regex.h>

#define ARRAY_SIZE(arr) (sizeof((arr)) / sizeof((arr)[0]))

static const char *const str =
       "1) John Driverhacker;\n2) John Doe;\n3) John Foo;\n";
static const char *const re = "John.*o";

int main(void) {
   static const char *s = str;
   regex_t     regex;
   regmatch_t  pmatch[1];
   regoff_t    off, len;

   if (regcomp(&regex, re, REG_NEWLINE))
       exit(EXIT_FAILURE);

   printf("String = \"%s\"\n", str);
   printf("Matches:\n");

   for (int i = 0; ; i++) {
       if (regexec(&regex, s, ARRAY_SIZE(pmatch), pmatch, 0))
           break;

       off = pmatch[0].rm_so + (s - str);
       len = pmatch[0].rm_eo - pmatch[0].rm_so;
       printf("#%d:\n", i);
       printf("offset = %jd; length = %jd\n", (intmax_t) off, (intmax_t) len);
       printf("substring = \"%.*s\"\n", len, s + pmatch[0].rm_so);

       s += pmatch[0].rm_eo;
   }
   exit(EXIT_SUCCESS);
}
```

## Homework

Write a program that inputs three command-line arguments:
- `argv[1]` - regular expression
- `argv[2]` - text
- `argv[3]` - replacement

The program finds all occurrences of the regular expression in the text and replaces all of them with the specified replacement string. The updated text is stored in a separate buffer and printed to the console.

_Hint_: Allocate a buffer for the new text. Find a regex match. Copy text before match to the buffer. Copy replacement to the buffer. Find the next match and so on.

_Note_: Use `realloc` if the buffer is not large enough.

### References

- Dynamic Memory Allocation. Section 9.9 in [CSPP]
- Interlude: Memory API. Chapter 14 in [COMET]
- Free-Space Management. Chapter 17 in [COMET]
- Memory Allocation. Chapter 7 in [TLPI]
- Donald Knuth. The Art of Computer Programming. Volume 1. Section 2.5.
- glibc's malloc, jemalloc, mimalloc

---

# Lecture 6: Processes and Threads

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/06_Processes/lecture.html

## Workshop Theory

### Processes

Modern operating systems run programs in an isolated context called a process. Each process has its own address space, where it stores the code of the executed program and data. A process is typically run by double-clicking a program or by executing `./program`. The OS switches between processes to give a user possibility to interact with several programs and give a feeling of concurrent execution.

```bash
acos@acos-vm:~$ ps -ef      # list running processes
acos@acos-vm:~$ ps
PID TTY TIME CMD
  2134 pts/0 00:00:00 bash
  2297 pts/0 00:00:00 ps
```

Also: `top` for dynamic view, `/proc` virtual file system for process info.

```bash
acos@acos-vm:~$ cat /proc/version
Linux version 5.4.0-52-generic ...
```

### Environment Variables

Each process maintains a list of environment variables loaded when the process starts.

```bash
tatarnikov@akos:~$ printenv
SHELL=/bin/bash
PWD=/home/tatarnikov/lab_proc
HOME=/home/tatarnikov
PATH=/usr/local/sbin:/usr/local/bin:...
```

**Accessing environment variables in C:**
```c
#include <stdio.h>
#include <stdlib.h>

extern char **environ;

int main(int argc, char *argv[]) {
    char **ep;
    for (ep = environ; *ep != NULL; ep++)
        puts(*ep);
    exit(EXIT_SUCCESS);
}
```

Functions for handling environment variables (`<stdlib.h>`):
- `getenv` - retrieve a specific environment variable
- `putenv` - change or add an environment variable
- `setenv` - change or add an environment variable
- `unsetenv` - remove an environment variable
- `clearenv` - clear entire environment

### Exit Handlers

```c
#include <stdlib.h>
#include <stdio.h>

static void atexitFunc1(void) {
    printf("atexit function 1 called\n");
}

static void atexitFunc2(void) {
    printf("atexit function 2 called\n");
}

static void onexitFunc(int exitStatus, void *arg) {
    printf("on_exit function called: status=%d, arg=%ld\n", exitStatus, (long) arg);
}

int main(int argc, char *argv[]) {
    if (on_exit(onexitFunc, (void *) 10) != 0)
        perror("on_exit 1");
    if (atexit(atexitFunc1) != 0)
        perror("atexit 1");
    if (atexit(atexitFunc2) != 0)
        perror("atexit 2");
    if (on_exit(onexitFunc, (void * ) 20) != 0)
        perror("on_exit 2");

    exit(2);
}
```

### Managing Processes

**fork** creates a new process by duplicating the calling process. The new process is the _child process_, the current is the _parent process_. They run in separate memory, but at the time of fork, both memory spaces have the same content.

**exec** runs a different program in the newly created process.

**wait** allows the parent to wait for the child to change state (e.g. terminate).

**Example: Running `ls` in a child process:**
```c
#include <sys/types.h>
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid;
    pid = fork();
    if (pid < 0) {
        perror("Fork Failed");
        return 1;
    } else if (pid == 0) {
        execlp("/bin/ls", "ls", NULL);
    } else {
        wait(NULL);
        printf("Child Complete");
    }
    return 0;
}
```

### Input and Output Redirection

Standard streams:

| Stream          | ID              | Descriptor |
|-----------------|-----------------|------------|
| Standard Input  | `STDIN_FILENO`  | `0`        |
| Standard Output | `STDOUT_FILENO` | `1`        |
| Standard Error  | `STDERR_FILENO` | `2`        |

**Using descriptors for output:**
```c
#include <unistd.h>

int main () {
    write(STDOUT_FILENO, "STD\n", 5);
    write(STDERR_FILENO, "ERR\n", 5);
    return 0;
}
```

**Redirect stdout to a file in C using `open`, `close`, and `dup2`:**
```c
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main(int argc, char *argv[]) {
    char* const command = argv[1];
    char* const out = argv[2];

    switch (fork()) {
       case -1: {
           return -1;
       }
       case 0: {
           int fd = open(out, O_CREAT|O_WRONLY|O_TRUNC, S_IRUSR|S_IWUSR);
           close(STDOUT_FILENO);
           dup2(fd, STDOUT_FILENO);
           execlp(command, command, NULL);
           break;
       }
       default: {
           wait(NULL);
           break;
       }
    }
    return 0;
}
```

### Connecting Processes with a Pipe

```bash
tatarnikov@akos:~$ ls | wc -l 
```

**Implementing `ls | wc -l` in C:**
```c
#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>
#include <stdlib.h>

void errExit(const char* err) {
    perror(err);
    exit(EXIT_FAILURE);
}

int main(int argc, char *argv[]) {
    int pfd[2];

    if (pipe(pfd) == -1)
        errExit("pipe");

    switch (fork()) {
        case -1:
            errExit("fork");
        case 0:
            if (close(pfd[0]) == -1)
                errExit("close 1");
            if (pfd[1] != STDOUT_FILENO) {
                if (dup2(pfd[1], STDOUT_FILENO) == -1)
                    errExit("dup2 1");
                if (close(pfd[1]) == -1)
                    errExit("close 2");
            }
            execlp("ls", "ls", (char *) NULL);
            errExit("execlp ls");
        default:
            break;
    }

    switch (fork()) {
        case -1:
            errExit("fork");
        case 0:
            if (close(pfd[1]) == -1)
                errExit("close 3");
            if (pfd[0] != STDIN_FILENO) {
                if (dup2(pfd[0], STDIN_FILENO) == -1)
                    errExit("dup2 2");
                if (close(pfd[0]) == -1)
                    errExit("close 4");
            }
            execlp("wc", "wc", "-l", (char *) NULL);
            errExit("execlp wc");
        default:
            break;
    }

    if (close(pfd[0]) == -1) errExit("close 5");
    if (close(pfd[1]) == -1) errExit("close 6");
    if (wait(NULL) == -1) errExit("wait 1");
    if (wait(NULL) == -1) errExit("wait 2");

    exit(EXIT_SUCCESS);
}
```

## Workshop Tasks

**Task 0: fork.c** - Create a child process and wait for it to complete. The child creates another child and waits. Each process prints an identifying message (e.g. "Hello from Parent").

**Task 1: outredir.c** - `outredir command output_filename`, which execs a command with stdout redirected to output_filename. E.g.: `./outredir ls out_of_ls`

**Task 2: allredir.c** - `allredir command infile outfile`, which forks and execs a command with both stdin and stdout redirection, then waits for termination and prints `WEXITSTATUS(wstatus)`. E.g.: `./allredir hexdump out_of_ls dump_file`

**Task 3: argredir.c** - `argredir infile outfile command arg1 arg2 arg3 ...`, rewrite previous program but use `execvp` for executing command with arguments. E.g.: `./argredir out_of_ls dump_file hexdump -C`

**Task 4: piperedir.c** - `piperedir command1 command2 arg1 arg2 arg3 ...`, which forks and execs `command1`, then execs `command2`, connecting them through an unnamed pipe. E.g.: `./piperedir date hexdump -C`

## Homework

1. Finish all tasks and send the code.
2. Improve `argredir.c` to check the error status of all operations and print an error message when errors occur:
   - See the "RETURN VALUE" section of each function manpage
   - Use `perror`
3. Write `pip2redir.c` that resembles `piperedir`, but forks both child processes, waits for both to end, and prints the exit status of both commands. Note `wait` returns after either child is stopped; check if `ECHILD` is returned to indicate no more living children.

### References

- Processes. Chapter 3 in [OSC]
- Processes. Section 8.2 in [CSPP]
- Processes. Chapter 6 in [TLPI]

---

# Lecture 7: I/O and Files

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/07_Synch/lecture.html

## Lecture

### Outline

- Files and folders in Linux
- Directory hierarchy in Linux
- Virtual file system and basics of Ext4
- System calls for working with files and folders
- Standard I/O, file descriptors and redirection
- Functions of C standard library (libc) for working with I/O

### Examples

**Getting information on file system usage:**
```bash
df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/nvme0n1p2 982862268 98898256 833963680  11% /
```

**Getting information on the disk:**
```bash
hwinfo --disk
```

**Getting information on a file and its file system:**
```bash
stat hello
  File: hello
  Size: 16048     	Blocks: 32         IO Block: 4096   regular file
Device: 259,2	Inode: 35260199    Links: 1
Access: (0775/-rwxrwxr-x)  Uid: ( 1001/ andrewt)   Gid: ( 1001/ andrewt)

stat hello --file-system
  File: "hello"
    ID: 2c46404543853cc9 Namelen: 255     Type: ext2/ext3
Block size: 4096       Fundamental block size: 4096
```

**Copying stdin to stdout, one byte at a time:**
```c
#include <unistd.h>

int main(void) {
    char c;
    while(read(STDIN_FILENO, &c, 1) != 0)
    write(STDOUT_FILENO, &c, 1);
    return 0;
}
```

**Accessing file metadata:**
```c
#include <sys/stat.h>
#include <stdio.h>

int main (int argc, char **argv) {
    struct stat st;
    char *type, *readok;
    stat(argv[1], &st);
    if (S_ISREG(st.st_mode))
        type = "regular";
    else if (S_ISDIR(st.st_mode))
        type = "directory";
    else
        type = "other";
    if ((st.st_mode & S_IRUSR))
        readok = "yes";
    else
        readok = "no";
    printf("type: %s, read: %s\n", type, readok);
    return 0;
}
```

**Reading a directory (ls-like program):**
```c
#include <sys/types.h>
#include <dirent.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    DIR *directory;
    struct dirent *de;
    const char *dir_name = argv[1];
    if (!(directory = opendir(dir_name))) {
        perror("Failed to open directory");
        return 1;
    }
    while ((de = readdir(directory))) {
        printf("Found file: %s\n", de->d_name);
    }
    closedir(directory);
    return 0;
}
```

**Buffering provided by the standard C library:**
```c
#include <stdio.h>

int main() {
    printf("h");
    printf("e");
    printf("l");
    printf("l");
    printf("o");
    printf("\n");
    fflush(stdout);
    return 0;
}
```

`strace` utility shows that many `printf` calls result in a single `write` system call:
```
strace -e trace=write  ./hello 
write(1, "hello\n", 6hello
)                  = 6
```

## Workshop: Shell Scripts

Shell allows creating simple scripts that run various Linux system utilities and connect them. A Shell script is an executable text file run with: `interpreter script_file` (e.g. `sh script`).

Special marker "shebang" (`#!/bin/sh`) specifies the interpreter. Give execute permission with `chmod +x script`.

### Input and Output

```bash
#!/bin/sh
read A
echo "$A"
```

```bash
#!/bin/sh
read A
Sys=`uname`
echo "Hello, $A!
Welcome to $Sys!"
```

**Task 01:** Modify to get the user name from system using `whoami` instead of reading input.

**Task 02:** Modify to store the message in a macro variable `Msg`.

### Command-Line Arguments

```bash
#!/bin/sh
# $1, $2 ... - command line argument 1, 2 ...
# $0 - name of the script itself
# $* - all command line arguments
# $# - number of command line arguments

echo "$0"
echo "$1"
echo "$*"
echo "$#"
```

### Conditions and Exit Status

```bash
#!/bin/sh
if ls $*; then
    echo YES
else
    echo NO $?
fi
```

**Task 03:** Rewrite to output `ls` content after "YES" if successful, don't output error message if not successful.

### Advanced Shell Features

**For loop:**
```bash
#!/bin/sh
for n in 1 2 3 10 20 30 qwe asd xcv; do
    echo "Next value $n"
done
```

**Functions:**
```bash
#!/bin/sh
fun() {
    echo "$0: $# $*"
    for arg; do
        echo "> $arg"
    done
}
fun 1 QWE "3 4"
fun "$*"
fun  $*
fun "$@"
```

**Task 04:** Create a program with a `sum` function. Note how `read var1 var2` works. Use `expr` to print sum of `$a` and `$b`.

```bash
#!/bin/sh
sum() {
    # insert your code here
}
while read a b; do
    sum $a $b
done
```

## Shell Scripts Homework

1. Experiments with `read` builtin:
   - Research exit status of `read`. Use `help read`.
   - Research how to suppress `\n` after `echo`.
   - Write a script that asks user for name, prints welcome message whether name was entered or not:
   ```
   $ ./c8
   Enter your name: Spot
   Hello, Spot!
   $ ./c8
   Enter your name:
   Hello, tmpuser?
   ```

2. Write Shell script `sumsum.sh` with function `sum()` that:
   - sums all of its arguments (any number)
   - returns the sum or `0` if there was an error
   - redirects all error messages to `/dev/null`
   - reads two lines of numbers
   - prints whether their sums are equal or not

   ```
   $ ./sumsum.sh
   1 3 5
   2 4 6
   Not equal
   $ ./sumsum.sh
   1 5 6
   4 4 4
   Equal
   ```

### References

- df, stat utilities
- File Systems. Chapter 14 in [TLPI]
- System-Level I/O. Chapter 10 in [CSPP]
- Filesystem Hierarchy Standard (Wikipedia)
- Overview of the Linux Virtual File System
- Inode (Wikipedia)
- ext4 Data Structures and Algorithms

---

# Lecture 8: Threads and Synchronization

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/08_IPC/lecture.html

## Workshop

### Outline

- Threads
- Spinlocks
- Mutexes and locks
- Conditional variables
- Thread local variables
- Atomic variables

### Threads in C

#### Compiling

```bash
gcc thread1.c -o thread -lpthread
```

#### Simple Multithreaded Program

Functions: `pthread_create`, `pthread_join`, `pthread_exit`, `pthread_detach`

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

static void * threadFunc(void *arg) {
    char *s = (char *) arg;
    printf("%s", s);
    return (void *) strlen(s);
}

int main(int argc, char *argv[]) {
    pthread_t t1;
    void *res;
    int s;
    s = pthread_create(&t1, NULL, threadFunc, "Hello world\n");
    if (s != 0) {
        perror("pthread_create");
        return -1;
    }
    printf("Message from main()\n");
    s = pthread_join(t1, &res);
    if (s != 0) {
        perror("pthread_join");
        return -1;
    }
    printf("Thread returned %ld\n", (long) res);
    exit(EXIT_SUCCESS);
}
```

#### Multithreaded Program WITHOUT Synchronization (Race Condition)

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

static int glob = 0;

static void * threadFunc(void *arg) {
    int loops = *((int *) arg);
    int loc, j;
    for (j = 0; j < loops; j++) {
        loc = glob;
        loc++;
        glob = loc;
    }
    return NULL;
}

int main(int argc, char *argv[]) {
    pthread_t t1, t2;
    int loops, s;
    loops = (argc > 1) ? atoi(argv[1]) : 10000000;
    s = pthread_create(&t1, NULL, threadFunc, &loops);
    if (s != 0) { perror("pthread_create"); return -1; }
    s = pthread_create(&t2, NULL, threadFunc, &loops);
    if (s != 0) { perror("pthread_create"); return -1; }
    s = pthread_join(t1, NULL);
    if (s != 0) { perror("pthread_join"); return -1; }
    s = pthread_join(t2, NULL);
    if (s != 0) { perror("pthread_join"); return -1; }
    printf("glob = %d\n", glob);
    exit(EXIT_SUCCESS);
}
```

#### Multithreaded Program WITH Synchronization (Mutexes)

Functions: `pthread_mutex_lock`, `pthread_mutex_unlock`

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

static int glob = 0;
static pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;

static void * threadFunc(void *arg) {
    int loops = *((int *) arg);
    int loc, j, s;
    for (j = 0; j < loops; j++) {
        s = pthread_mutex_lock(&mtx);
        if (s != 0) { perror("pthread_mutex_lock"); return NULL; }
        loc = glob;
        loc++;
        glob = loc;
        s = pthread_mutex_unlock(&mtx);
        if (s != 0) { perror("pthread_mutex_unlock"); return NULL; }
    }
    return NULL;
}

int main(int argc, char *argv[]) {
    pthread_t t1, t2;
    int loops, s;
    loops = (argc > 1) ? atoi(argv[1]) : 10000000;
    s = pthread_create(&t1, NULL, threadFunc, &loops);
    if (s != 0) { perror("pthread_create"); return -1; }
    s = pthread_create(&t2, NULL, threadFunc, &loops);
    if (s != 0) { perror("pthread_create"); return -1; }
    s = pthread_join(t1, NULL);
    if (s != 0) { perror("pthread_join"); return -1; }
    s = pthread_join(t2, NULL);
    if (s != 0) { perror("pthread_join"); return -1; }
    printf("glob = %d\n", glob);
    exit(EXIT_SUCCESS);
}
```

#### Conditional Variables (Producer-Consumer in C)

Functions: `pthread_cond_signal`, `pthread_cond_broadcast`, `pthread_cond_wait`

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

static pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
static pthread_cond_t cv_full = PTHREAD_COND_INITIALIZER;
static pthread_cond_t cv_empty = PTHREAD_COND_INITIALIZER;

static int data;
static int data_ready;

typedef struct {
   int count;
   int* items;
} array;

static void * producerFunc(void *arg) {
    array* parr = (array *) arg;
    int count = (*parr).count;
    int *items = (*parr).items;
    for (int i = 0; i < count; ++i) {
        pthread_mutex_lock(&m);
        while (data_ready) {
            pthread_cond_wait(&cv_empty, &m);
        }
        data = items[i];
        printf("produced %d\n", data);
        data_ready = 1;
        pthread_mutex_unlock(&m);
        pthread_cond_signal(&cv_full);
    }
}

static void * consumerFunc(void *arg) {
    int items = (int) arg;
    for (int i = 0; i < items; ++i) {
        pthread_mutex_lock(&m);
        while (!data_ready) {
            pthread_cond_wait(&cv_full, &m);
        }
        printf("consumed %d\n", data);
        data_ready = 0;
        pthread_mutex_unlock(&m);
        pthread_cond_signal(&cv_empty);
   }
}

int main() {
    int items[] = {1, 1, 2, 3, 5, 8, 13, 21, 34, 55};
    pthread_t producer, consumer;
    array arr;
    int s;
    arr.count = sizeof(items) / sizeof(int);
    arr.items = items;
    s = pthread_create(&producer, NULL, producerFunc, &arr);
    if (s != 0) { perror("pthread_create"); return -1; }
    s = pthread_create(&consumer, NULL, consumerFunc, (void *) arr.count);
    if (s != 0) { perror("pthread_create"); return -1; }
    s = pthread_join(producer, NULL);
    if (s != 0) { perror("pthread_join"); return -1; }
    s = pthread_join(consumer, NULL);
    if (s != 0) { perror("pthread_join"); return -1; }
    exit(EXIT_SUCCESS);
}
```

### Threads in C++

#### std::thread

```cpp
#include <iostream>
#include <thread>

void f1() {
    std::cout << "Hello from f1" << std::endl;
}

void f2(int a, int b) {
    std::cout << "f2 invoked with " << a << ", " << b << std::endl;
}

int main() {
    std::thread t1(f1);
    t1.join();

    std::thread t2(f2, 1, 2);
    t2.join();

    int i = 7;
    std::thread t4([&]() {
        std::cout << "lambda invoked with captured i == " << i << std::endl;
    });
    t4.join();

    auto f = [&](int k) {
        f1();
        f2(i, i * k);
    };
    std::thread t6(f, 99);
    t6.join();
}
```

Compile: `g++ -Wall -g -std=c++0x -pthread threads.cpp -o threads`

#### C++ Mutexes, Locks, and Conditional Variables (Producer-Consumer)

```cpp
#include <condition_variable>
#include <iostream>
#include <mutex>
#include <string>
#include <thread>

std::mutex m;
std::condition_variable cv_full, cv_empty;
int data;
bool data_ready;

void consumer_thread(int items) {
    for (int i = 0; i < items; ++i) {
        std::unique_lock<std::mutex> g(m);
        cv_full.wait(g, []() { return data_ready; });
        std::cout << "consumed " << data << std::endl;
        data_ready = false;
        cv_empty.notify_one();
    }
}

void producer_thread(int count, int *items) {
    for (int i = 0; i < count; ++i) {
        std::unique_lock<std::mutex> g(m);
        cv_empty.wait(g, []() { return !data_ready; });
        data = items[i];
        std::cout << "produced " << data << std::endl;
        data_ready = true;
        cv_full.notify_one();
    }
}

int main() {
    int items[] = {1, 1, 2, 3, 5, 8, 13, 21, 34, 55};
    std::thread producer(producer_thread, 10, items);
    std::thread consumer(consumer_thread, 10);
    producer.join();
    consumer.join();
}
```

#### C++ Atomic and Thread Local Variables

```cpp
#include <iostream>
#include <atomic>
#include <mutex>
#include <thread>
#include <vector>

thread_local int local_counter = 0;
std::atomic<int> global_counter{0};
std::mutex m;

void increment(int howmany) {
    for (int i = 0; i < howmany; ++i) {
        local_counter++;
        global_counter++;
    }
    std::lock_guard<std::mutex> g(m);
    std::cout << "Thread exiting with local = " << local_counter
              << " and global = " << global_counter << std::endl;
}

void run_threads(int thread_count, int increments_per_thread) {
    std::vector<std::thread> threads;
    for (int i = 0; i < thread_count; ++i)
        threads.push_back(std::thread(increment, increments_per_thread));
    for (int i = 0; i < thread_count; ++i)
        threads[i].join();
}

int main() {
    run_threads(5, 10);
    return 0;
}
```

## Workshop Tasks

Please use the C language and the POSIX thread library.

**Task 1:** Write a multithreaded program that calculates statistical values for a list of numbers on the command line. Create three separate worker threads: one for average, one for maximum, one for minimum. Store results globally. Parent thread outputs values after workers exit.

Example input: `90 81 78 95 79 72 85`

Output:
```
The average value is 82
The minimum value is 72
The maximum value is 95
```

**Task 2:** Write a multithreaded program that generates the Fibonacci sequence. User enters the count on the command line. A separate thread generates the sequence into shared data (array). Parent thread outputs the sequence after child thread finishes.

**Task 3:** Modify the matrix multiplication program to use a separate thread for calculating each row of the target matrix.

## Homework

Write a program that:
1. Inputs integer value `N`
2. Allocates an array of `N` random integer values
3. Splits the array into 4 approximately equal parts
4. Creates 4 worker threads, each:
   - Calculates the sum of elements in one part
   - Adds the result to global variable `sum` (protected with a mutex)
5. The main thread:
   - Prints the array content
   - Joins the 4 working threads
   - Prints the resulting `sum` when all 4 threads finish

### References

- Synchronization (Wikipedia)
- Critical section (Wikipedia)
- Spinlock (Wikipedia)
- Synchronization Tools. Chapter 6 in [OSC]
- Threads: Introduction. Chapter 29 in [TLPI]
- Threads: Thread synchronization. Chapter 30 in [TLPI]
- Thread support in C++ (cppreference)

---

# Lecture 9: Inter-Process Communication

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/09_IPC/lecture.html

## Outline

- Signals
- Message Queues
- Memory Mapping
- Shared Memory

## Examples

### Signals

**endless.c - Never-ending program (target for signals):**
```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
   int i;
   for (i = 0;; i++) {
       sleep(1);
       printf("%d\n", i);
   }
   return 0;
}
```

**killn.c - Sending signals in C:**
```c
#include <stdio.h>
#include <sys/types.h>
#include <signal.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    if (kill(atoi(argv[1]), atoi(argv[2]))) {
        perror("Failed to kill");
    }
    return 0;
}
```

**catch.c - Handling signals:**
```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
 
void handler(int sig) {
    printf("Caught %d\n", sig);
}

int main(int argc, char *argv[]) {
    signal(SIGINT,  handler);
    signal(SIGSEGV, handler);
    
    int i;
    for (i = 0;; i++) {
       sleep(1);
       printf("%d\n", i);
    }
    return 0;
}
```

**waitchild.c - Monitoring child processes:**
```c
#include <stdio.h>
#include <wait.h>
#include <signal.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    int stat;
    pid_t pid;
    if ((pid = fork()) == 0) {
        while(1);
    } else {
        printf("Forking a child: %d\n", pid);
        wait(&stat);
        printf("And finally...\n");

        if (WIFSIGNALED(stat)) {
            psignal(WTERMSIG(stat), "Terminated:");
        }
        printf("Exit status: %d\n", stat);
    }
    return 0;
}
```

### Message Queues

POSIX message queues advantages over signals: synchronous, can store content, can be queued, can be prioritized.

**crt_mq.c - Creating a message queue:**
```c
#include <mqueue.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    mqd_t mqd;
    struct mq_attr attr;
    attr.mq_maxmsg = 10;
    attr.mq_msgsize = 2048;

    mqd = mq_open(argv[1], O_RDWR|O_CREAT|O_EXCL, S_IRUSR|S_IWUSR, &attr);
    return 0;
}
```

**snd_mq.c - Sending a message:**
```c
#include <mqueue.h>
#include <fcntl.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    mqd_t mqd;
    unsigned int prio;
    mqd = mq_open(argv[1], O_WRONLY);
    prio = atoi(argv[2]);
    mq_send(mqd, argv[3], strlen(argv[3]), prio);
    return 0;
}
```

**rec_mq.c - Receiving a message:**
```c
#include <mqueue.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
   mqd_t mqd;
   unsigned int prio;
   void *buf;
   struct mq_attr attr;
   ssize_t n;
   mqd = mq_open(argv[1], O_RDONLY);
   mq_getattr(mqd, &attr);
   buf = malloc(attr.mq_msgsize);
   n = mq_receive(mqd, buf, attr.mq_msgsize, &prio);
   printf("Read %ld bytes; priority = %u\n", (long) n, prio);
   free(buf);
   return 0;
}
```

**unl_mq.c - Unlinking a message queue:**
```c
#include <mqueue.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (mq_unlink(argv[1])) {
        perror("mq_unlink");
        return -1;
    }
    return 0;
}
```

**Compile and run:**
```bash
gcc crt_mq.c -lrt -o crt_mq 
gcc snd_mq.c -lrt -o snd_mq
gcc rec_mq.c -lrt -o rec_mq
gcc unl_mq.c -lrt -o unl_mq

./crt_mq /queue
./snd_mq /queue 5 Five
./snd_mq /queue 10 Ten
./snd_mq /queue 5 Five_2
./snd_mq /queue 3 Three
./snd_mq /queue 7 Seven
for n in `seq 5`; do ./rec_mq /queue; done
Read 3 bytes; priority = 10
Read 5 bytes; priority = 7
Read 4 bytes; priority = 5
Read 6 bytes; priority = 5
Read 5 bytes; priority = 3
./unl_mq /queue
```

### Memory Mapping

The `mmap` syscall maps a file to virtual memory address range. After mapping, the range can be used as an ordinary array filled with file contents. Linux uses paging mechanism to represent file parts.

**mmcat.c - Simple `cat` using mmap:**
```c
#include <sys/mman.h>
#include <sys/stat.h>
#include <stdio.h>
#include <fcntl.h>

int main(int argc, char *argv[]) {
    char *addr;
    int fd;
    struct stat sb;

    fd = open(argv[1], O_RDONLY);
    fstat(fd, &sb);
    addr = mmap(NULL, sb.st_size, PROT_READ, MAP_PRIVATE, fd, 0);
    fwrite(addr, 1, sb.st_size, stdout);
    return 0;
}
```

### Shared Memory

**crt_shm.c - Creating shared memory:**
```c
#include <stdio.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/mman.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    int fd;
    size_t size;
    void *addr;

    fd = shm_open(argv[1], O_RDWR|O_CREAT|O_EXCL, S_IRUSR|S_IWUSR);
    size = atol(argv[2]);
    ftruncate(fd, size);

    addr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    close(fd);

    return 0;
}
```

**wrt_shm.c - Writing to shared memory:**
```c
#include <stdio.h>
#include <fcntl.h>
#include <sys/mman.h>
#include <string.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    int fd;
    size_t len;
    char *addr;

    fd = shm_open(argv[1], O_RDWR, 0);
    len = strlen(argv[2]);
    ftruncate(fd, len);

    addr = mmap(NULL, len, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    close(fd);

    printf("Copying %ld bytes\n", len);
    memcpy(addr, argv[2], len);
    return 0;
}
```

**rd_shm.c - Reading from shared memory:**
```c
#include <stdio.h>
#include <fcntl.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    int fd;
    char *addr;
    struct stat sb;

    fd = shm_open(argv[1], O_RDONLY, 0);
    fstat(fd, &sb);
    addr = mmap(NULL, sb.st_size, PROT_READ, MAP_SHARED, fd, 0);
    close(fd);

    fwrite(addr, 1, sb.st_size, stdout);
    printf("\n... Done");
    return 0;
}
```

**unl_shm.c - Unlinking shared memory:**
```c
#include <sys/mman.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (shm_unlink(argv[1])) {
        perror("shm_unlink");
        return -1;
    }
    return 0;
}
```

**Compile and run shared memory examples:**
```bash
gcc crt_shm.c -lrt -o crt_shm
gcc wrt_shm.c -lrt -o wrt_shm
gcc rd_shm.c -lrt -o rd_shm
gcc unl_shm.c -lrt -o unl_shm

./crt_shm /shmem 0
./wrt_shm /shmem 'Hello!'
Copying 6 bytes
./rd_shm /shmem
Hello!
... Done
./unl_shm /shmem
```

## Workshop

### Outline

- Practice with signals
- Study, run, and modify the lecture examples

### Important Signals

| Signal | Number | Description |
|--------|--------|-------------|
| SIGHUP | 1 | Terminal is closed |
| SIGINT | 2 | Interrupt process from terminal (`Ctrl-C`) |
| SIGQUIT | 3 | Terminate process and dump core (`Ctrl-\`) |
| SIGKILL | 9 | Immediately terminate (cannot be handled) |
| SIGTERM | 15 | Request termination (can be handled, sent by default) |
| SIGSTOP | 19 | Stop a process (cannot be handled) |
| SIGTSTP | 20 | Request stop (`Ctrl-Z`, can be handled) |
| SIGCONT | 18 | Continue process previously paused |
| SIGCHLD | 17 | Child process exits, is interrupted, or resumes |

### Managing Processes

Utilities: `ps`, `ps -a`, `ps -ef`, `pstree`, `ps axu`, `pidof program`, `ls /proc`, `kill proc`, `kill -STOP proc`, `kill -HUP proc`

### Foreground and Background Processes

- **Foreground** - Interactive process, can input and output, only one per terminal. Run: `./endless`
- **Background** - Can only output, any number. Run: `./endless &`
- **Changing type:** `Ctrl-Z` to stop, `fg` to continue in foreground, `bg` to continue in background

### Workshop Tasks

**Task 1:** Make the `09_IPC` directory. Code must reside there.

**Task 2:** Compile `endless.c`. Run in background, stop it, resume in foreground, send `SIGINT` to terminate.

**Task 3:** Create `proc.c` (modify `endless.c`) that:
- Waits forever
- Periodically prints its PID via `getpid` and an increased counter
- Uses a command-line argument for timeout between prints

Example: `./proc 5` prints once every 5 seconds:
```
26475: 0
26475: 1
26475: 2
...
```

**Task 4:** Write `killn.c` to send a signal:
- Use `kill -l` to create signal names array
- `./killn PID NAME` sends signal `NAME` to process `PID`
- Use `perror` if error occurred
- Print "No such signal" if `NAME` is not found, return 1

**Task 5:** Copy `proc.c` to `catchsig.c` and modify:
- `./catchsig 5 SIGNAL_NAME1 SIGNAL_NAME2 ...` prints signal description via `strsignal` when catching a signal
- Note not all signals can be handled

Example:
```
$ ./catchsig 5 INT ABRT SEGV
26775: 0
^C[Caught: Interrupt]26775: 1
26775: 2
[Caught: Segmentation fault]26775: 3
...
```

**Task 6:** Join `catchsig.c` with child-control program, name result `childctl.c`:
- `./childctl timeout signalQ signal1 ... signaln`
- Print a message once in timeout seconds
- Catch signal1...signaln and print message
- Peacefully exit when got signalQ

## Homework

1. Finish all programs from the workshop.
2. Modify the last program to:
   - Exit after getting `signalQ` three times
   - Check every syscall return values on error state

## Bonus Tasks: Message Queue

**Task 1:** Write `sendmq.c` that sends a message with priority 1 through the specified queue.

**Task 2:** Write `mqserver.c` (messaging server) that:
- Creates a queue
- Waits for messages and receives them
- If message is NOT "QUIT", print it and continue
- If message IS "QUIT", stop receiving and unlink the queue

**Task 3:** Modify server to catch `SIGINT` as `mqsignal.c`:
- When pressing `Ctrl+C`, stop reading, unlink queue, and stop
- Check every function for errors

### References

- Interprocess communication overview. Chapter 49 in [TLPI]
- Signals: fundamental concepts. Chapter 20 in [TLPI]
- POSIX message queues. Chapter 50 in [TLPI]
- Memory mappings. Chapter 49 in [TLPI]
- POSIX shared memory. Chapter 54 in [TLPI]

---

# Lecture 13: Sockets

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/13_Sockets/lecture.html

## Lecture Theory

### Socket

An abstraction for asynchronous data transfer:
- Has two ends
- Can be bidirectional
- Can be organized over various underlying layers (network TCP/UDP for IPv4 _internet_ socket, special filesystem object _unix domain_ socket, etc.)

### Socket Disciplines

**Stream:** An ordered series of packets of reliable data:
- No transfer without established connection
- Data transferred is reliably equal to data received (including corruption/loss/duplication correction)
- Out-of-band states are eliminated (sender cannot send more than receiver can receive)

**Datagram:** A single message:
- No need to establish a connection
- When sending over a network, no need to order and count packets

### Socket Programming

#### To Initialize a Socket

Create a `socket(domain, type, 0)`:
- `domain` is underlying layer type (address family)
- `type` is discipline (stream, datagram, etc.)

#### To Run a Server

1. `bind(socket, address, length)` - Associate socket with specific address
2. `listen(socket, queue_length)` - Start listening
3. `accept(socket, address, &length)` - Get new connection data socket descriptor
4. `recv(data_socket, buffer, length, 0)` - Receive data
5. `close()` - Close data and control sockets

#### To Run a Client

1. `connect(socket, address, length)` - Connect to remote server
2. `send(socket, buffer, length, 0)` - Send data
3. `close()` - Close socket

## Examples

### Unix Domain + Datagram

**unix_d_send.c - Unix domain datagram sender:**
```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <sys/un.h>

#define USIZE sizeof(struct sockaddr_un)

int main(int argc, char *argv[]) {
    struct sockaddr_un srv;
    int fd;

    fd = socket(AF_UNIX, SOCK_DGRAM, 0);
    srv.sun_family = AF_UNIX;
    strncpy(srv.sun_path, argv[1], sizeof(srv.sun_path)-1);

    bind(fd, (const struct sockaddr *) &srv, USIZE);
    sendto(fd, argv[2], strlen(argv[2]), 0, (const struct sockaddr *) &srv, USIZE);

    return 0;
}
```

**unix_d_server.c - Unix domain datagram server:**
```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <sys/un.h>
 
#define USIZE sizeof(struct sockaddr_un)
#define BLOG 3
#define DSIZE 16

int main(int argc, char *argv[]) {
    struct sockaddr_un srv;
    char dgram[DSIZE];
    int fd, rsz, i;

    fd = socket(AF_UNIX, SOCK_DGRAM, 0);
    srv.sun_family = AF_UNIX;
    strncpy(srv.sun_path, argv[1], sizeof(srv.sun_path)-1);

    remove(argv[1]);
    bind(fd, (const struct sockaddr *) &srv, USIZE);
    listen(fd, BLOG);

    rsz = recv(fd, dgram, DSIZE, 0);
    for(i=0; i<rsz; i++)
        printf("%02x ", dgram[i]);
    putchar('\n');

    remove(argv[1]);
    return 0;
}
```

**Usage:**
```bash
./unix_d_server u_socket &
./unix_d_send u_socket Message
4d 65 73 73 61 67 65 
```

### Internet (IPv4) + Stream (TCP)

**tcp_qq_srver.c - Simple TCP server:**
```c
#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define ISIZE (sizeof(struct sockaddr_in))
#define MAXCONN 3
#define BUFSIZE 32

int main(int argc, char *argv[]) {
    int fd, connfd, conncount=0;
    struct sockaddr_in srv;
    char buf[32];

    memset(&srv, 0, ISIZE);
    srv.sin_family = AF_INET;
    inet_pton(AF_INET, argv[1], &(srv.sin_addr));
    srv.sin_port = htons(atoi(argv[2]));

    fd = socket(AF_INET, SOCK_STREAM, 0);
    bind(fd, (struct sockaddr*) &srv, ISIZE);
    listen(fd, MAXCONN);

    while(1) {
        connfd = accept(fd, NULL, NULL);
        snprintf(buf, BUFSIZE, "Connection %d!\n", ++conncount);
        write(connfd, buf, strlen(buf));
        close(connfd);
    }
    return 0;
}
```

**Byte ordering notes:**
- Various architectures can have various byte ordering
- Network protocols use Big-endian
- `htons` - convert short from host to network endianness
- `ntohs` - reverse
- `inet_pton` - convert string IP address to binary in network order
- `inet_ntop` - reverse

**tcp_client.c - Simple TCP client:**
```c
#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define ISIZE (sizeof(struct sockaddr_in))
#define MAXCONN 3
#define BUFSIZE 32

int main(int argc, char *argv[]) {
    int fd, sz;
    struct sockaddr_in srv;
    char buf[32];

    memset(&srv, 0, ISIZE);
    srv.sin_family = AF_INET;
    inet_pton(AF_INET, argv[1], &(srv.sin_addr));
    srv.sin_port = htons(atoi(argv[2]));

    fd = socket(AF_INET, SOCK_STREAM, 0);
    connect(fd, (struct sockaddr*) &srv, ISIZE);

    do {
        fgets(buf, BUFSIZE, stdin);
        if(buf[0]!='\n')
            write(fd, buf, strlen(buf));
        sz = read(fd, buf, BUFSIZE);
        if(sz>0) printf("%s\n", buf);
    } while(sz);
    return 0;
}
```

**Usage:**
```bash
./tcp_qq_srver 127.0.0.1 1213 &
./tcp_client 127.0.0.1 1213
[press enter]
Connection 1!
```

**tcp_echo_serverSR.c - TCP echo server:**
```c
#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#define ISIZE (sizeof(struct sockaddr_in))
#define MAXCONN 3
#define BUFSIZE 32

int main(int argc, char *argv[]) {
    int fd, connfd, sz, port;
    struct sockaddr_in srv, peer;
    char buf[32];
    char addr[INET_ADDRSTRLEN+1];
    unsigned peersz=ISIZE;

    memset(&srv, 0, ISIZE);
    srv.sin_family = AF_INET;
    inet_pton(AF_INET, argv[1], &(srv.sin_addr));
    srv.sin_port = htons(atoi(argv[2]));

    fd = socket(AF_INET, SOCK_STREAM, 0);

    bind(fd, (struct sockaddr*) &srv, ISIZE);
    listen(fd, MAXCONN);

    while(1) {
        connfd = accept(fd, (struct sockaddr *) &peer, &peersz);
        sz = recv(connfd, buf, BUFSIZE, 0);
        inet_ntop(AF_INET, &peer.sin_addr, addr, INET_ADDRSTRLEN);
        port = ntohs(peer.sin_port);
        printf("Received %d bytes from %s, port %d\n", sz, addr, port);
        send(connfd, buf, sz, 0);
        close(connfd);
    }
    return 0;
}
```

## Workshop

### Socket System Calls Summary

**Common:** `socket`, `close`

**Client:** `connect`

**Server:** `bind`, `listen`, `accept`

**Send/Receive:** `read`, `write`, `send`, `recv`

## Homework

_TODO (not specified in original)_

### References

- OSI model (Wikipedia)
- Internet protocol suite (Wikipedia)
