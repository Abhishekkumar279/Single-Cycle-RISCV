
# Single-Cycle RISC-V Processor (Verilog)

## Overview

This project implements a 32-bit Single-Cycle RISC-V Processor using Verilog HDL. The processor follows a modular hardware architecture in which instruction fetch, instruction decoding, execution, memory access, and register write-back are organized within a single clock cycle.

The design integrates a program counter, instruction memory, register file, control unit, immediate generator, arithmetic logic unit (ALU), data memory, and multiplexers to implement the processor datapath.

The project focuses on understanding RISC-V processor architecture, RTL design, instruction decoding, datapath integration, and hardware simulation.

## Architecture

The processor is organized into the following major stages:

1. **Instruction Fetch:** The Program Counter (PC) provides the instruction address. The instruction memory retrieves the corresponding 32-bit instruction, while the PC adder generates the next sequential address (PC + 4).

2. **Instruction Decode:** The control unit decodes the opcode and function fields to generate the control signals required for instruction execution.

3. **Register Read and Immediate Generation:** The register file provides two source operands. The immediate generation unit extracts and sign-extends the immediate value from the instruction.

4. **Execution:** The ALU performs arithmetic and logical operations based on the decoded ALU control signals. A multiplexer selects either the second register operand or the immediate value as the ALU input.

5. **Memory Access:** The data memory supports memory read and write operations. The ALU result is used as the memory address.

6. **Write Back:** A result multiplexer selects between the ALU output and memory read data before writing the result into the destination register.

## Supported Instruction Operations

The current control and ALU decoding logic includes support for the following instruction operations:

| Instruction | Operation |
|---|---|
| ADD | Addition |
| SUB | Subtraction |
| AND | Bitwise AND |
| OR | Bitwise OR |
| SLT | Set Less Than |
| LW | Load Word |
| SW | Store Word |

The branch opcode is recognized by the main decoder, but complete branch execution and PC redirection are not implemented in the current top-level datapath.

## Project Structure

```text
Single-Cycle-RISCV/
│
├── ALU.v
├── ALU_Decoder.v
├── Control_Unit_Top.v
├── Data_Memory.v
├── Instruction_Memory.v
├── Main_Decoder.v
├── Mux.v
├── PC.v
├── PC_Adder.v
├── Register_File.v
├── Sign_Extend.v
│
├── Single_Cycle_Top.v
├── Single_Cycle_Top_Tb.v
├── memfile.hex
│
├── RISCV_Single_Cycle_Microarchitecture.pdf
│
├── 1.jpeg
├── 2.jpeg
├── 3.jpeg
│
└── README.md
```

## Module Description

| Module | Description |
|---|---|
| `Single_Cycle_Top.v` | Integrates all processor components and implements the main datapath. |
| `PC.v` | Stores the current program counter and updates it on the rising clock edge. |
| `PC_Adder.v` | Calculates the next sequential instruction address (PC + 4). |
| `Instruction_Memory.v` | Stores and retrieves 32-bit instructions using the instruction address. |
| `Register_File.v` | Implements 32 general-purpose 32-bit registers with two read ports and one write port. |
| `Main_Decoder.v` | Generates primary control signals based on the instruction opcode. |
| `ALU_Decoder.v` | Determines the ALU operation using ALUOp, funct3, funct7, and opcode information. |
| `Control_Unit_Top.v` | Combines the main decoder and ALU decoder. |
| `Sign_Extend.v` | Extracts and sign-extends I-type and S-type immediate fields. |
| `ALU.v` | Performs arithmetic and logical operations and generates status flags. |
| `Data_Memory.v` | Implements a 32-bit data memory with synchronous writes and combinational reads. |
| `Mux.v` | Selects between two 32-bit input values based on a control signal. |
| `Single_Cycle_Top_Tb.v` | Provides clock generation, reset sequencing, and waveform dumping for simulation. |

## ALU Operations

The Arithmetic Logic Unit supports the following operations through its 3-bit ALU control input:

| ALU Control | Operation |
|---|---|
| `000` | Addition |
| `001` | Subtraction |
| `010` | Bitwise AND |
| `011` | Bitwise OR |
| `101` | Set Less Than |

The ALU also generates Carry, Overflow, Zero, and Negative status signals.

## Memory Organization

### Instruction Memory

- 1024 entries of 32 bits each.
- Instructions are initialized using `$readmemh`.
- The instruction address is word-aligned using address bits `[31:2]`.
- The initialization file is `memfile.hex`.

### Data Memory

- 1024 entries of 32 bits each.
- Supports synchronous memory writes.
- Provides combinational memory read data.
- Includes an initial memory value at address 28.

### Register File

- 32 registers, each 32 bits wide.
- Two independent read ports.
- One synchronous write port.
- Initial values are assigned to registers 5 and 6.

## Instruction Memory Initialization

The `memfile.hex` file contains the initial instruction sequence:

```text
@00000000
0062E3B3
0062F433
```

These hexadecimal values are loaded into instruction memory during initialization.

## Simulation and Verification

The project includes a Verilog testbench (`Single_Cycle_Top_Tb.v`) to simulate the processor.

The testbench performs the following operations:

- Generates a clock signal with a 100-time-unit period.
- Applies an active-low reset initially.
- Releases reset after 150 time units.
- Allows the processor to execute for an additional 450 time units.
- Generates a VCD waveform file for observing internal simulation activity.

### Tools

- Verilog HDL
- Icarus Verilog (simulation)
- GTKWave (waveform analysis)

### Running the Simulation

**1. Clone the repository**

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
```

**2. Navigate to the project directory**

```bash
cd Single-Cycle-RISCV
```

**3. Compile the testbench**

```bash
iverilog -g2012 -o riscv_sim Single_Cycle_Top_Tb.v
```

**4. Execute the simulation**

```bash
vvp riscv_sim
```

**5. Open the generated waveform**

```bash
gtkwave "Single Cycle.vcd"
```

Make sure `memfile.hex` is available in the working directory when running the simulation.

## Current Implementation Scope

The current RTL implementation provides the basic single-cycle processor datapath and instruction decoding for arithmetic, logical, load, and store operations.

Some architectural features remain incomplete:

- Branch control signals are decoded, but branch execution and PC redirection are not integrated.
- The current immediate generator handles I-type and S-type formats, not complete B-type immediate generation.
- The register file does not explicitly enforce the RISC-V x0 constant-zero behavior.
- The current data memory uses the ALU address directly as its array index.

These are potential areas for further development and architectural improvements.

## Future Enhancements

- Implement complete branch instruction execution.
- Add B-type immediate generation and branch target calculation.
- Enforce the RISC-V x0 register behavior.
- Improve memory address handling and access control.
- Extend instruction support.
- Develop a more comprehensive verification testbench with additional test cases.
- Verify the design using systematic instruction-level testing.

## Learning Outcomes

This project demonstrates practical understanding of:

- RISC-V ISA fundamentals and processor datapath organization.
- RTL design and modular Verilog development.
- ALU design and instruction decoding.
- Control signal generation and datapath integration.
- Register file and memory implementation.
- Clock, reset, and synchronous sequential logic.
- Functional simulation and waveform-based debugging.

## License

The Verilog source files contain Apache License 2.0 notices. Refer to the applicable license terms when using or redistributing the original source code.
