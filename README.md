# Simple-8-bit-CPU

## INTRODUCTION 

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

## Instruction Set Architecture (ISA)

Instructions are encoded as 8-bit instruction words in a way very similar to that of a MIPS 32-bit ISA but for 8-bit instructions. The instructions in this ISA are organized into three categories based on their format and functionality: Register (R) format, Load/Store format, and Branch format instructions.

In this CPU Design the instruction Fields are as follows:
* 4-bit OPCODE - Instruction[7:4}
* 3-bit fields for the source and destination registers (rs, rt and rd), depicted as follows :
  - rs: Instruction [3:1]
  - rt: Instruction [2:0]
  - rd: Instruction [2:0]
* 8 bit Immediate value - Instruction[7:0]

### R-Format instructions

In R-format instructions the data is read from two register operands (rs & rt) .These instructions are used to perform arithmetic and logical operations and write back the result into the register stored in rd.

### Load/Store Instructions

Register rs is the base register that is added to the 8-bit address field to form the memory address. For loads, rt is the destination register for the loaded value. For stores, rt is the source register whose value should be stored into memory.

### Branch Instructions 

The registers rs and rt are the source registers that are compared for equality. The 8-bit address field is shifted, and added to the PC + 1 to compute the branch target address. (In a typical RISC ISA the 16-bit address field is sign extened, shifted, and added to the PC + 4 to compute the branch target address).

## CPU OVERVIEW

### 1. ALU (Arithmetic Logic Unit) :

Performs logical and arithmetic operations. The operations of the ALU are determined by the 3 bit alu_control signal and are as follows:
* 3'b000 : ADD Operation
* 3'b001 : SUBTRACT Operation
* 3'b010 : AND Operation
* 3'b011 : OR Operation
* 3'b100 : XOR Operation
* 3'b101 : Default NO Operation

The 8-bit 'result' stores the result of the operations for further use.

### 2. REGISTER FILES :

Manages registers for data storage and manipulation.
#### Inputs:
* Read Register 1 (read_reg1): Register address for the first read operation.
* Read Register 2 (read_reg2): Register address for the second read operation (if applicable).
* Write Register (write_reg): Register address for the write operation.
* Write Data (write_data): Data to be written to the register.
* Write Enable (write_enable): Control signal that enables or disables writing to the register file.
 
#### Outputs:
* Read Data 1 (read_data1): Data read from the register specified by read_reg1.
* Read Data 2 (read_data2): Data read from the register specified by read_reg2.

### 3. CONTROL UNIT

The Control Unit (CU) is a central component of the CPU that generates control signals based on the current instruction and the state of the CPU. It orchestrates the operation of the CPU by directing the activities of other components such as the Register Files, ALU, and Memory. The CU is essential for fetching, decoding, and executing instructions.

#### Inputs : 
* 4-bit opcode - Specifies the operation to be performed by the CPU.

#### Outputs :
* reg_dst (1-bit): Controls whether the destination register is specified by rd (for R-type instructions) or rt (for I-type instructions like LOAD/STORE).
* alu_src (1-bit): Determines whether the ALU should use an immediate value or a register value as its second operand.
* mem_to_reg (1-bit): Indicates whether the data to be written to the register file comes from memory (for LOAD instructions).
* reg_write (1-bit): Enables writing to the register file.
* mem_read (1-bit): Enables reading from memory.
* mem_write (1-bit): Enables writing to memory.
* branch (1-bit): Indicates whether a branch operation is to be performed (used for branch instructions).
* alu_control (3-bit): Specifies the operation to be performed by the ALU (e.g., addition, subtraction).

### 4. MEMORY 

The Memory module simulates a simple 256 x 8-bit memory unit in a CPU. It supports reading from and writing to memory based on the provided control signals. 

#### Inputs :
* clk: The clock signal that synchronizes memory operations.
* mem_read (1-bit): A control signal that enables reading data from memory when asserted (logic high).
* mem_write (1-bit): A control signal that enables writing data to memory when asserted (logic high).
* address (8-bit): The memory address where the read or write operation is to be performed. With 8 bits, the memory can address 256 unique locations.
* write_data (8-bit): The data to be written to memory when the mem_write signal is asserted.

#### Outputs : 
* read_data (8-bit): The data read from the memory at the specified address when the mem_read signal is asserted.

### 5. 2-BIT BRANCH PREDICTOR

