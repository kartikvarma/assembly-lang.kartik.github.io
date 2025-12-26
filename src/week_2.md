# Week 2: Introduction to Assembly Language

## Overview
This week you'll set up your development environment, write your first assembly programs, understand program structure, and implement basic input/output operations.

---

## Day 1-2: Development Environment Setup

### Learning Objectives
- Install and configure assembler (NASM)
- Install and configure debugger (GDB)
- Set up text editor with assembly support
- Compile and run your first program
- Understand the assembly toolchain

### Installation

#### Linux (Ubuntu/Debian)
```bash
# Update package list
sudo apt-get update

# Install NASM (Netwide Assembler)
sudo apt-get install nasm

# Install GDB (GNU Debugger)
sudo apt-get install gdb

# Install build essentials
sudo apt-get install build-essential

# Verify installations
nasm -v
gdb --version
```

#### macOS
```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install NASM
brew install nasm

# Install GDB
brew install gdb

# Verify installations
nasm -v
gdb --version
```

#### Windows (WSL - Windows Subsystem for Linux)
```bash
# Enable WSL and install Ubuntu from Microsoft Store
# Then follow Linux instructions above

# Alternative: Install NASM for Windows directly
# Download from: https://www.nasm.us/
# Add to PATH environment variable
```

### Text Editor Setup

#### VS Code (Recommended)
```bash
# Install VS Code
# Download from: https://code.visualstudio.com/

# Install extensions:
# 1. "x86 and x86_64 Assembly" by 13xforever
# 2. "ASM Code Lens" by maziac
# 3. "NASM" by Right-5

# Optional: Configure build tasks (tasks.json)
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "assemble",
            "type": "shell",
            "command": "nasm -f elf64 ${file} -o ${fileBasenameNoExtension}.o",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "link",
            "type": "shell",
            "command": "ld ${fileBasenameNoExtension}.o -o ${fileBasenameNoExtension}",
            "group": "build"
        }
    ]
}
```

#### Vim/Neovim
```vim
" Add to .vimrc or init.vim
" Syntax highlighting
syntax on
filetype plugin indent on

" NASM-specific settings
autocmd BufRead,BufNewFile *.asm set filetype=nasm
autocmd FileType nasm setlocal expandtab shiftwidth=4 softtabstop=4
```

### Your First Assembly Program

#### hello.asm
```nasm
; hello.asm - First assembly program
; Purpose: Print "Hello, Assembly World!" to console

section .data
    ; Define our message
    msg db 'Hello, Assembly World!', 0x0A  ; 0x0A = newline
    msg_len equ $ - msg                     ; Calculate length

section .text
    global _start       ; Entry point for linker

_start:
    ; Write message to stdout
    ; syscall: write(fd, buffer, count)
    mov rax, 1          ; syscall number for write
    mov rdi, 1          ; file descriptor: 1 = stdout
    mov rsi, msg        ; pointer to message
    mov rdx, msg_len    ; message length
    syscall             ; invoke system call

    ; Exit program
    ; syscall: exit(status)
    mov rax, 60         ; syscall number for exit
    xor rdi, rdi        ; exit status: 0 = success
    syscall             ; invoke system call
```

### Build Process

#### Step-by-Step Build
```bash
# Step 1: Assemble (source code → object file)
nasm -f elf64 hello.asm -o hello.o
# -f elf64: output format for 64-bit Linux
# hello.asm: input source file
# -o hello.o: output object file

# Step 2: Link (object file → executable)
ld hello.o -o hello
# ld: GNU linker
# hello.o: input object file
# -o hello: output executable

# Step 3: Run
./hello
```

#### Understanding the Output
```
Hello, Assembly World!
```

#### Build Script (Makefile)
```makefile
# Makefile for assembly programs
ASM = nasm
ASMFLAGS = -f elf64
LINKER = ld
LDFLAGS = 

# Default target
all: hello

# Build hello
hello: hello.o
	$(LINKER) $(LDFLAGS) hello.o -o hello

hello.o: hello.asm
	$(ASM) $(ASMFLAGS) hello.asm -o hello.o

# Clean build artifacts
clean:
	rm -f *.o hello

# Phony targets
.PHONY: all clean
```

Usage:
```bash
make        # Build
make clean  # Clean
```

### Understanding System Calls

