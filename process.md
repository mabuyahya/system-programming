### what is a process?
to know what is a process, we first need to understand what a program is.
a program is a set of instructions that a computer can execute that exists on disk. where does these instructions come form? they come from a source code that is written by a programmer in a high-level programming language like C, C++, Java, Python, etc. this source code is then compiled or interpreted into machine code (binary code) that the computer can understand.(the machine code is the program that will get executed by the CPU).

a process is an instance of a program that is currently being executed by the computer.

### what is a CPU?
a CPU (central processing unit) is the brain of the computer. it is responsible for executing instructions from programs. the CPU performs basic arithmetic, logic, control, and input/output (I/O) operations specified by the instructions in the program.

the CPU consists of several components: ram, registers, and the control unit, program counter, instruction register, and arithmetic logic unit (ALU).
1. ram (random access memory): this is the main memory of the computer where data and instructions are stored temporarily while the CPU is executing a program.
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