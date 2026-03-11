# HSE ACOS Course - Part 2: Operating Systems - Complete Lecture Summary

**Course URL:** https://andrewt0301.github.io/hse-acos-course/
**Source repository:** https://github.com/andrewt0301/hse-acos-course

---

## Full Lecture List (15 lectures + 1 supplementary)

| # | Title | URL |
|---|-------|-----|
| 01 | Operating System Architecture. Linux | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/01_OS_Architecture/lecture.html) |
| 02 | C Programming Language | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/02_C/lecture.html) |
| 03 | System Calls | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/03_SystemCalls/lecture.html) |
| 04 | Linking and Loading | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/04_Linking/lecture.html) |
| 05 | Dynamic Memory Allocation (Strings workshop) | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/05_Strings/lecture.html) |
| 06 | Processes and Threads | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/06_Processes/lecture.html) |
| 07 | I/O and Files | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/07_Synch/lecture.html) |
| 08 | Threads and Synchronization | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/08_IPC/lecture.html) |
| 09 | Inter-Process Communication | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/09_IPC/lecture.html) |
| 10 | Users, Groups, and Permissions | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/10_Permissions/lecture.html) |
| 11 | Virtual Machines | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/11_VM/lecture.html) |
| 12 | Basics of Networking | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/12_Networking/lecture.html) |
| 13 | Sockets | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/13_Sockets/lecture.html) |
| 14 | High-level Languages and Operating System | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/14_Python/lecture.html) |
| 15 | Compiler Infrastructure Clang/LLVM | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/15_Clang/lecture.html) |
| XX | Linux Kernel Modules (supplementary) | [lecture.html](https://andrewt0301.github.io/hse-acos-course/part2os/XX_Linux_Modules/lecture.html) |

---

## Lecture 1: Operating System Architecture. Linux

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/01_OS_Architecture/lecture.html
**Slides:** PDF, PPTX

### Key Theory Topics
- Operating system architecture fundamentals
- Linux operating system overview
- Kernel vs. user space

### Workshop Content
- Installing VirtualBox
- Installing and using Linux Ubuntu in VirtualBox
- Setting up a GitHub repository
- Learning main Bash commands
- Workshop on Linux Kernel Modules

### References
- Operating System Structures (Chapter 2 in OSC textbook)
- Bash (Wikipedia)
- Linux man pages online

---

## Lecture 2: C Programming Language

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/02_C/lecture.html
**Slides:** PDF, PPTX

### Key Theory Topics
- History and overview of the C Language
- **Built-in data types:** `char`, `short`, `int`, `long`, `float`, `double`, `void`
- **Pointer types:** `void*`, `char*`, `int*`, `long*`
- **Type aliases:** `typedef`
- **Structures and bitfields:** `struct` definitions with bit-level field widths
- **Unions:** Memory-efficient data storage with shared memory layout
- **Input/output:** `scanf` and `printf` functions
- **Function pointers:** Declaring and using pointers to functions (`typedef void (* func_t)(int, int)`)
- **Dynamic memory allocation:** `malloc()` and `free()`
- **Preprocessor directives:** Header guards, macros, conditional compilation

### Important Code Examples
```c
// Structures
struct point {
    int x;
    int y;
};

// Bitfields
struct point {
    int x;
    int y1 : 16;
    int y2 : 16;
};

// Unions
union S {
    uint32_t u32;
    uint16_t u16[2];
    uint8_t  u8[4];
};

// Function pointers
typedef void (* func_t)(int, int);
```

### Workshop Content
- Writing and debugging small C programs covering different language features
- Learning to use GDB for debugging simple programs
- Discussion of practical programming questions

---

## Lecture 3: System Calls

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/03_SystemCalls/lecture.html
**Slides:** PDF, PPTX

### Key Theory Topics

#### System Call Types (6 categories):
1. **Process control:** creating/terminating processes, loading/executing programs, getting/setting process attributes, allocating/freeing memory, waiting/signaling events
2. **File management:** creating/deleting files, opening/closing files, reading/writing/repositioning, getting/setting file attributes
3. **Device management:** requesting/releasing devices, reading/writing/repositioning, getting/setting device attributes, attaching/detaching
4. **Information maintenance:** getting/setting date/time, system data, process/file/device attributes
5. **Communications:** create/delete communication connections, send/receive messages, transfer status information
6. **Protection:** getting/setting file permissions

#### System Calls Mechanism:
- System calls are operations provided by the OS kernel to user applications
- Executed through wrapper functions (e.g., glibc)
- Require mode switch between kernel and user modes (much slower than regular function calls)
- Architecture-dependent: use processor interrupt feature or special instructions
- Arguments passed via registers; extra arguments via stack

#### Linux API (3 methods to make syscalls in C):
1. **POSIX functions** mapped directly to syscalls: `open()`, `read()`, `write()`
2. **glibc wrapper functions** (OS-independent): `fopen()`, `scanf()`, `printf()`
3. **`syscall()` function** for indirect system calls by number

### Workshop Content
- System calls `open`, `close`, `read`, `write`
- System call `sbrk`
- Writing programs that use low-level I/O
- Reading chars from stdin, incrementing, and writing to stdout
- Opening files with flags (`O_RDONLY`, `O_WRONLY|O_CREAT|O_TRUNC`)
- Using command-line arguments (argc/argv) with file operations

### Important Code Examples
```c
// hello1.c - using printf (glibc)
// hello2.c - using write (POSIX)
// hello3.c - using syscall()
```

### References
- System call (Wikipedia), open/close/read/write/sbrk (Wikipedia)
- The GNU C Library (glibc), POSIX, C POSIX library

---

## Lecture 4: Linking and Loading

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/04_Linking/lecture.html
**Slides:** PDF, PPTX

### Key Theory Topics
- **Linking** (with code examples)
- **Relocatable object files**
- **Executable and Linkable Format (ELF)** - detailed analysis of object file structure
- **Shared libraries**
- **Linked libraries**
- **Library interpositioning** (compile-time, link-time, load-time/run-time)
- **Position-independent code (PIC)**
- **Address Space Layout Randomization (ASLR)**

### Tools for Manipulating Object Files
- `ar` - creates static libraries, inserts/deletes/lists/extracts members
- `strings` - lists printable strings in an object file
- `strip` - deletes symbol table information
- `nm` - lists symbols in the symbol table
- `size` - lists names and sizes of sections
- `readelf` - displays complete ELF structure
- `objdump` - can display all information; most useful for disassembling `.text` section
- `ldd` - lists shared libraries needed at run time

### Important Code Examples
- Detailed ELF disassembly examples using RISC-V toolchain:
  - Relocatable object files (`main.o`, `sum.o`) showing relocations
  - Final executable showing resolved addresses
- Library interpositioning at compile-time, link-time, and load-time/run-time

### Workshop Content
- Creating static libraries
- Creating shared libraries
- Library interpositioning
- Make scripts
- CMake build system

### Homework
- Provide link-time interpositioning for functions
- Provide load-time interpositioning for functions
- Create a Make script to build the program

### References
- Linking (Chapter 7 in CSPP), Section 2.5 in OSC, Chapters 41-42 in TLPI
- Address space layout randomization (Wikipedia)

---

## Lecture 5: Dynamic Memory Allocation

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/05_Strings/lecture.html
**Slides:** PDF, PPTX

### Key Theory Topics
- **Heap management and dynamic memory allocation**
- **`malloc` package** - `malloc()`, `free()`, `realloc()`
- **Internal and external fragmentation**
- **Implicit and explicit lists, segregated lists, sorting blocks by size**
- **Headers and footers** for block metadata
- **Placement policies** (first-fit, next-fit, best-fit)
- **Splitting and coalescing** of free blocks

### Important Code Examples
- Simple `malloc` implementation based on an implicit list and first-fit policy
- Demonstration with C++ programs using the custom malloc via `LD_PRELOAD`

### Workshop Content
Workshop on **strings in C language** (separate topic from the lecture):
- C string manipulation functions
- String handling exercises

### Practice Tasks (Exercises to improve malloc implementation)
1. Change to next-fit strategy, analyze memory utilization impact
2. Improve `realloc`: split blocks when reallocating smaller; extend current block if next block is empty and large enough
3. Use lower bits of header for block status to eliminate footers in allocated blocks
4. Implement explicit double-linked free list for faster search (minimum block size: 24-32 bytes)

### References
- Dynamic Memory Allocation (Section 9.9 in CSPP)
- Knuth, The Art of Computer Programming Vol. 1, Section 2.5
- Real-world implementations: glibc malloc, jemalloc, mimalloc
- Buddy memory allocation, Slab allocation (Wikipedia)

---

## Lecture 6: Processes and Threads

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/06_Processes/lecture.html
**Slides:** PDF, PPTX

### Key Theory Topics

#### Processes
- Isolated execution context with own address space for code and data
- OS switches between processes for concurrent execution
- Multicore processors allow limited true parallelism
- Each process assigned a unique **Process ID (PID)**

#### Process Management Tools
- `ps -ef` - lists currently running processes
- `top` - dynamic view of running processes and resources
- `/proc` virtual file system - provides process information (e.g., `/proc/version`)

#### Environment Variables
- Each process maintains a list of environment variables loaded at startup
- `printenv` utility to display environment variables
- C access via `extern char **environ` global variable
- Functions: `getenv()`, `putenv()`, `setenv()`, `unsetenv()`, `clearenv()`

#### Exit Handlers
- `atexit()` and `on_exit()` register handlers to execute on process termination
- Handlers called in reverse order of registration

#### Managing Processes
- **`fork()`** - creates child process by duplicating the calling process
- **`exec()`** - replaces process memory with a new program
- **`wait()`** - parent waits for child process to change state

#### Input and Output Redirection
- Standard streams: stdin (0), stdout (1), stderr (2)
- File descriptors `STDIN_FILENO`, `STDOUT_FILENO`, `STDERR_FILENO`

### Important Code Examples
```c
// Fork + exec + wait pattern
pid = fork();
if (pid == 0) {         // child
    execlp("/bin/ls", "ls", NULL);
} else {                 // parent
    wait(NULL);
    printf("Child Complete");
}

// Environment variable access
extern char **environ;
for (ep = environ; *ep != NULL; ep++)
    puts(*ep);

// Exit handlers
atexit(atexitFunc1);
on_exit(onexitFunc, (void *) 10);
```

---

## Lecture 7: I/O and Files

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/07_Synch/lecture.html
**Slides:** PDF (OS_Lecture_07.pdf), PPTX

### Key Theory Topics
- **Files and folders in Linux**
- **Directory hierarchy in Linux** (Filesystem Hierarchy Standard)
- **Virtual File System (VFS)** and basics of **Ext4**
- **System calls for working with files and folders**
- **Standard I/O, file descriptors, and redirection**
- **C standard library (libc) functions for I/O**
- **Inodes** - internal file system data structures

### Important Commands/Utilities
- `df` - report file system space usage
- `hwinfo --disk` - disk hardware information
- `stat` - display file or file system status (shows inode, size, blocks, permissions, timestamps)
- `strace` - trace system calls made by a program

### Important Code Examples
```c
// Copying stdin to stdout, one byte at a time
while(read(STDIN_FILENO, &c, 1) != 0)
    write(STDOUT_FILENO, &c, 1);

// Accessing file metadata with stat()
struct stat st;
stat(argv[1], &st);
if (S_ISREG(st.st_mode)) type = "regular";
else if (S_ISDIR(st.st_mode)) type = "directory";

// Reading a directory (ls-like program)
DIR *directory = opendir(dir_name);
while ((de = readdir(directory)))
    printf("Found file: %s\n", de->d_name);
closedir(directory);

// Buffering: multiple printf calls result in single write() syscall
printf("h"); printf("e"); printf("l"); printf("l"); printf("o"); printf("\n");
fflush(stdout);
// strace shows: write(1, "hello\n", 6) = 6
```

### Workshop Content
- Shell Scripts (references Lecture 6 Bash workshop)

### References
- File Systems (Chapter 14 in TLPI), System-Level I/O (Chapter 10 in CSPP)
- Filesystem Hierarchy Standard (Wikipedia)
- Overview of the Linux Virtual File System (kernel docs)
- Inode (Wikipedia), ext4 Data Structures and Algorithms (kernel docs)

---

## Lecture 8: Threads and Synchronization

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/08_IPC/lecture.html
**Slides:** PDF (OS_Lecture_08.pdf), PPTX

### Key Theory Topics
- **Threads** - lightweight processes sharing the same address space
- **Spinlocks** - busy-wait synchronization
- **Mutexes and locks** - blocking synchronization primitives
- **Conditional variables** - synchronization based on conditions
- **Thread local variables** - per-thread storage
- **Atomic variables** - lock-free synchronization

### POSIX Threads (pthreads) API
- `pthread_create()` - create a new thread
- `pthread_join()` - wait for thread completion
- `pthread_exit()` - exit a thread
- `pthread_detach()` - detach a thread
- `pthread_mutex_lock()` / `pthread_mutex_unlock()` - mutex operations
- `pthread_cond_signal()` / `pthread_cond_broadcast()` / `pthread_cond_wait()` - condition variables

### Compilation
```bash
gcc thread1.c -o thread -lpthread
```

### Important Code Examples

**1. Simple multithreaded program:**
```c
static void * threadFunc(void *arg) {
    char *s = (char *) arg;
    printf("%s", s);
    return (void *) strlen(s);
}
// pthread_create(&t1, NULL, threadFunc, "Hello world\n");
// pthread_join(t1, &res);
```

**2. Race condition demonstration (no synchronization):**
- Two threads incrementing a shared `glob` variable
- Shows incorrect results due to race conditions

**3. Synchronized version with mutexes:**
```c
static pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;
// Inside thread:
pthread_mutex_lock(&mtx);
loc = glob; loc++; glob = loc;
pthread_mutex_unlock(&mtx);
```

**4. Conditional variables** for producer-consumer pattern

---

## Lecture 9: Inter-Process Communication

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/09_IPC/lecture.html
**Slides:** PDF (OS_Lecture_09.pdf), PPTX

### Key Theory Topics

#### 1. Signals
- Asynchronous notification mechanism (one byte, idempotent)
- Sending signals with `kill()` system call
- Handling signals with `signal()` function
- Monitoring child processes with `wait()` and status macros (`WIFSIGNALED`, `WTERMSIG`)

#### 2. Message Queues (POSIX)
- Synchronous communication with content, queuing, and prioritization
- `mq_open()` - create a message queue
- `mq_send()` - send a message (with priority 0 to 32767 in Linux)
- `mq_receive()` - receive a message (highest priority first)
- `mq_unlink()` - remove a queue
- `mq_notify()` - subscribe for asynchronous message delivery via signals
- Compile with `-lrt` flag; queues visible in `/dev/mqueue/`

#### 3. Memory Mapping
- Linux paging mechanism: swapping pages to/from disk
- `mmap()` system call maps files to virtual memory address range
- `PROT_READ`, `MAP_PRIVATE` flags
- Acts as an array filled with file contents; uses paging under the hood

#### 4. Shared Memory
- `shm_open()` - create shared memory object
- `ftruncate()` - set shared memory size
- `mmap()` with `MAP_SHARED` - map shared memory for inter-process use

### Important Code Examples
```c
// Signal handling
void handler(int sig) { printf("Caught %d\n", sig); }
signal(SIGINT, handler);
signal(SIGSEGV, handler);

// Memory mapping (simple cat analog)
fd = open(argv[1], O_RDONLY);
fstat(fd, &sb);
addr = mmap(NULL, sb.st_size, PROT_READ, MAP_PRIVATE, fd, 0);
fwrite(addr, 1, sb.st_size, stdout);

// Message queue creation
mqd = mq_open(argv[1], O_RDWR|O_CREAT|O_EXCL, S_IRUSR|S_IWUSR, &attr);
```

### Workshop Content
- Workshop on POSIX message queues with full compile/run examples

---

## Lecture 10: Users, Groups, and Permissions

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/10_Permissions/lecture.html
**Slides:** PDF (OS_Lecture_10.pdf), PPTX

### Key Theory Topics
- **Access Control** - controlling who can access resources
- **Users and Groups** - Linux user/group management model
- **Permissions** - file permission system (rwx for user/group/others)
- **Links** - hard and symbolic links
- **setuid/setgid** - special permission bits

### Key Utilities
- `su`, `sudo`, `passwd` - user switching and password management
- `useradd`, `usermod`, `userdel` - user management
- `groupadd`, `groupmod`, `groupdel` - group management
- `chmod`, `chown`, `chgrp` - permission and ownership management
- `ln` - creating links
- `whoami` - display current user
- Configuration files: `/etc/passwd`, `/etc/group`

### Workshop Content (Hands-on exercises on local Ubuntu VM)
1. **Create users and groups:** `useradd -m myuser`, `passwd myuser`, `groupadd mygroup`
2. **Add users to groups:** `usermod -a -G mygroup myuser`
3. **Create test files** and observe ownership/permissions with `ls -li`
4. **Permission experiments:**
   - Demonstrate "Permission denied" for files without execute bit
   - `chmod u+x` to add user execute permission
   - `chmod g+x` to add group execute permission
   - Cross-user permission testing
5. **Ownership experiments:**
   - `chown` to change file ownership
   - Testing access after ownership change
6. **setuid/setgid experiments:**
   - Program that creates files (observing owner of created files)
   - Setting setuid bit: `chmod u+s hello`
   - Demonstrating that setuid program creates files owned by the file owner, not the runner

### Important Code Example
```c
// Program that creates a file (for setuid demonstration)
int fd = open("hello.txt", O_CREAT | O_WRONLY, S_IRUSR | S_IWUSR);
write(fd, hello, sizeof(hello));
close(fd);
```

---

## Lecture 11: Virtual Machines

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/11_VM/lecture.html
**Slides:** PDF (OS_Lecture_11.pdf), PPTX

### Key Theory Topics

#### 1. LLVM (Sub-lecture)
- Installing Clang: `sudo apt install clang`
- Translating C to LLVM IR: `clang -emit-llvm -c -S hello.c`
- LLVM IR is a typed, SSA-based intermediate representation
- Converting IR to bitcode: `llvm-as hello.ll -o hello.bc`
- Detailed LLVM IR code examples showing allocas, loads, stores, arithmetic ops

#### 2. Java Virtual Machine (Sub-lecture)
- Installing Java: `sudo apt install openjdk-21-jdk`
- Compiling and viewing bytecode: `javac Hello.java` then `javap -c Hello`
- Stack-based bytecode: `bipush`, `iload`, `istore`, `iadd`, `isub`, `idiv`
- JVM Specification reference

#### 3. Python Virtual Machine (Sub-lecture)
- Python bytecode and the `dis` module
- CPython interpreter internals

### Key Concepts
- Hypervisors (Type 1 and Type 2)
- VirtualBox, QEMU emulation
- Windows Subsystem for Linux (WSL), Hyper-V
- Comparison: hardware VMs vs. language VMs (JVM, Python VM, LLVM)

### References
- Virtual Machines (Chapter 18 in OSC)
- Hypervisor, VirtualBox, QEMU, WSL, Hyper-V (Wikipedia)

---

## Lecture 12: Basics of Networking

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/12_Networking/lecture.html
**Slides:** PDF (OS_Lecture_12.pdf), PPTX

### Key Theory Topics
- TCP/IP networking fundamentals
- DNS (Domain Name System)
- Network ports and services
- File downloading protocols (HTTP/HTTPS)

### Important Commands/Utilities
- **`wget`** - download files via HTTPS
  ```bash
  wget https://example.com/file.pdf
  ```
- **`curl`** - download files via HTTPS
  ```bash
  curl https://example.com/file.pdf -O
  ```
- **`/etc/services`** - standard port definitions (HTTP=80, HTTPS=443, HTTP-alt=8080)
- **`/etc/hosts`** - local hostname resolution file
- **`nslookup`** - DNS query tool
  ```bash
  nslookup hse.ru
  ```
- **`whois`** - query IP address registration information
- **`tcpdump`** - analyze TCP packets
  ```bash
  sudo tcpdump host 158.160.144.239 -v
  ```

### References
- Network Programming (Chapter 11 in CSPP)
- Sockets: Fundamentals of TCP/IP Networks (Chapter 58 in TLPI)
- TCP/IP, DNS (Wikipedia)

---

## Lecture 13: Sockets

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/13_Sockets/lecture.html
**Slides:** PDF (OS_Lecture_13.pdf), PPTX

### Key Theory Topics

#### Socket Concept
- Abstraction for asynchronous data transfer (bidirectional, two ends)
- Organized over various underlying layers:
  - Network sockets (TCP/UDP for IPv4) = **internet sockets**
  - Filesystem objects = **Unix domain sockets**

#### Socket Disciplines
- **Stream:** Ordered, reliable data (requires connection; handles corruption/loss/duplication; flow control)
- **Datagram:** Single message (no connection needed; no ordering/counting)

#### Socket Programming API
**Server setup:**
1. `socket(domain, type, 0)` - create socket
2. `bind(socket, address, length)` - bind to address
3. `listen(socket, queue_length)` - start listening
4. `accept(socket, address, &length)` - accept connection (returns data socket)
5. `recv(data_socket, buffer, length, 0)` or `read()` - receive data
6. `close()` - close sockets

**Client setup:**
1. `socket()` - create socket
2. `connect(socket, address, length)` - connect to server
3. `send(socket, buffer, length, 0)` - send data
4. `close()` - close socket

**Datagram:** `sendto()` / `recvfrom()` instead of connect+send/recv

### Important Code Examples

**Unix domain datagram** (server + client communicating via filesystem socket):
```c
// Server: socket -> bind -> listen -> recv
fd = socket(AF_UNIX, SOCK_DGRAM, 0);
bind(fd, (const struct sockaddr *) &srv, USIZE);
listen(fd, BLOG);
rsz = recv(fd, dgram, DSIZE, 0);

// Client: socket -> bind -> sendto
fd = socket(AF_UNIX, SOCK_DGRAM, 0);
sendto(fd, argv[2], strlen(argv[2]), 0, (const struct sockaddr *) &srv, USIZE);
```

**TCP client/server** (IPv4 stream sockets):
- `tcp_server.c` - accepts connections, sends connection count
- `tcp_client.c` - connects and receives data
- `tcp_qq_srver.c` - multi-connection TCP server

---

## Lecture 14: High-level Languages and Operating System

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/14_Python/lecture.html

### Key Theory Topics

#### OS and Programming Languages
- **Linux:** Kernel in C; core utils in C/C++/Python/Perl; scripting in shell/Perl/Python
- **Windows:** Core in C++; scripting via PowerShell, .NET, JScript, VBScript
- **macOS:** Kernel in C; core in Objective-C/C, now Swift; scripting in AppleScript/shell

#### Python and OS Programming
- Cross-platform but needs own OS feature implementations
- POSIX-oriented (Linux-focused)
- High-level but not OS-oriented (unlike shell)
- Rich ecosystem at PyPI for system-oriented tasks
- "Non resource scrimp" style: `.read()` instead of `.readline()`

#### Key Python Modules
- **`os` and `sys`:** Syscall wrappers (`.fork()`, `.getpid()`, `.fstat()`, `.popen()`, `.wait()`), environment variables, `sys.argv`, `sys.stdin`/`stdout`/`stderr`
- **`os.path` and `pathlib`:** Cross-platform path handling (`.is*()`, `.exists()`)
- **`subprocess`:**
  - `run()` - just run and get result (recommended over `os.system()`)
  - `Popen()` - high-level popen analog for pipelines
  - Example: replacing shell pipelines with `Popen` chains
- **`multiprocessing`:**
  - Uses `fork()` on Linux; avoids GIL limitations
  - Child process runs a function (not equal process like fork)
  - Communication via socket-like objects or object queues
  - Shared memory-alike objects or object managers
  - Process pools: exactly N workers running in parallel
- **`threading`:** Exists but limited by GIL; useful only when one thread is CPU-bound and others do I/O
- **`platform`**, **`time`**, **`datetime`**, **`calendar`**

### Important Code Example
```python
from subprocess import *
p1 = Popen(["cal", "-s"], stdout=PIPE)
p2 = Popen(["hexdump", "-C"], stdin=p1.stdout, stdout=PIPE)
p1.stdout.close()
res = p2.communicate()
print(res[0].decode())
```

### Workshop
- Python workshop exercises

---

## Lecture 15: Compiler Infrastructure Clang/LLVM

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/15_Clang/lecture.html
**Slides:** PDF (OS_Lecture_15.pdf), PPTX

### Key Theory Topics
- **LLVM Project:** History and architecture
- **LLVM IR:** Intermediate representation format
- **Clang:** Parser and static analysis tool
- **Clang Static Analyzer:** Symbolic execution engine
- **Clang Extra Tools:** Clang-Tidy linter

### Workshop Content
1. **Compiling/Installing LLVM** (including building from source with Ninja + CMake)
2. **Compiling C/C++ programs** to executables and LLVM IR
3. **Exploring C/C++ programs with Clang:**
   - Tokens: `clang test.c -Xclang -dump-tokens`
   - AST (Abstract Syntax Tree): `clang test.c -Xclang -ast-dump`
   - Call graph visualization with Graphviz
   - CFG (Control Flow Graph)
   - Exploded graph (for static analysis)
4. **Running Clang Static Analyzer**
5. **Exploring Clang Static Analyzer and Clang-Tidy checks**

### Build Phases
```bash
clang hello.c -o hello -ccc-print-phases
#  0: input, "hello.c", c
#  1: preprocessor, {0}, cpp-output
#  2: compiler, {1}, ir
#  3: backend, {2}, assembler
#  4: assembler, {3}, object
#  5: linker, {4}, image
```

### Key Commands
```bash
# Install Clang
sudo apt install clang

# Preprocess
clang test.c -E

# Compile to LLVM IR
clang -emit-llvm -c -S hello.c

# View tokens
clang test.c -Xclang -dump-tokens

# View AST
clang test.c -Xclang -ast-dump

# View build phases
clang hello.c -o hello -ccc-print-phases
```

---

## Supplementary: Linux Kernel Modules

**URL:** https://andrewt0301.github.io/hse-acos-course/part2os/XX_Linux_Modules/lecture.html

### Key Theory Topics
- **OS Architecture:** Kernel (core software controlling hardware) + system programs
- **Kernel responsibilities:** Process management, memory management, filesystem management, device control, networking
- **Linux hybrid approach:** Monolithic kernel with dynamically loadable modules
- **Module types:** Device drivers and other kernel extensions
- **Module management:** `insmod` (load), `rmmod` (unload), modules execute in kernel mode

### Important Code Example: "Hello World" Kernel Module
```c
#include <linux/init.h>
#include <linux/module.h>

static int hello_init(void) {
    printk(KERN_ALERT "Hello, world\n");
    return 0;
}

static void hello_exit(void) {
    printk(KERN_ALERT "Goodbye, cruel world\n");
}

module_init(hello_init);
module_exit(hello_exit);
MODULE_LICENSE("GPL");
```

### Key Concepts
- `module_init()` / `module_exit()` macros
- `printk()` for kernel-level logging
- Makefile for building modules using the kernel build system
- `MODULE_LICENSE()` declaration

---

## Course Textbook References

The course references several key textbooks (abbreviated):
- **[CSPP]** Computer Systems: A Programmer's Perspective (Bryant & O'Hallaron)
- **[OSC]** Operating System Concepts (Silberschatz, Galvin, Gagne)
- **[TLPI]** The Linux Programming Interface (Kerrisk)
- **[COMET]** Operating Systems: Three Easy Pieces (Arpaci-Dusseau)
- **[PGLC]** Practical Guide to Linux Commands, Editors, and Shell Programming