#### What is a System Call?
- Interface between user program and operating system
- Requests OS services (I/O, memory, processes)
- x86-64 Linux uses the `syscall` instruction

#### Common Linux System Calls (x86-64)

| Syscall | RAX | Description | Parameters |
|---------|-----|-------------|------------|
| read    | 0   | Read from file | RDI=fd, RSI=buffer, RDX=count |
| write   | 1   | Write to file | RDI=fd, RSI=buffer, RDX=count |
| open    | 2   | Open file | RDI=filename, RSI=flags, RDX=mode |
| close   | 3   | Close file | RDI=fd |
| exit    | 60  | Exit program | RDI=status |

#### File Descriptors
- 0 = stdin (standard input)
- 1 = stdout (standard output)
- 2 = stderr (standard error)

### Exercises

#### Exercise 1: Modify the Message
Change the message to print your name:
```nasm
msg db 'Hello, [Your Name]!', 0x0A
```

#### Exercise 2: Multiple Messages
Print two lines:
```nasm
section .data
    msg1 db 'First line', 0x0A
    len1 equ $ - msg1
    msg2 db 'Second line', 0x0A
    len2 equ $ - msg2

section .text
    global _start

_start:
    ; Print first message
    mov rax, 1
    mov rdi, 1
    mov rsi, msg1
    mov rdx, len1
    syscall

    ; Print second message
    mov rax, 1
    mov rdi, 1
    mov rsi, msg2
    mov rdx, len2
    syscall

    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

#### Exercise 3: Exit with Different Status
```nasm
; Exit with status code 42
mov rax, 60
mov rdi, 42
syscall
```

Check exit status:
```bash
./hello
echo $?     # Prints: 42
```

#### Exercise 4: Using GDB
```bash
# Assemble with debug symbols
nasm -f elf64 -g -F dwarf hello.asm -o hello.o
ld hello.o -o hello

# Debug
gdb ./hello

# GDB commands:
(gdb) break _start      # Set breakpoint
(gdb) run               # Run program
(gdb) info registers    # Show registers
(gdb) stepi             # Step one instruction
(gdb) continue          # Continue execution
(gdb) quit              # Exit GDB
```

### Troubleshooting Common Issues

#### Issue 1: "command not found: nasm"
```bash
# Check if installed
which nasm

# If not found, reinstall
sudo apt-get install nasm
```

#### Issue 2: "Permission denied"
```bash
# Make executable
chmod +x hello
./hello
```

#### Issue 3: "cannot execute binary file"
```bash
# Check if correct architecture
file hello
# Should show: ELF 64-bit LSB executable, x86-64

# If wrong architecture, reassemble with correct format
nasm -f elf64 hello.asm -o hello.o
```

#### Issue 4: Segmentation Fault
- Check that you're using correct syscall numbers
- Verify register values before syscall
- Use GDB to find exact failure point

---

## Day 3-4: Program Structure & Sections

### Learning Objectives
- Understand assembly program organization
- Master different section types
- Learn data declaration directives
- Use special operators ($ and $$)

### Section Types

#### .data Section
Purpose: Initialized data (variables with values)

```nasm
section .data
    ; Syntax: label <directive> <value>
    
    ; Integer data
    byte_var    db 0x42             ; Define byte (1 byte)
    word_var    dw 0x1234           ; Define word (2 bytes)
    dword_var   dd 0x12345678       ; Define double word (4 bytes)
    qword_var   dq 0x123456789ABCDEF0  ; Define quad word (8 bytes)
    
    ; Multiple values (arrays)
    array       dd 1, 2, 3, 4, 5    ; Array of 5 dwords
    matrix      db 1, 2, 3, 4       ; 2x2 matrix as flat array
    
    ; Strings
    string1     db 'Hello', 0       ; Null-terminated string
    string2     db 'World', 0x0A, 0 ; With newline
    
    ; Floating-point
    float_var   dd 3.14159          ; Single precision (4 bytes)
    double_var  dq 2.71828          ; Double precision (8 bytes)
    
    ; Repeated values
    zeros       times 10 db 0       ; 10 bytes of zeros
    buffer      times 100 db ' '    ; 100 spaces
