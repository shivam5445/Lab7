# Lab7
Description:
The Pipeline Datapath works in the following order of steps:
1. Instruction Fetch (IF)
2. Instruction Decode (ID)
3. Execution (EX)
4. Memory Read/Write (MEM)
5. Write Back (WB)

We have used no-op conditions to avoid data hazards such as RAW (Read After Write) 
We have based the pipeline on Harvard Architecture.

Instruction Fetch (IF):
We fetch the instruction from the instruction cache, and increment the program counter.
We have two units:
1. Program Counter - Progresses through the instructions
a) In our code, we have implemented it as an integer ‘address’ which increments by 1 after IF 
Stage is finished.
2. Instruction Cache - Stores the Instructions to be executed.
a) In our code, the 8x32 bit cache file ‘icache’ is the instruction file, and each instruction is 
accessed with address as the index.

Instruction Decode (ID):
We decode the instuctions, fetch the values stored in the register file and load them onto the 
necessary registers.
We have two units:
1. Pipeline Register 1 - Stores the instruction
a) The pipeline register is named as ‘preg1’.
2. Decoder - Decodes the instructions
a) The instruction fetched from icache as ‘ins’ is decoded into opcode, func7, func3, rs1, rs2, 
imm, rd.
3. Register File – Stores all the registers
a) It is stored with a 32x32 register file as ‘registers’
1. Inputs – rs1, rs2, rd, wdata
2. Outputs – read_data1, read_data2
4. Immediate Generator -
a) Given the decoded imm, it forwards the immediate value

Execution (EX):
We run the ALU.
We have two units.
1. Pipeline register – Stores required inputs to the ALU and rd for next stage.
a) In our code, it is implemented using ‘preg2’
2. ALU – It performs arithmetic operations
a) Inputs – read_data1, read_data2, s
b) Outputs – z/preg3
The s = func7 | func3 | opcode (as specified in the ALU designed in Lab 5) 
Based on value of s and opcode, ALU performs the following operations:
• If it is a load/store operation – we simply add the immediate (offset) to the base value 
and pass it on
• If it is an I-type/R-type operation – we have a mux choose between read_data2 or imm 
based on the opcode and based on s, we perform addition/subtraction/multiplication. 
• For Addition, we have used Carry Look Ahead Adder;
• For Subtraction, we have followed 2s complement system;
• For Multiplication, we have followed the Booth’s Algorithm.

Memory Read/Write (MEM):
We read/write data into the data cache.
We have two units:
1. Pipeline Register – stores inputs to the data cache.
a) In our code, we have used reg ‘preg3’ as pipeline register.
2. Data Cache – temporarilary stores data for the operations
a) inputs – output of the alu that is preg3[31:0]
data stored in cache according to the given destination registor.

Write Back (WB):
Depending on whether the instruction is store or not, it writes back to the register file 
We have two units:
1. Pipeline Register – stores outputs of Data Cache
a) In our code, we have used reg ‘preg4’ as pipeline register.
2. Mux – decides whether to store the value or not
a) If the opcode is that of store, we store the value ‘read_data3’ read from the data cache 
into the register-file.
b) Else, we store back the same value as before.
