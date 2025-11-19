### what is a process?
to know what is a process, we first need to understand what a program is.
a program is a set of instructions that a computer can execute that exists on disk. where does these instructions come form? they come from a source code that is written by a programmer in a high-level programming language like C, C++, Java, Python, etc. this source code is then compiled or interpreted into machine code (binary code) that the computer can understand.(the machine code is the program that will get executed by the CPU).

a process is an instance of a program that is currently being executed by the computer.

### what is a CPU?
a CPU (central processing unit) is the brain of the computer. it is responsible for executing instructions from programs. the CPU performs basic arithmetic, logic, control, and input/output (I/O) operations specified by the instructions in the program.

the CPU consists of several components: ram, registers, and the control unit, program counter, instruction register, and arithmetic logic unit (ALU).
1. ram (random access memory): this is the main memory of the computer where data and instructions are stored temporarily while the CPU is executing a program.(ram is not inside the cpu but the ram is connected to the cpu).
2. registers: these are small, high-speed storage locations within the CPU that hold data and instructions that are being processed.
3. control unit: this component directs the operation of the CPU and coordinates the execution of instructions(looks inside the instruction register sees the bits(001001010) and know what to do).
4. program counter: this register keeps track of the address of the next instruction to be executed.
5. instruction register: this register holds the current instruction being executed.
6. arithmetic logic unit (ALU): this component performs arithmetic and logical operations on the data.

first of all the OS loads the program from the disk into the ram. then the CPU fetches the instruction from the ram using the program counter register, and put it inside the instruction register. the control unit decodes the instruction and tells the ALU what operation to perform. the ALU performs the operation and stores the result back in the ram or in a register. this process continues until the program is finished executing.
MOV R1, 5     ; 
MOV R2, 3     ; 
ADD R3, R1, R2;
let's say we have the following assembly instructions in the ram:
first the CPU fetches the first instruction "MOV R1, 5" from the ram and puts it inside the instruction register. the control unit decodes the instruction and tells the ALU to move the value 5 into register R1. then the CPU fetches the second instruction "MOV R2, 3" and puts it inside the instruction register. the control unit decodes the instruction and tells the ALU to move the value 3 into register R2. finally, the CPU fetches the third instruction "ADD R3, R1, R2" and puts it inside the instruction register. the control unit decodes the instruction and tells the ALU to add the values in registers R1 and R2 and store the result in register R3.

### RAM
In your explanation, you mentioned that instructions move from Disk → RAM → CPU. That is correct, but a process needs RAM for more than just holding the code.
in the ram we store:Code, data.
inside the ram, a process is divided into several segments:
1. text segment: this segment contains the executable code of the program.
2. data segment: this segment contains global and static variables that are initialized by the programmer.
3. bss segment: this segment contains global and static variables that are uninitialized by the programmer.
4. heap segment: this segment is used for dynamic memory allocation during the execution of the program.
5. stack segment: this segment is used for function calls and local variables.

### kernel vs user mode
* in a CPU there are two modes of operation: kernel mode and user mode. if the cpu is running in kernel mode it has full access to all the resources of the computer including hardware and memory. (the process get extra access to special cpu instructions that are not available in user mode).
* the ram in system is also divided into kernel space and user space. the kernel space is reserved for the operating system and its components, while the user space is where user processes run.

when a process is running in a user mode this process has a memory space that is isolated from other processes (it's own stack and heap). and it's own fd's and resources. 
but who creates and gives these processes these resources and memory space ?

all that things the kernel does. the kernel is the code that get executed in the kernel mode, and the first thing that get executed when the computer boots up is the kernel code.
A Process is a program in execution that runs sequentially (line 1, line 2...) and can be paused/stopped by the Scheduler.
The Kernel is the Scheduler itself. It is the code that runs between processes.

what is happening first is a kernel code get loaded into memory and this code creates the first user process (init process) and this init process creates other user processes.
each time a process created it needs memory space and resources so the kernel allocates these things to the new process.

also if a user process needs to do some operation that requires higher privileges (like accessing hardware or memory) it makes a system call to the kernel and the kernel code start executing in kernel mode and does the operation on behalf of the user process.

if user process tryed to access memory that it is not allowed to access (like kernel space or other process space) the cpu will raise a fault (segmentation fault) and the kernel code will start executing in kernel mode and handle the fault (usually by terminating the process).

if a user process is running and the scheduler wants to switch to another process, the kernel code starts executing in kernel mode and saves the state of the current process (like the values of registers, program counter, etc.) and loads the state of the new process and switches to user mode to run the new process.

in the kernel code it tracks all the processes in a data structure called process table. this table contains information about each process like its state (running, waiting, etc.), memory space, resources, etc.

so the kernel is the code that manages all the processes (creating, deleting, scheduling, allocating resources, and keeping track of their states). and the kernel code runs in kernel mode with full access to all the resources of the computer so it can talk to hardware (like disk, network, etc.).

kernel summary:
* first code that get executed when the computer boots up.
* creates processes and allocates resources to them.
* run in kernel mode with full access to all resources.
* manages all processes (creating, deleting, scheduling, allocating resources, and keeping track of their states).
* handles system calls from user processes.
* handles faults (like segmentation fault) from user processes.
* handles interrupts from hardware devices.