```

#### .bss Section
Purpose: Uninitialized data (reserved space)

```nasm
section .bss
    ; Syntax: label <reserve_directive> <count>
    
    ; Reserve bytes
    buffer1     resb 64             ; Reserve 64 bytes
    buffer2     resb 256            ; Reserve 256 bytes
    
    ; Reserve words/dwords/qwords
    word_buf    resw 10             ; Reserve 10 words (20 bytes)
    dword_buf   resd 20             ; Reserve 20 dwords (80 bytes)
    qword_buf   resq 5              ; Reserve 5 qwords (40 bytes)
    
    ; Large buffers
    large_buf   resb 1024*1024      ; Reserve 1 MB
```

**Why use .bss?**
- Doesn't increase executable file size
- OS allocates memory at runtime
- Automatically initialized to zero

#### .text Section
Purpose: Executable code

```nasm
section .text
    global _start       ; Make _start visible to linker
    
_start:
    ; Your code here
    mov rax, 1
    mov rdi, 1
    mov rsi, msg
    mov rdx, len
    syscall
    
    ; More code
    mov rax, 60
    xor rdi, rdi
    syscall

; Additional functions
my_function:
    ; Function code
    ret
```

#### .rodata Section
Purpose: Read-only data (constants)

```nasm
section .rodata
    ; Constants that shouldn't be modified
    pi          dd 3.14159
    version     db 'Version 1.0', 0
    error_msg   db 'Error occurred!', 0x0A
    msg_len     equ $ - error_msg
```

### Data Declaration Directives

#### Size Directives

| Directive | Size | Description | Example |
|-----------|------|-------------|---------|
| DB | 1 byte | Define Byte | db 42, 'A', 0xFF |
| DW | 2 bytes | Define Word | dw 0x1234, 1000 |
| DD | 4 bytes | Define Double Word | dd 0x12345678 |
| DQ | 8 bytes | Define Quad Word | dq 0x123456789ABCDEF0 |
| DT | 10 bytes | Define Ten Bytes | dt 1.23 (extended precision) |

#### Reserve Directives

| Directive | Size | Description | Example |
|-----------|------|-------------|---------|
| RESB | 1 byte | Reserve Bytes | resb 100 |
| RESW | 2 bytes | Reserve Words | resw 50 |
| RESD | 4 bytes | Reserve Double Words | resd 25 |
| RESQ | 8 bytes | Reserve Quad Words | resq 10 |

### Special Operators

#### $ Operator (Current Position)
Returns the current address in the section

```nasm
section .data
    msg db 'Hello, World!', 0x0A
    msg_len equ $ - msg     ; Calculate length
    ; $ points to position after msg
    ; msg_len = current_position - start_of_msg = length
```

Example:
```nasm
section .data
    str1 db 'First'         ; Address: 0x1000
    len1 equ $ - str1       ; $ = 0x1005, len1 = 5
    
    str2 db 'Second'        ; Address: 0x1005
    len2 equ $ - str2       ; $ = 0x100B, len2 = 6
```

#### $$ Operator (Section Start)
Returns the address of the beginning of current section

```nasm
section .data
    ; $$ points to start of .data section
    
    var1 dd 100
    offset1 equ $ - $$      ; Offset from section start
    
    var2 dd 200
    offset2 equ $ - $$      ; Different offset
```

#### EQU Directive (Define Constant)
Defines a constant value (no memory allocated)

```nasm
section .data
    ; Constants
    BUFFER_SIZE equ 1024
    MAX_USERS equ 100
    EOF equ -1
    
    ; Calculated constants
    msg db 'Hello'
    MSG_LEN equ $ - msg
    
    ; Using constants
    buffer resb BUFFER_SIZE
    users resd MAX_USERS
```

### Complete Program Example

```nasm
; complete_example.asm
; Demonstrates all sections and directives

; Constants
BUFFER_SIZE equ 64
SUCCESS equ 0
FAILURE equ 1

section .data
    ; Messages
    prompt db 'Enter your name: '
    prompt_len equ $ - prompt
    
    greeting db 'Hello, '
    greeting_len equ $ - greeting
    
    newline db 0x0A
    
    ; Numbers
    counter dd 0
    max_count dd 10
    
    ; Arrays
    numbers dd 1, 2, 3, 4, 5
    numbers_count equ ($ - numbers) / 4  ; Count of dwords

section .bss
    ; Buffers
    name_buffer resb BUFFER_SIZE
    temp_buffer resb 256
    
    ; Variables
    result resq 1
    status resd 1

