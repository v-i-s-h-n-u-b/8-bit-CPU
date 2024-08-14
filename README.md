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

Instructions are encoded as 8-bit instruction words in a way very similar to that of a MIPS 32-bit ISA but for 8-bit instructions. They can be classfied under three classifications in the ISA. These are Register type (R), Immediate type (Load/Store) (I) and Jump type (Branch instructions) (J).

In this CPU Design the instruction Fields are as follows:
* 4-bit OPCODE - Instruction[7:4}
* 3-bit fields for the source and destination registers (rs, rt and rd), depicted as follows 
  - rs: Instruction [3:1]
  - rt: Instruction [2:0]
  - rd: Instruction [2:0]
* 8 bit Immediate value - Instruction[7:0]

### R-Format instructions

In R-format instructions the data is read from two register operands (rs & rt) .These instructions are used to perform arithmetic and logical operations and write back the result into the register stored in rd.

### Immediate Format 

Register rs is the base register that is added to the 8-bit address field to form the memory address. For loads, rt is the destination register for the loaded value. For stores, rt is the source register whose value should be stored into memory.

### Branch Instructions 

The registers rs and rt are the source registers that are compared for equality. The 8-bit address field is shifted, and added to the PC + 1 to compute the branch target address. (In a typical RISC ISA the 16-bit address field is sign extened, shifted, and added to the PC + 4 to compute the branch target address).

## CPU Overview

### ALU (Arithmetic Logic Unit) :

Performs logical and arithmetic operations. The operations of the ALU are determined by the 3 bit alu_control signal and are as follows:
* 3'b000 : ADD Operation
* 3'b001 : SUBTRACT Operation
* 3'b010 : AND Operation
* 3'b011 : OR Operation
* 3'b100 : XOR Operation
* 3'b101 : Default NO Operation

The 8-bit 'result' stores the result of the operations for further use

### Register Files :

Manages registers for data storage and manipulation.
#### INPUTS:
* Read Register 1 (read_reg1): Register address for the first read operation.
* Read Register 2 (read_reg2): Register address for the second read operation (if applicable).
* Write Register (write_reg): Register address for the write operation.
* Write Data (write_data): Data to be written to the register.
* Write Enable (write_enable): Control signal that enables or disables writing to the register file.
 
#### OUTPUTS:
* Read Data 1 (read_data1): Data read from the register specified by read_reg1.
* Read Data 2 (read_data2): Data read from the register specified by read_reg2.

### Control Unit 

The Control Unit (CU) is a central component of the CPU that generates control signals based on the current instruction and the state of the CPU. It orchestrates the operation of the CPU by directing the activities of other components such as the Register Files, ALU, and Memory. The CU is essential for fetching, decoding, and executing instructions.

#### INPUTS : 4-bit opcode - Specifies the operation to be performed by the CPU.

#### OUTPUTS :
* reg_dst (1-bit): Controls whether the destination register is specified by rd (for R-type instructions) or rt (for I-type instructions like LOAD/STORE).
* alu_src (1-bit): Determines whether the ALU should use an immediate value or a register value as its second operand.
* mem_to_reg (1-bit): Indicates whether the data to be written to the register file comes from memory (for LOAD instructions).
* reg_write (1-bit): Enables writing to the register file.
* mem_read (1-bit): Enables reading from memory.
* mem_write (1-bit): Enables writing to memory.
* branch (1-bit): Indicates whether a branch operation is to be performed (used for branch instructions).
* alu_control (3-bit): Specifies the operation to be performed by the ALU (e.g., addition, subtraction).

### MEMORY

The Memory module simulates a simple 256 x 8-bit memory unit in a CPU. It supports reading from and writing to memory based on the provided control signals. 

#### INPUTS :
* clk: The clock signal that synchronizes memory operations.
* mem_read (1-bit): A control signal that enables reading data from memory when asserted (logic high).
* mem_write (1-bit): A control signal that enables writing data to memory when asserted (logic high).
* address (8-bit): The memory address where the read or write operation is to be performed. With 8 bits, the memory can address 256 unique locations.
* write_data (8-bit): The data to be written to memory when the mem_write signal is asserted.

#### OUTPUTS : read_data (8-bit): The data read from the memory at the specified address when the mem_read signal is asserted.

### 2-bit Branch Predictor 

