# RISC-V based CPU

[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-c66648af7eb3fe8bc4f294546bfd86ef473780cde1dea487d3c4ff354943c9ae.svg)](https://classroom.github.com/online_ide?assignment_repo_id=7935482&assignment_repo_type=AssignmentRepo)

[![](https://img.shields.io/badge/-RISC--V-red "RISC-V")](https://riscv.org/) [![](https://img.shields.io/badge/-TL--Verilog-blue "TL-Verilog")](https://tl-x.org/) 


This repository contains all the information needed to build your RISC-V pipelined core, which has support of base interger RV32I instruction format using TL-Verilog on makerchip platform

# Introduction to RISC-V Instruction set architecture
A RISC-V ISA is defined as a base integer ISA, which must be present in any implementation, plus optional extensions to the base ISA. Each base integer instruction set is characterized by

* Width of the integer registers (XLEN)
* Corresponding size of the address space
* Number of integer registers (32 in RISC-V)

More details on RISC-V ISA can be obtained [Link](https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf)

# Why RISC-V?
* Far simple and smaller than commercial ISAs.
* Avoids micro-architecture or technology dependent features.
* Small standard base ISA.
* Multiple Standard Extensions.
* Variable-length instruction encoding

# Brief of GNU compiler toolchain
The GNU Toolchain is a set of programming tools in Linux systems that programmers can use to make and compile their code to produce a program or library. So, how the machine code which is understandable by processer is explained below

* Preprocessor - Process source code before compilation. Macro definition, file inclusion or any other directive if present then are preprocessed.
* Compiler - Takes the input provided by preprocessor and converts to assembly code.
* Assembler - Takes the input provided by compiler and converts to relocatable machine code.
* Linker - Takes the input provided by Assembler and converts to Absolute machine code.

## Using RISC-V Complier 

* To use the risc-v gcc compiler use the below command:
```
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o <object filename> <C filename>
```

More generic command with different options:
```
riscv64-unknown-elf-gcc <compiler option -O1 ; Ofast> <ABI specifier -lp64; -lp32; -ilp32> <architecture specifier -RV64 ; RV32> -o <object filename> <C filename>
```
* compiler options : O1, Ofast
* ABI options : lp64, lp32
* Architecture options: RV64, RV32

More details on compiler options can be obtained [Link](https://www.sifive.com/blog/all-aboard-part-1-compiler-args)

#### To view assembly code use the below command,
```
riscv64-unknown-elf-objdump -d <object filename>
```
#### Simulating the object file using SPIKE simulator
```
spike pk <object filename>
```
#### Debugging the object file using SPIKE

```
spike -d pk <object Filename>
```
#### Degub command as 
```
until pc 0 <pc of your choice>
```



To install complete risc-v toolchain locally on linux machine
[Link](https://github.com/kunalg123/riscv_workshop_collaterals/blob/master/run.sh)
[Link](http://hdlexpress.com/RisKy1/How2/toolchain/toolchain.html)

Below images show the toolchain implementation for a small c program for sum of first 9 positive integers.

1.RISC-V Toolchain: Compilation, Simulation and Debugging

#### Test Case for the above commands

![sum1ton11](https://user-images.githubusercontent.com/88897605/170711445-906a8c49-43d8-4969-b20a-e3be6b7cfd55.png)

To view the registers we can use command as ```reg <core> <register name>.```

Below image shows how to debug the disassembled file using Spike simulator where a1,a2 register are checked before and after the instructions got executed
![cat_sum1ton](https://user-images.githubusercontent.com/88897605/170712081-2fbb779e-2727-4b08-957d-739c1b6c5d16.png)


# Digital Logic with TL-Verilog and Makerchip
Makerchip is a free online environment for developing high-quality integrated circuits. You can code, compile, simulate, and debug Verilog designs, all from your browser. Your code, block diagrams, and waveforms are tightly integrated.
The Application Binary Interface is the sum total of what the application programmer needs to understand in order to write programs; the programmer does not have to understand or know what is going on within the Application Execution Environment.

An Application Binary Interface would combine the processor ISA along with the OS system-call interface. The below snippet gives the list of registers, thier short description and ABI name of every register in RISC-V ISA.


# Combinational Logic
Starting with basic example in combinational logic is an inverter. To write the logic of inverter using TL-verilog is 
```
$out = ! $in;
```

* There is no need to declare
```
$out and $in unlike Verilog
```

There is also no need to assign ``` $in``` Random stimulus is provided, and a warning is produced.

![IMG_20220527_194822](https://user-images.githubusercontent.com/88897605/170717943-aff0f3e1-6ed0-4be8-8fed-c348f405edc1.jpg)


# Sequential logic
Starting with basic example in sequential logic is Fibonacci Series with reset. To write the logic of Series using TL-Verilog is ``` $num[31:0] = $reset ? 1 : (>>1$num + >>2$num).``` This operator >>? is ahead of operator which will provide the value of that signal 1 cycle before and 2 cycle before respectively.

 Sequential Calculator Shown Below
 ![srq](https://user-images.githubusercontent.com/88897605/170718723-5e6c51c9-63d7-40fd-920f-279f2ea6ff9c.jpg)

# Pipelined Logic 
Timing abstract powerful feature of TL-Verilog which converts a code into pipeline stages easily
* This is a place where TLV Shines bright. All us Verilog users have faced the endless pains of pipelining and fails. In both the calculator and the cpu core we have used pipelines
* Defined with a '|' symbol
* Within the Pipeline, multiple stages can be defined using '@'
* This concept is called Time Abstraction

* 2-cycle calculator which clears the output alternatively and output of given inputs are observed at the next cycle.
![21](https://user-images.githubusercontent.com/88897605/170719820-9a484354-db42-45c2-ad08-e09d9b767275.jpg)

## Validity

* NOTE: This functionality is not a part of other circuit design languages and is exclusive to TLV
* This will make no difference on the nature of the code to be honest. In usual processes, these would all be not care
* It basically decides when a signal has significance, so it is only executed then.
* This keeps the Waveform clean and easy to debug
* You will see this operator being used '?' throughout the CPU Code.

![21_v](https://user-images.githubusercontent.com/88897605/170719922-4f8432ad-6024-48d3-a45b-2359a305acc2.jpg)