section .rodata
    ; Constants (read-only)
    version db 'v1.0.0', 0
    author db 'Your Name', 0

section .text
    global _start

_start:
    ; Print prompt
    mov rax, 1              ; sys_write
    mov rdi, 1              ; stdout
    mov rsi, prompt
    mov rdx, prompt_len
    syscall
    
    ; Read name
    mov rax, 0              ; sys_read
    mov rdi, 0              ; stdin
    mov rsi, name_buffer
    mov rdx, BUFFER_SIZE
    syscall
    
    ; Store bytes read
    mov [result], rax
    
    ; Print greeting
    mov rax, 1
    mov rdi, 1
    mov rsi, greeting
    mov rdx, greeting_len
    syscall
    
    ; Print name
    mov rax, 1
    mov rdi, 1
    mov rsi, name_buffer
    mov rdx, [result]
    syscall
    
    ; Exit
    mov rax, 60
    mov rdi, SUCCESS
    syscall
```

### Exercises

#### Exercise 1: Data Declaration Practice
Create a program with:
- 5 different integer types (byte, word, dword, qword)
- 2 strings
- 1 array of 10 elements
- 1 floating-point number

#### Exercise 2: Calculate Sizes
```nasm
section .data
    array1 db 1, 2, 3, 4, 5
    array1_len equ $ - array1       ; What is the value?
    
    array2 dw 1, 2, 3, 4, 5
    array2_len equ $ - array2       ; What is the value?
    
    array3 dd 1, 2, 3, 4, 5
    array3_len equ $ - array3       ; What is the value?
```

Calculate lengths and element counts for each array.

#### Exercise 3: Menu Program
Create a program that displays a menu:
```
=== Main Menu ===
1. Option One
2. Option Two
3. Option Three
4. Exit
Enter choice:
```

Use proper sections and calculate all string lengths.

#### Exercise 4: Buffer Allocation
Allocate buffers in .bss section:
- Input buffer: 256 bytes
- Output buffer: 512 bytes
- Temp storage: 1024 bytes

#### Exercise 5: Constants and Calculations
```nasm
; Define constants
SCREEN_WIDTH equ 80
SCREEN_HEIGHT equ 25
SCREEN_SIZE equ SCREEN_WIDTH * SCREEN_HEIGHT

; Use them
section .bss
    screen_buffer resb SCREEN_SIZE
```

Create similar constants for:
- Maximum string length
- Number of menu items
- Buffer size in KB

### Mini-Project: Information Display

Create a program that displays:
```
====================================
    System Information Display
====================================
Program: Your Program Name
Version: 1.0.0
Author: Your Name
Built: 2024

Press Enter to exit...
====================================
```

Requirements:
- Use all four section types (.data, .bss, .text, .rodata)
- Calculate all string lengths with $
- Use EQU for constants
- Wait for user input before exiting

---

## Day 5-7: System Calls & Basic I/O

### Learning Objectives
- Master Linux system call interface
- Implement input operations (read)
- Implement output operations (write)
- Handle file descriptors
- Create interactive programs

### Linux System Call Interface

#### System Call Mechanism
1. Place syscall number in RAX
2. Place arguments in: RDI, RSI, RDX, R10, R8, R9
3. Execute `syscall` instruction
4. Result returned in RAX
5. Errors: RAX contains negative errno

#### System Call Table (x86-64 Linux)

| Number | Name | RAX | Arguments |
|--------|------|-----|-----------|
| 0 | read | 0 | RDI=fd, RSI=buf, RDX=count |
| 1 | write | 1 | RDI=fd, RSI=buf, RDX=count |
| 2 | open | 2 | RDI=pathname, RSI=flags, RDX=mode |
| 3 | close | 3 | RDI=fd |
| 60 | exit | 60 | RDI=status |
| 89 | readlink | 89 | RDI=path, RSI=buf, RDX=bufsiz |

Full list: `/usr/include/asm/unistd_64.h`

### File Descriptors

#### Standard Descriptors
```nasm
STDIN  equ 0    ; Standard input
STDOUT equ 1    ; Standard output
STDERR equ 2    ; Standard error
```

Usage:
```nasm
; Write to stdout
mov rdi, STDOUT

; Write to stderr
mov rdi, STDERR

