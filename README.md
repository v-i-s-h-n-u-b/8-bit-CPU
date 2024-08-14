# Simple-8-bit-CPU

This project showcases a simplified 8-bit CPU design inspired by the RISC-V architecture. The design includes:

* Arithmetic Logic Unit (ALU): Performs arithmetic and logical operations.
* Register Files: Manages registers for data storage and manipulation.
* Control Unit: Directs the operation of the CPU by interpreting instructions and controlling data flow.
* Memory Registers: Handles the storage and retrieval of data and instructions.

The CPU incorporates a pipelined architecture with the following stages:
 
* Instruction Fetch (IF): Retrieves instructions from memory.
* Instruction Decode (ID): Decodes the fetched instruction to determine the required operation.
* Execute (EX): Performs the operation specified by the instruction.
* Memory Access (MEM): Manages data read/write operations to memory.
* Write Back (WB): Updates registers with results from the execution stage.

Additional features include:

* 2-bit Branch Predictor: Enhances performance by predicting the outcome of branch instructions.
* Interrupt Controller: Manages interrupts to handle asynchronous events and enhance CPU responsiveness.

## INTRODUCTION 

Microprocessors and Microcontrollers are generally designed in the vicinity of two main computer architectures RISC and CISC respectively which are two fundamental types of processor architectures that define how a CPU executes instructions. RISC architecture focuses on a simplified set of instructions that can be executed quickly, often in a single clock cycle. This design allows for efficient pipelining, where multiple instructions are processed simultaneously, enhancing performance. RISC processors typically use a load/store model, where operations are performed on data stored in registers rather than directly from memory. RISC processors typically have load store architecture. This denotes there are two instructions for accessing memory which are a load instruction set to load data from the memory and store instruction set to Write Back (WB) the data into memory without any instructions. CISC architecture, on the other hand, includes a more complex set of instructions, some of which can perform multiple low-level operations (such as loading from memory, performing an arithmetic operation, and storing the result) in a single instruction. This approach aims to reduce the number of instructions per program, even if individual instructions take longer to execute. RISC's simplicity and efficiency make it ideal for applications requiring high performance and low power consumption, such as mobile devices, while CISC's complexity and versatility are suited for tasks that benefit from more comprehensive instruction sets, such as desktop computing.

## Instruction Set Architecture (ISA)

Instructions are encoded as 8-bit instruction words in a way very similar to that of a MIPS 32-bit ISA but for 8-bit instructions. They can be classfied under three classifications in the ISA. These are Register type (R), Immediate type (I) and Jump type (J).

In this CPU Design the instruction encodings are as follows:
* 3-bit OPCODE - Instruction[7:4}
* 2-bit fields for the source and destination registers (rs, rt and rd), depicted as follows 
  - rs: Instruction [3:1]
  - rt: Instruction [2:0]
  - rd: Instruction [2:0]
 
