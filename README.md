# Pipelined Based RISC-V CPU

[![](https://img.shields.io/badge/-RISC--V-red "RISC-V")](https://riscv.org/) [![](https://img.shields.io/badge/-TL--Verilog-blue "TL-Verilog")](https://tl-x.org/) 

![myth_banner](https://user-images.githubusercontent.com/88897605/170865437-56aa5203-f1dd-448a-8910-33d4f0899ed5.png)

My Project Repository for RISC-V MYTH ( Microprocessor for You in Thirty Hours) workshop, conducted by VSD Corp and Redwood EDA.This workshop was conducted over a period of 5 days and in this short span of time we were able to understand & implement a RISC-V core with base instruction set. Programming language used in the software section was with TL-Verilog for HDL implementation.Tools used: Spike and Makerchip IDE.This repository contains all the information needed to build your RISC-V pipelined core, which has support of base interger RV32I instruction format using TL-Verilog on makerchip platform


# Table of Contents
- [Introduction to RISC-V Instruction Set Architecture](#introduction-to-risc-v-instruction-set-architecture)
- [Why RISC-V?](#why-risc-v)
- [Brief of GNU compiler toolchain](#brief-of-gnu-compiler-toolchain)
- [Introduction to ABI](#introduction-to-abi)
- [Digital Logic with TL-Verilog and Makerchip](#digital-logic-with-tl-verilog-and-makerchip)
  - [Combinational logic](#combinational-logic)
  - [Sequential logic](#sequential-logic)
  - [Pipelined logic](#pipelined-logic)
  - [Validity](#validity)
- [Basic RISC-V CPU micro-architecture](#basic-risc-v-cpu-micro-architecture)
  - [Fetch](#fetch)
  - [Decode](#decode)
  - [Register File Read and Write](#register-file-read-and-write)
  - [Execute](#execute)
  - [Control Logic](#control-logic)
- [Pipelined RISC-V Core](#pipelined-risc-v-core)
  - [Pipelining the Core](#pipelining-the-core)
  - [Load and Store Data](#load-and-store-data)
  - [FINAL RISC-V CPU](#final-risc-v-cpu)
- [Contributor](#contributor) 
- [Acknowledgements](#acknowledgements)


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

# Introduction to ABI
An Application Binary Interface is a set of rules enforced by the Operating System on a specific architecture. So, Linker converts relocatable machine code to absolute machine code via ABI interface specific to the architecture of machine.

![calling_convetion](https://user-images.githubusercontent.com/88897605/170866175-eba932fe-4dce-4a1f-bc7e-5e6eca38fce2.png)

So, it is system call interface used by the application program to access the registers specific to architecture. Overhere the architecture is RISC-V, so to access 32 registers of RISC-V below is the table which shows the calling convention (ABI name) given to registers for the application programmer to use

# Brief of GNU compiler toolchain
The GNU Toolchain is a set of programming tools in Linux systems that programmers can use to make and compile their code to produce a program or library. So, how the machine code which is understandable by processer is explained below

* Preprocessor - Process source code before compilation. Macro definition, file inclusion or any other directive if present then are preprocessed.
* Compiler - Takes the input provided by preprocessor and converts to assembly code.
* Assembler - Takes the input provided by compiler and converts to relocatable machine code.
* Linker - Takes the input provided by Assembler and converts to Absolute machine code.


Under the risc-v toolchain, 
  * To use the risc-v gcc compiler use the below command:

    `riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o <object filename> <C filename>`

    More generic command with different options:

    `riscv64-unknown-elf-gcc <compiler option -O1 ; Ofast> <ABI specifier -lp64; -lp32; -ilp32> <architecture specifier -RV64 ; RV32> -o <object filename> <C      filename>`

    More details on compiler options can be obtained [here](https://www.sifive.com/blog/all-aboard-part-1-compiler-args)

  * To view assembly code use the below command,
    
    `riscv64-unknown-elf-objdump -d <object filename>`
    
  * To use SPIKE simualtor to run risc-v obj file use the below command,
  
    `spike pk <object filename>`
    
    To use SPIKE as debugger
    
    `spike -d pk <object Filename>` with degub command as `until pc 0 <pc of your choice>`




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




# Basic RISC-V CPU micro-architecture 

This section will cover the implementation of a simple 3-stage RISC-V Core / CPU.
### The 3-stages broadly are: 

* Fetch
* Decode
* Execute. 


### Basic block of the CPU/RISC-V core

![riscv_block_diagram](https://user-images.githubusercontent.com/88897605/170861561-0fb3a68c-7d2f-4fd6-ad00-3370fc5af3e9.jpeg)

## Fetch

##### Program Counter 
* (```Instruction Pointer```) is a block which contains the address of the next instruction to be executed.
 
* It is feed to the instruction memory, which in turn gives out the instruction to be executed
* In Short ```Program Counter (PC)```: Holds the address of next Instruction


##### Instruction Memory (IM)
* ```IM``` :Holds the set of instructions to be executed
* The instruction memory gives out a 32-bit instruction depending upon the input address

During Fetch Stage, processor fetches the instruction from the IM pointed by address given by PC

The below Snippet shows the Program Counter and Instruction Fetch Implementation in Makerchip

![fetch](https://user-images.githubusercontent.com/88897605/170862029-7dfb3d17-eaed-4f93-90e0-db4e84c7ea93.png)


## Decode
6 types of Instructions:

 * R-type - Register
 * I-type - Immediate
 * S-type - Store
 * B-type - Branch (Conditional Jump)
 * U-type - Upper Immediate
 * J-type - Jump (Unconditional Jump)
 
Decode Function - The 32-bit fetched instruction has to be decoded first to determine the operation to be performed and the source / destination address. Instruction Type is first identified on the opcode bits of instruction. The instruction type can R, I, S, B, U, J. Every instruction has a fixed format defined in the RISC-V ISA. Depending on the formats, the following fields are determined:

* ```opcode``` funct3, funct7 -> Specifies the Operation
* ```imm``` -> Immediate values / Offsets
* ```rs1```,```rs2``` -> Source register index
* ```rd``` -> Destination register index

The Below snippet shows the Decode in Makerchip
![decode](https://user-images.githubusercontent.com/88897605/170862239-b991fd07-e9ad-4c93-9fcc-3e98521b2dbc.png)


## Register file Read and Write

Here the Register file is 2 read, 1 write means 2 read and 1 write operation can happen simultanously.

Inputs:

Read_Enable - Enable signal to perform read operation
Read_Address1 - Address1 from where data has to be read
Read_Address2 - Address2 from where data has to be read
Write_Enable - Enable signal to perform write operation
Write_Address - Address where data has to be written
Write_Data - Data to be written at Write_Address
Outputs:

Read_Data1 - Data from Read_Address1
Read_Data2 - Data from Read_Address2

The Below Snippet shows the Write and Read Register Implementation in Makerchip.

### Read

The Below Snippet shows the Read Register Implementation in Makerchip. 

![Read](https://user-images.githubusercontent.com/88897605/170862394-43614149-e49d-405e-be2f-e3255b1e489a.png)

### Write

The Below Snippet shows the Write Register Implementation in Makerchip.

![write](https://user-images.githubusercontent.com/88897605/170862405-e8acd752-7be2-4d74-a2e5-e0f81d665c18.png)

# Execute

* Operation- During the Execute Stage, both the operands perform the operation based on Opcode

Below is Snippet shows Excute stage in Makerchip.

![excute](https://user-images.githubusercontent.com/88897605/170862589-020c4532-77cc-4761-809b-8db593f060d1.png)

# Control logic

* Operation- During Decode Stage,branch target address is calculated and fed into PC mux. Before Execute Stage, once the operands are ready branch condition is checked.

Below is Snippet shows Control logic stage in Makerchip.

![control_logic](https://user-images.githubusercontent.com/88897605/170862720-abfb3e8d-d69d-4f6b-abfc-262da9aae44a.png)


# Pipelined RISC-V Core

* Pipelining processes increases the overall performance of the system. Thus, the previously designed cores can be pipelined. 
* The "Timing Abstraction" feature of TL-Verilog makes it easy.
* Converting non-piepleined CPU to pipelined CPU using timing abstract feature of TL-Verilog. This allows easy retiming wihtout any risk of funcational bugs. 
* More details reagrding Timing Abstract in TL-Verilog can be found in IEEE Paper ["Timing-Abstract Circuit Design in Transaction-Level Verilog" by Steven Hoover.](https://ieeexplore.ieee.org/document/8119264)

## Pipelining the Core

* Pipelining the CPU with branches still having 3 cycle delay rest all instructions are pipelined

Pipelining the CPU in TL-Verilog can be done in following manner:
```
|<pipe_name>
@<pipe_stage>
   Instructions present in this stage
@<pipe_stage>
   Instructions present in this stage
```
There are various hazards to be taken into consideration while implementing a pipelined design. Some of hazards taken under consideration are:

* Improper Updating of Program Counter (PC)
* Read-before-Write Hazard

Below is Snippet of pipelined CPU with a test case of assembly program which does summation from 1 to 9 then stores to r10 of register file

In Snippet r10 = 45 Test case: ```*passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9);```

![piplining_cpu](https://user-images.githubusercontent.com/88897605/170863136-854575e8-bf0d-4dfa-beed-914eb8d62e71.png)

# Load and Store Data

Similar to branch, load will also have 3 cycle delay. So, added a Data Memory 1 write/read memory.

Inputs:

* Read_Enable - Enable signal to perform read operation
* Write_Enable - Enable signal to perform write operation
* Address - Address specified whether to read/write from
* Write_Data - Data to be written on Address (Store Instruction)
Output:

 * Read_Data - Data to be read from Address (Load Instruction)
 
Added test case to check fucntionality of load/store. Stored the summation of 1 to 9 on address 4 of Data Memory and loaded that value from Data Memory to r17.



* A Data memory can be added to the Core. The Load-Store operations will add up a new stage to the core.Thus,making it now a 4-Stage Core/CPU.

The proper functioning of the RISC-V core can be ensured by introducing some testcases to the code.
* For example, if program for summation of positive integers from 1 to 9 and storing it to specific register can be verified by:

   * ```*passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9);``` 

Below is Snippet from Makerchip IDE after including load/store instructions in Makerchip

![load$store](https://user-images.githubusercontent.com/88897605/170863238-7f5f164b-11f1-4712-be84-b9e12d56d9e0.png)

# FINAL RISC-V CPU

* After pipelining is proved in simulations, the operations for Jump Instructions are added. 
* Also, added Instruction Decode and ALU Implementation for RV32I Base Integer Instruction Set.

The Snippet below shows the successful implementation of 4-stage RISC-V Core

![final_cpu](https://user-images.githubusercontent.com/88897605/170863583-6a18307d-570c-4be4-bb6e-aecd1cc10305.png)


# Contributor
Aakash K</br>
Contact:iaakashkrish@gmail.com</br>

# Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd - kunalpghosh@gmail.com
- [Steve Hoover](https://github.com/stevehoover), Founder, Redwood EDA - steve.hoover@redwoodeda.com
- [Shivam Potdar](https://github.com/shivampotdar), GSoC 2020 @fossi-foundation