; Read from stdin
mov rdi, STDIN
```

### Output Operations (write syscall)

#### Basic Write
```nasm
; ssize_t write(int fd, const void *buf, size_t count);
; Returns: number of bytes written, or -1 on error

section .data
    msg db 'Hello!', 0x0A
    msg_len equ $ - msg

section .text
    global _start

_start:
    mov rax, 1          ; syscall: write
    mov rdi, 1          ; fd: stdout
    mov rsi, msg        ; buffer address
    mov rdx, msg_len    ; byte count
    syscall             ; RAX = bytes written
    
    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

#### Writing Multiple Strings
```nasm
section .data
    str1 db 'Line 1', 0x0A
    len1 equ $ - str1
    str2 db 'Line 2', 0x0A
    len2 equ $ - str2
    str3 db 'Line 3', 0x0A
    len3 equ $ - str3

section .text
    global _start

_start:
    ; Write string 1
    mov rax, 1
    mov rdi, 1
    mov rsi, str1
    mov rdx, len1
    syscall
    
    ; Write string 2
    mov rax, 1
    mov rdi, 1
    mov rsi, str2
    mov rdx, len2
    syscall
    
    ; Write string 3
    mov rax, 1
    mov rdi, 1
    mov rsi, str3
    mov rdx, len3
    syscall
    
    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

#### Writing to stderr
```nasm
section .data
    error_msg db 'ERROR: Something went wrong!', 0x0A
    error_len equ $ - error_msg

section .text
    global _start

_start:
    ; Write to stderr
    mov rax, 1
    mov rdi, 2          ; stderr = 2
    mov rsi, error_msg
    mov rdx, error_len
    syscall
    
    ; Exit with error code
    mov rax, 60
    mov rdi, 1          ; exit status: 1 = error
    syscall
```

### Input Operations (read syscall)

#### Basic Read
```nasm
; ssize_t read(int fd, void *buf, size_t count);
; Returns: number of bytes read, 0 on EOF, -1 on error

section .bss
    buffer resb 64      ; 64-byte buffer

section .text
    global _start

_start:
    ; Read from stdin
    mov rax, 0          ; syscall: read
    mov rdi, 0          ; fd: stdin
    mov rsi, buffer     ; buffer address
    mov rdx, 64         ; max bytes to read
    syscall             ; RAX = bytes actually read
    
    ; RAX now contains number of bytes read
    ; buffer contains the data
    
    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

#### Echo Program
```nasm
; Reads input and writes it back

section .bss
    input_buffer resb 256

section .text
    global _start

_start:
    ; Read from stdin
    mov rax, 0
    mov rdi, 0
    mov rsi, input_buffer
    mov rdx, 256
    syscall
    
    ; Save bytes read
    mov r12, rax        ; Store count in R12
    
    ; Write to stdout
    mov rax, 1
    mov rdi, 1
    mov rsi, input_buffer
    mov rdx, r12        ; Use saved count
    syscall
    
    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

### Interactive Programs

#### Prompt and Read
```nasm
section .data
    prompt db 'Enter your name: '
    prompt_len equ $ - prompt
    
    greeting db 'Hello, '
    greeting_len equ $ - greeting
    
    newline db 0x0A

section .bss
    name resb 32

section .text
    global _start

_start:
    ; Display prompt
    mov rax, 1
    mov rdi, 1
    mov rsi, prompt
    mov rdx, prompt_len
    syscall
    
    ; Read name
    mov rax, 0
    mov rdi, 0
    mov rsi, name
    mov rdx, 32
    syscall
    mov r12, rax        ; Save length
    
    ; Display greeting
    mov rax, 1
    mov rdi, 1
    mov rsi, greeting
    mov rdx, greeting_len
    syscall
    
    ; Display name
    mov rax, 1
    mov rdi, 1
    mov rsi, name
    mov rdx, r12
    syscall
    
    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

#### Multiple Inputs
```nasm
section .data
    prompt1 db 'Enter first number: '
    plen1 equ $ - prompt1
    prompt2 db 'Enter second number: '
    plen2 equ $ - prompt2
    result_msg db 'Numbers entered!', 0x0A
    rlen equ $ - result_msg

section .bss
    num1 resb 16
    num2 resb 16

section .text
    global _start

_start:
    ; Get first number
    mov rax, 1
    mov rdi, 1
    mov rsi, prompt1
    mov rdx, plen1
    syscall
    
    mov rax, 0
    mov rdi, 0
    mov rsi, num1
    mov rdx, 16
    syscall
    
    ; Get second number
    mov rax, 1
    mov rdi, 1
    mov rsi, prompt2
    mov rdx, plen2
    syscall
    
    mov rax, 0
    mov rdi, 0
    mov rsi, num2
    mov rdx, 16
    syscall
    
    ; Display result
    mov rax, 1
    mov rdi, 1
    mov rsi, result_msg
    mov rdx, rlen
    syscall
    
    ; Exit
    mov rax, 60
    xor rdi, rdi
    syscall
```

