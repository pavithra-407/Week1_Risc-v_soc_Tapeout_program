# RISC-V SoC Tapeout Program: Day 1 - Introduction to Verilog RTL Design & Synthesis

Welcome to Day 1 of the **RISC-V SoC Tapeout Program**! This session introduces you to the fundamentals of Verilog RTL design, open-source simulation with Icarus Verilog, and logic synthesis using Yosys. Through hands-on labs and detailed explanations, you'll build a strong foundation for designing RISC-V-based System-on-Chip (SoC) circuits.

## Table of Contents

1. [Understanding Simulators, Designs, and Testbenches](#understanding-simulators-designs-and-testbenches)
   - [What is a Simulator?](#what-is-a-simulator)
   - [What is a Design?](#what-is-a-design)
   - [What is a Testbench?](#what-is-a-testbench)
2. [Getting Started with Icarus Verilog](#getting-started-with-icarus-verilog)
3. [Lab 1: Simulating a 2-to-1 Multiplexer](#lab-1-simulating-a-2-to-1-multiplexer)
4. [Verilog Code Analysis](#verilog-code-analysis)
5. [Introduction to Yosys and Gate Libraries](#introduction-to-yosys-and-gate-libraries)
   - [What is Yosys?](#what-is-yosys)
   - [Why Multiple Gate Flavors?](#why-multiple-gate-flavors)
6. [Lab 2: Synthesis with Yosys](#lab-2-synthesis-with-yosys)
7. [Key Takeaways](#key-takeaways)
8. [Additional Resources](#additional-resources)

## Understanding Simulators, Designs, and Testbenches

### What is a Simulator?

A **simulator** is a software tool that verifies the functionality of a digital circuit by applying input stimuli and observing the outputs. It allows designers to test and debug RISC-V designs before physical implementation, ensuring correctness and reliability.

### What is a Design?

The **design** is the Verilog code that describes the logic and functionality of your circuit, such as a RISC-V core component (e.g., ALU, multiplexer). It defines how inputs are processed to produce outputs.

### What is a Testbench?

A **testbench** is a Verilog module that creates a simulation environment for the design. It generates test inputs, applies them to the design, and verifies the outputs against expected results.



## Getting Started with Icarus Verilog

**Icarus Verilog (iverilog)** is an open-source Verilog simulator widely used for functional verification. It compiles Verilog code into a simulatable format and generates waveform data for analysis.

### Simulation Flow
- **Input**: Provide the design and testbench Verilog files to `iverilog`.
- **Output**: The simulator generates a `.vcd` file, which can be visualized using GTKWave to inspect waveforms.

## Lab 1: Simulating a 2-to-1 Multiplexer

This lab guides you through simulating a 2-to-1 multiplexer, a fundamental component in RISC-V designs for selecting data paths.

### Steps
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
   cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
   ```

2. **Install Tools**:
   Install Icarus Verilog and GTKWave on Ubuntu:
   ```bash
   sudo apt update
   sudo apt install iverilog gtkwave
   ```

3. **Compile the Design**:
   Compile the multiplexer and its testbench:
   ```bash
   iverilog good_mux.v tb_good_mux.v
   ```

4. **Run the Simulation**:
   Execute the compiled simulation:
   ```bash
   ./a.out
   ```

5. **View Waveform**:
   Open the waveform in GTKWave:
   ```bash
   gtkwave tb_good_mux.vcd
   ```

**Output**:
- ![Multiplexer Waveform](good_mux.png)

## Verilog Code Analysis

Below is the Verilog code for the 2-to-1 multiplexer (`good_mux.v`):

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

### Explanation
- **Inputs**: `i0`, `i1` (data inputs), `sel` (select line).
- **Output**: `y` (registered output).
- **Logic**: 
  - If `sel = 1`, `y` takes the value of `i1`.
  - If `sel = 0`, `y` takes the value of `i0`.
- **Sensitivity List**: `always @(*)` ensures the block updates for any input change, suitable for combinational logic.

**Use Case**: This multiplexer can be used in RISC-V data paths to select between operands or control signals.

## Introduction to Yosys and Gate Libraries

### What is Yosys?

**Yosys** is an open-source synthesis tool that transforms Verilog HDL into a gate-level netlist, a critical step in mapping RISC-V designs to hardware.

### Yosys Features
- **Synthesis**: Converts Verilog to a logic circuit.
- **Optimization**: Reduces area or improves timing.
- **Technology Mapping**: Maps logic to standard cells in a library.
- **Verification**: Ensures functional correctness.
- **Extensibility**: Supports custom workflows for RISC-V SoC design.

### Why Multiple Gate Flavors?

Gate libraries (e.g., `sky130_fd_sc_hd__tt_025C_1v80.lib`) contain multiple versions of standard cells (e.g., AND, OR, flip-flops) to address different design needs:
- **Performance**: High-speed gates for critical RISC-V pipeline paths.
- **Power**: Low-power cells for energy-efficient designs.
- **Area**: Compact cells to minimize chip size.
- **Drive Strength**: Stronger gates for high fan-out signals.
- **Signal Integrity**: Specialized cells to reduce noise.
- **Synthesis Mapping**: Tools select optimal cells based on design constraints.

The Sky130 PDK’s `.lib` file provides these cell variations, enabling efficient synthesis for RISC-V SoCs.

![Gate Library Example](images/lab2.png)

## Lab 2: Synthesis with Yosys

This lab synthesizes the 2-to-1 multiplexer using Yosys and the Sky130 PDK, producing a gate-level netlist.

### Steps
1. **Start Yosys**:
   ```bash
   yosys
   ```

2. **Read the Liberty Library**:
   ```tcl
   read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

3. **Read the Verilog Code**:
   ```tcl
   read_verilog /path/to/good_mux.v
   ```

4. **Synthesize the Design**:
   ```tcl
   synth -top mux_2to1
   ```

5. **Technology Mapping**:
   Map the design to Sky130 standard cells:
   ```tcl
   abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

6. **Visualize the Netlist**:
   Display the gate-level schematic:
   ```tcl
   show
   ```

**Output**:
- ![Gate-Level Netlist](synth_good_mux.png)

## Key Takeaways

- **Simulators and Testbenches**: Icarus Verilog and testbenches enable functional verification of RISC-V designs.
- **Verilog Basics**: The 2-to-1 multiplexer lab demonstrates proper combinational logic coding.
- **Yosys Synthesis**: Converts Verilog to gate-level netlists, essential for RISC-V SoC tapeout.
- **Gate Libraries**: The Sky130 PDK’s `.lib` file supports diverse cell flavors for performance, power, and area optimization.
- **Practical Skills**: Labs provide hands-on experience with simulation and synthesis workflows, critical for RISC-V design.

## Additional Resources

- **RISC-V Specification**: Explore the [RISC-V Instruction Set Manual](https://riscv.org/technical/specifications/) for designing RISC-V cores.
- **Icarus Verilog**: Visit the [Icarus Verilog Wiki](http://iverilog.fandom.com/) for simulation tutorials.
- **Yosys Documentation**: Refer to the [Yosys Manual](http://www.clifford.at/yosys/) for synthesis techniques.
- **Sky130 PDK**: Learn about the Sky130 process via the [OpenROAD Project](https://theopenroadproject.org/).
- **GTKWave**: Check the [GTKWave User Guide](http://gtkwave.sourceforge.net/) for waveform analysis.
- **Verilog Learning**: Use [ChipVerify](https://www.chipverify.com/) for Verilog coding best practices.
