# compiler
everything is a file in the system, and the file can store data or code (just text), number, or binary(only 0s and 1s).
these files can perform different purposes, like the binary files stores a instructions that can be executed by the CPU and the text files store data (that you want to store) or code (that you want to execute).

a programming language is a set of rules and syntax to write code (text files) so the compiler can understand it and convert it to binary files (machine code) that can be executed by the CPU.

a compiler is a binary file that get loaded to the memory and executed by the CPU, it reads the code (text file) that you wrote in a programming language, analyze it, and convert it to machine code (binary file) that can be executed by the CPU.

after you compiled the rust text file using the compiler, now you give the CPU the binary file to execute it.

you can't compile a rust text file using the compiler.

gcc in a c compiler.
gcc goes through several stages to convert the c code to machine code. 
1. preprocessing: in this stage, the compiler removes comments and whitespace from the code, and handles directives (like #include in C/C++).
2. compilation: in this stage, the compiler translates the preprocessed code into assembly code (low-level code that is specific to a particular CPU architecture).
3. assembly: in this stage, the assembler converts the assembly code into machine code (binary code).
4. linking: in this stage, the linker combines the machine code with any necessary libraries and creates the final executable binary file.

main.c (c language)--> main.i (after removing the comment)--> main.s (assembly code)--> main.o (machine code) --> a.out (executable binary file)

the (0's and 1's) file is prodused in after the assembly phase so why do i need the linking phase ? the linking phase is used to combine multiple (1's and 0's) files into a single executable file.

# interpreter
an interpreter is binary file (1's and 0's) that get loaded to the memory and executed by the CPU (just like the compiler), and reads the code (text file) that you wrote in a programming language(here comes the difference), the compiler takes the whole code and tokenize it then convert it to machine code but the interpreter reads the code line by line, tokenize each line, and execute it directly without converting it to machine code.

the compiler take your text file and convert it to machine code (binary file) that can be executed by the CPU, but the interpreter take your text file and read it line by line and the interpreter won't preduced a binary code like the compiler but enested of that he will excute that line(will call the binary function that already reaten that do what does what the same line has to do).

python is an interpreted language, so when you run a python script, the python interpreter reads the script line by line, tokenizes each line, and executes it directly.
for example, when you run a python script that adds two numbers (2 + 3), the interpreter reads the line, tokenizes it into the numbers 2 and 3 and the operator +, and then goes to the "big switch" (the part of the interpreter that handles different operations) to find the function that handles addition, and then calls that function to perform the addition and return the result (5). that function was already written in c and compiled to machine code, so the interpreter just calls that function to do the addition.

examples of interpreted languages:
- node is a javascript interpreter, the node interpreter is just a c++ code that get compiled to machine code, so when you run a javascript file using node, the node interpreter reads the javascript code line by line, tokenizes each line, and executes it directly by calling the pre-written functions that handle different operations(these functions are written in c++ and compiled to machine code).
- python is a python interpreter, the python interpreter is just a c code that get compiled to machine code, so when you run a python file using python interpreter, the python interpreter reads the python code line by line, tokenizes each line, and executes it directly by calling the pre-written functions that handle different operations(these functions are written in c and compiled to machine code).