### Exercises

#### Exercise 1: Simple Output
Create a program that prints:
```
********************
* Assembly Rules! *
********************
```

#### Exercise 2: Error Message
Write a program that:
1. Prints normal message to stdout
2. Prints error message to stderr
3. Exits with code 1

#### Exercise 3: Name Collector
Prompt for and read:
- First name
- Last name
- Age
Display all information back

#### Exercise 4: Character Counter
Read input and count how many characters were entered (use return value of read).

#### Exercise 5: Loop Input
Read and echo input 5 times in a loop.

### Complete Project: Interactive Menu

```nasm
; menu.asm - Interactive menu system

section .data
    banner db '=========================', 0x0A
    banner_len equ $ - banner
    
    title db '    MAIN MENU', 0x0A
    title_len equ $ - title
    
    opt1 db '1. View Information', 0x0A
    opt1_len equ $ - opt1
    
    opt2 db '2. Settings', 0x0A
    opt2_len equ $ - opt2
    
    opt3 db '3. Exit', 0x0A
    opt3_len equ $ - opt3
    
    prompt db 'Enter choice: '
    prompt_len equ $ - prompt
    
    choice1_msg db 'You selected: View Information', 0x0A
    choice1_len equ $ - choice1_msg
    
    choice2_msg db 'You selected: Settings', 0x0A
    choice2_len equ $ - choice2_msg
    
    exit_msg db 'Goodbye!', 0x0A
    exit_len equ $ - exit_msg
    
    invalid_msg db 'Invalid choice!', 0x0A
    invalid_len equ $ - invalid_msg
    
    newline db 0x0A

section .bss
    choice resb 2

section .text
    global _start

_start:
    ; Print banner
    mov rax, 1
    mov rdi, 1
    mov rsi, banner
    mov rdx, banner_len
    syscall
    
    ; Print title
    mov rax, 1
    mov rdi, 1
    mov rsi, title
    mov rdx, title_len
    syscall
    
    ; Print banner again
    mov rax, 1
    mov rdi, 1
    mov rsi, banner
    mov rdx, banner_len
    syscall
    
    ; Print options
    mov rax, 1
    mov rdi, 1
    mov rsi, opt1
    mov rdx, opt1_len
    syscall
    
    mov rax, 1
    mov rdi, 1
    mov rsi, opt2
    mov rdx, opt2_len
    syscall
    
    mov rax, 1
    mov rdi, 1
    mov rsi, opt3
    mov rdx, opt3_len
    syscall
    
    ; Print newline
    mov rax, 1
    mov rdi, 1
    mov rsi, newline
    mov rdx, 1
    syscall
    
    ; Print prompt
    mov rax, 1
    mov rdi, 1
    mov rsi, prompt
    mov rdx, prompt_len
    syscall
    
    ; Read choice
    mov rax, 0
    mov rdi, 0
    mov rsi, choice
    mov rdx, 2
    syscall
    
    ; Check choice
    mov al, [choice]
    cmp al, '1'
    je choice_1
    cmp al, '2'
    je choice_2
    cmp al, '3'
    je choice_3
    jmp invalid_choice

choice_1:
    mov rax, 1
    mov rdi, 1
    mov rsi, choice1_msg
    mov rdx, choice1_len
    syscall
    jmp exit_program

choice_2:
    mov rax, 1
    mov rdi, 1
    mov rsi, choice2_msg
    mov rdx, choice2_len
    syscall
    jmp exit_program

choice_3:
    mov rax, 1
    mov rdi, 1
    mov rsi, exit_msg
    mov rdx, exit_len
    syscall
    jmp exit_program

invalid_choice:
    mov rax, 1
    mov rdi, 1
    mov rsi, invalid_msg
    mov rdx, invalid_len
    syscall

exit_program