![Screenshot 2024-08-15 013342](https://github.com/user-attachments/assets/70855018-b886-44b1-a438-0e79290fd010)

#### 2 bit Finite State Machine 

The branch predictor uses a 2-bit state machine (reg [1:0] state) to track the history of branch outcomes. The states represent different levels of confidence in the branch being taken or not taken which can be seen from the diagram above. The states are as follows:

* STRONGLY_NOT_TAKEN (00): The predictor is very confident that the branch will not be taken.
* WEAKLY_NOT_TAKEN (01): The predictor is less confident, but still leans towards the branch not being taken.
* WEAKLY_TAKEN (10): The predictor is less confident, but leans towards the branch being taken.
* STRONGLY_TAKEN (11): The predictor is very confident that the branch will be taken.

#### State Transititions 

The state transitions are governed by the actual outcome of the branch (branch_result). Based on the current state and the branch_result, the predictor moves between states. Initially the instruction is predicted to be not taken (STRONGLY NOT TAKEN) and then moves accordingly as seen in the diagram. If the predictor is in a strongly or weakly "not taken" state and the branch is taken, it transitions towards the "taken" states. Conversely, if the predictor is in a strongly or weakly "taken" state and the branch is not taken, it transitions towards the "not taken" states.

#### Inputs :
* clk: The clock signal that drives the predictor's state updates.
* reset: A signal to reset the predictor to its initial state.
* branch_result (1-bit): The actual outcome of the branch instruction. It is 1 if the branch was taken and 0 if it was not.

#### Outputs :
* prediction (1-bit): The predicted outcome of the branch. It is 1 if the branch is predicted to be taken and 0 if it is predicted not to be taken.

### 6. INTERRUPT CONTROLLER

The Interrupt Controller module manages interrupt requests (irq) in a CPU, determining when an interrupt is pending and when it has been acknowledged. This module is crucial for handling asynchronous events that require the CPU's immediate attention, such as hardware interrupts.

#### Inputs :
* clk: The clock signal that drives the state updates in the interrupt controller.
* reset: A signal to reset the interrupt controller to its initial state.
* irq (8-bit): An 8-bit interrupt request signal, where each bit represents a different interrupt source.

#### Outputs:
* irq_pending (1-bit): A flag that indicates if any interrupt is currently pending.
* irq_ack (1-bit): A flag that acknowledges the receipt and handling of a pending interrupt.

#### Functioning :
The module monitors the irq input to determine if any interrupt request is active. If irq is non-zero, it sets the irq_pending flag, indicating that an interrupt is pending and needs to be handled by the CPU. The irq_ack signal is asserted (set to 1) when an interrupt is pending (irq_pending = 1) and has not yet been acknowledged. This signal informs the CPU that an interrupt has been detected and is in the process of being handled. Once the interrupt has been acknowledged (irq_ack = 1), the irq_pending flag is cleared, and the irq_ack flag is reset to 0 after the interrupt has been fully processed.

### 7. PIPELINE STAGES :

Five stages : IF (Instruction Fetch), ID (Instruction Decode), EX (Execute), MEM (Memory), WB (Write Back)

* IF: The CPU fetches the instruction from memory using the Program Counter (PC). If a branch is predicted taken, the PC is updated to the branch address. Otherwise, it increments to the next instruction.
* ID: The instruction is decoded, and the required registers are read from the Register File. Control signals are generated based on the opcode.
* EX: The ALU performs the required operation, using either an immediate value or the second register's data as the operand. The result is forwarded to the Memory stage.
* MEM: Based on control signals, the CPU either reads data from or writes data to memory. The result is forwarded to the Write Back stage.
* WB: The result from the MEM stage is written back to the Register File, updating the appropriate register.

If an interrupt request is detected, the current PC is saved, and the CPU jumps to the interrupt service routine. After handling the interrupt, the CPU resumes normal execution from the saved PC.

## RESULTS AND DISCUSSIONS 

Successfully implemented and verified all the key components and the 5 pipeline stages in verilog.

## PROBLEMS FACED 

When the design was run on Xilinx ISE/Vivado, the main CPU module encountered a few parameter instantiation errors. Once these issues are resolved, the updated CPU module code will be uploaded.

## FUTURE WORK 

To implement operand forwarding, sign extensions, stalls to handle hazards and realise Instruction level Parallelism (ILP).

