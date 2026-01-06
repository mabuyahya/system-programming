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