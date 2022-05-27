# RISC-V based CPU
This repository contains all the information needed to build your RISC-V pipelined core, which has support of base interger RV32I instruction format using TL-Verilog on makerchip platform

# Introduction to RISC-V Instruction set architecture
A RISC-V ISA is defined as a base integer ISA, which must be present in any implementation, plus optional extensions to the base ISA. Each base integer instruction set is characterized by

* Width of the integer registers (XLEN)
* Corresponding size of the address space
* Number of integer registers (32 in RISC-V)
