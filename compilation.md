in the 'compiler_interpreter.md' file i have talked about the compiler and what he does here i will talk about the compilation process in more details and will talk about some new concepts.
# compilation process
the compilation process is the process of converting the source code (text file) that you wrote in a programming language to machine code (binary file) that can be executed by the CPU.
the compilation process goes through several stages to convert the source code to machine code. 
1. preprocessing: in this stage, the compiler removes comments and whitespace from the code, and handles directives (like #include in C/C++).
2. compilation: in this stage, the compiler translates the preprocessed code into intermediate representation (IR) code (a low-level code that is easier to analyze and optimize than the source code).
3. optimization: in this stage, the compiler analyzes the IR code and applies various optimization techniques to improve the performance of the code (like removing dead code, inlining functions, etc.).
4. code generation: in this stage, the compiler translates the optimized IR code into assembly code (low-level code that is specific to a particular CPU architecture).
5. assembly: in this stage, the assembler converts the assembly code into machine code (binary code).
6. linking: in this stage, the linker combines the machine code with any necessary libraries and creates the final executable binary file.
in short the compiler takes the source code and converts it to assembly code then the assembler converts the assembly code to machine code and finally the linker combines the machine code with any necessary libraries to create the final executable binary file.
1. main.c (c language)--> main.i (after removing the comment) --> main.s (assembly code)
2. main.i (after removing the comment) --> main.s (assembly code)
3. main.s (assembly code)--> main.o (machine code)
4. main.o (machine code) --> a.out (executable binary file)
the (0's and 1's) file is produced after the assembly phase so why do i need the linking phase ? the linking phase is used to combine multiple (1's and 0's) files into a single executable file so all the (1's and 0's) will be in one file .
* i want to talk about the second stage where the compiler translates the code to assembly instructions.
speaking about the assembly instructions, assembly instructions are low-level instructions that are specific to a particular CPU architecture. each assembly instruction corresponds to a specific machine code instruction that can be executed by the CPU.
and the CPU can run in two modes: user mode and kernel mode.
when the CPU is in user mode, it can only execute user-level instructions (like arithmetic operations, memory access, etc.) and when the CPU is in kernel mode, it can execute both user-level instructions and kernel-level instructions (a hardware-level instructions that can access system resources and perform privileged operations).
but what if the CPU is in user mode and it tries to execute a kernel-level instruction ? the CPU will raise a protection fault (a type of exception that occurs when a program tries to access a memory location or perform an operation that it is not allowed to).
the programs that run in user mode are called user-space programs and the programs that run in kernel mode are called kernel-space programs.
my programs that i write in c or rust are user-space programs so they can only execute user-level instructions.
the os (operating system) is a kernel-space program so it can execute both user-level instructions and kernel-level instructions.
we have said that when the CPU is in user mode, it can only execute user-level instructions, so how can my user-space program (like a c program) access system resources (like files, network, etc.) that require kernel-level instructions ?
the answer is through system calls.
a system call is a mechanism that allows a user-space program to request a service from the kernel (like accessing files, network, etc.).
when a user-space program makes a system call, the CPU switches from user mode to kernel mode, executes the requested service in kernel mode, and then switches back to user mode.
for example, when a c program wants to read a file, it makes a system call to the kernel to request the file read operation. the CPU switches to kernel mode, executes the file read operation, and then switches back to user mode to return the result to the c program.
so the read function in c does not contain the actual code to read a file, instead it contains the code to make a system call to the kernel (instruction to switch to kernel code) and the kernel contains the actual code to read a file which will run in the kernel mode.

after we have talked about the CPU instrections (the one's that runs in the kernel mode and the ones thats runs in the user mode) and who does the CPU handle these instructions (the operating system and the programs), now its time to talk about how we can write these instructions.
also we have talked about the compiler and what he does to the source code (it converts it to CPU instructions (machine code)).
now when i for example write a c code file:
```c
#include <stdio.h>
int main() {
    int a = 5;
    int b = 10;
    int sum = a + b;
    return 0;
}
```
these are the intsructions that the compiler will convert them to assembly code.
```assembly
section .text
    global _start
_start:
    mov eax, 5      ; load 5 into register eax
    mov ebx, 10     ; load 10 into register ebx
    add eax, ebx    ; add the values in eax and ebx, result in eax
    mov ebx, 0      ; prepare return code 0
    mov eax, 1      ; syscall number for sys_exit
    int 0x80        ; call kernel
```

these instructions have to be user mode instructions because we are writing a user program, so the compiler will convert these instructions to user mode instructions that can be executed by the CPU in user mode.
but what if we try to write a kernel mode instruction in our c code file like this(in c code you can write assembly code using the asm keyword so when the compiler sees this keyword it knows that you are writing assembly code and will keep it as is without converting it to anything else):
```c
#include <stdio.h>
int main() {
    asm("cli"); // clear interrupt flag (kernel mode instruction)
    asm("hlt"); // halt the CPU (kernel mode instruction)
    asm("sti"); // set interrupt flag (kernel mode instruction)
    return 0;
}
```
after i compile this code using the compiler this will be the assembly code that the compiler will generate:
```assembly
section .text
    global _start
_start:
    cli             ; clear interrupt flag (kernel mode instruction)
    hlt             ; halt the CPU (kernel mode instruction)
    sti             ; set interrupt flag (kernel mode instruction)
    mov ebx, 0      ; prepare return code 0
    mov eax, 1      ; syscall number for sys_exit
    int 0x80        ; call kernel
```
but when the CPU tries to execute these instructions in user mode it will throw a general protection fault because these instructions are kernel mode instructions and cannot be executed in user mode.

we have talked about how can my user program (the c code file) execute kernel mode instructions (the cli, hlt, sti instructions), the answer is through system calls, the user program cannot execute kernel mode instructions directly, but it can request the operating system to execute these instructions on its behalf through system calls.

### compiler
know after we now know that the compiler converts the source code to assembly code, a new question arises, how does the compiler know which assembly instructions to generate?
because different CPU architectures have different assembly instructions(the 86x_64 architecture has different instructions than the ARM architecture) 
```assembly
; x86_64 architecture
mov rax, 5      ; load 5 into register rax  
; ARM architecture
MOV R0, #5      ; load 5 into register R0
```
```
they are both have different syntax and different register names. how could the compiler know which assembly instructions to generate? the answer is through the target architecture.
when you compile a source code file using a compiler, you are using a compiler that is designed to generate assembly instructions for a specific target architecture.

when you have installed this compiler on your machine, you have installed the version of the compiler that is designed to generate assembly instructions for your machine's architecture.

but there are a compilers version that can generate an assembly code for any architecture but you have to tell the compiler which architecture you are using (86x_64, ARM, etc.) so the compiler can generate the correct assembly instructions for that architecture.
these compilers have a flag that you can use to specify the target architecture, for example in gcc compiler you can use the -march flag to specify the target architecture.
also this compiler has a default target architecture that it uses when you don't specify the -march flag, this default target architecture is usually the architecture of the machine that the compiler is running on.
but how does the compiler know the architecture of the machine that it is running on? using the uname -e command.


now what about the functions that i use in my c code file like printf, scanf.
these functions are alrady compiled to assembly instructions (machine code) and stored in a library file (a file that contains pre-compiled functions that can be used by other programs).
when the compiler sees these functions in my c code file, it knows that these functions are already compiled to assembly instructions and stored in a library file, so it does not need to generate assembly instructions for these functions and get link them during the linking phase, but these functions instructions have to be for the same target architecture as the one that the compiler is generating assembly instructions for.
so they are the same and the compiler when you installed a code that already compiled to instructions these instructions have to be for the same target architecture.
when we installed the stdlib for c language we have installed the version of the library that is designed for our machine architecture so when the compiler links these functions during the linking phase it links the correct version of these functions that are designed for our machine architecture.


what about the OS does it effect the assembly instructions that the compiler generates?
the OS does not effect the assembly instructions that the compiler generates because the assembly instructions are specific to
the CPU architecture and not the OS. that's rwong because different OS's have different system calls and different ways of handling system calls.

i new that doesn't make sense because the complier has to generate assembly instructions that are compatible with CPU architecture and this has nothing to do with the OS but there are two things where that compiler have to take the OS into considration system calls and calling conventions. 

### system calls

when we have talked about system calls we have said that different OS's have different system calls and different ways of handling system calls so the compiler has to generate assembly instructions that are compatible with the OS's system calls even if the architecture is the same.
for example the Linux OS and FreeBSD both can run in x86_64 but for a system call to read a file in Linux the compiler has to generate the following assembly instructions:
```assembly
mov eax, 0      ; syscall number for sys_read
mov ebx, fd     ; file descriptor
mov ecx, buf    ; buffer to store the read data
mov edx, count  ; number of bytes to read
int 0x80        ; call kernel
```
but for FreeBSD the compiler has to generate the following assembly instructions:
```assembly
mov eax, 3      ; syscall number for sys_read
mov ebx, fd     ; file descriptor
mov ecx, buf    ; buffer to store the read data
mov edx, count  ; number of bytes to read
int 0x80        ; call kernel
```
as you can see the syscall number for sys_read is different in Linux and FreeBSD so the compiler has to generate different assembly instructions for the same system call in different OS's even if the architecture is the same.

### calling conventions
On the same CPU, different OSes use different calling conventions: calling conventions define how functions receive parameters from the caller and how they return a value. they also define how the stack is managed during function calls.
for example in x86_64 architecture, Linux uses the System V AMD64 ABI calling convention, while Windows on x86_64 uses the Microsoft x64 calling convention.
so the compiler has to generate different assembly instructions for function calls depending on the OS even if the architecture is the same.
for example for a function call in Linux the compiler has to generate the following assembly instructions:
```assembly
mov rdi, arg1   ; first argument
mov rsi, arg2   ; second argument
call function   ; call the function
```
but for Windows the compiler has to generate the following assembly instructions:
```assembly
mov rcx, arg1   ; first argument
mov rdx, arg2   ; second argument
call function   ; call the function
```
as you can see the way the arguments are passed to the function is different in Linux and Windows so the compiler has to generate different assembly instructions for function calls in different OS's even if the architecture is the same.
but here comes the question, if the both generated assembly instructions are for the same architecture (x86_64) that's means that both assembly instructions can be executed by the same CPU, so why do we need different calling conventions for different OS's and what does hppens if we the compiler generated the wrong calling convention(widows calling convention on linux)? nothing. the CPU will execute the instructions without any problem because both assembly instructions are for the same architecture, 