# RISC-V SoC Tapeout Program: Day 2 - Timing Libraries, Synthesis Strategies, and Flip-Flop Design

Welcome to Day 2 of the **RISC-V SoC Tapeout Program**! This session explores essential concepts for designing efficient RISC-V-based System-on-Chip (SoC) circuits. We focus on understanding timing libraries, comparing synthesis approaches, and mastering flip-flop coding styles to ensure robust RTL designs.

## Table of Contents

1. [Timing Libraries](#timing-libraries)
   - [Sky130 PDK Introduction](#sky130-pdk-introduction)
   - [Understanding tt_025C_1v80](#understanding-tt_025c_1v80)
   - [Exploring the .lib File](#exploring-the-lib-file)
2. [Hierarchical vs. Flattened Synthesis](#hierarchical-vs-flattened-synthesis)
   - [Hierarchical Synthesis](#hierarchical-synthesis)
   - [Flattened Synthesis](#flattened-synthesis)
   - [Comparison Table](#comparison-table)
3. [Flip-Flop Coding Styles](#flip-flop-coding-styles)
   - [Asynchronous Reset D Flip-Flop](#asynchronous-reset-d-flip-flop)
   - [Asynchronous Set D Flip-Flop](#asynchronous-set-d-flip-flop)
   - [Synchronous Reset D Flip-Flop](#synchronous-reset-d-flip-flop)
4. [Simulation and Synthesis Workflow](#simulation-and-synthesis-workflow)
   - [Icarus Verilog Simulation](#icarus-verilog-simulation)
   - [Yosys Synthesis](#yosys-synthesis)
5. [Key Takeaways](#key-takeaways)
6. [Additional Resources](#additional-resources)

## Timing Libraries

Timing libraries are critical for defining the performance characteristics of cells in a Process Design Kit (PDK). They provide timing, power, and process variation data used during synthesis and timing analysis.

### Sky130 PDK Introduction

The **Sky130 PDK**, developed by SkyWater Technology, is an open-source 130nm CMOS process design kit. It includes models, libraries, and tools for IC design, enabling RISC-V SoC development with comprehensive timing and power information.

![Sky130 PDK Overview](images/sky130PDK.png)

### Understanding tt_025C_1v80

The naming convention `sky130_fd_sc_hd__tt_025C_1v80.lib` breaks down as follows:
- **tt**: Typical process corner, representing nominal manufacturing conditions.
- **025C**: Operating temperature of 25°C, critical for temperature-dependent performance.
- **1v80**: Core supply voltage of 1.8V, defining the electrical operating condition.

This library models cell behavior under these specific conditions, ensuring accurate synthesis for RISC-V designs.

### Exploring the .lib File

To view the `sky130_fd_sc_hd__tt_025C_1v80.lib` file:
1. Install a text editor (e.g., **gedit**) on Ubuntu:
   ```bash
   sudo apt update
   sudo apt install gedit
   ```
2. Open the file:
   ```bash
   gedit /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

The `.lib` file contains cell descriptions, including timing arcs, power consumption, and area, which are essential for synthesis and static timing analysis in RISC-V SoC designs.

![Library File Exploration](sky130_fd_sc_hd__tt_025C_1v80.lib.png)

## Hierarchical vs. Flattened Synthesis

Synthesis transforms RTL code into a gate-level netlist. The choice between hierarchical and flattened synthesis impacts optimization, runtime, and debugging.

### Hierarchical Synthesis

**Definition**: Preserves the RTL module hierarchy, synthesizing each module independently.

**Process**:
- Tools like Yosys use the `hierarchy` command to analyze and maintain module structure.
- Each module is synthesized separately, retaining boundaries.

**Advantages**:
- Faster synthesis for large RISC-V designs.
- Easier debugging due to clear module boundaries.
- Modular approach simplifies integration with place-and-route tools.

**Disadvantages**:
- Limited cross-module optimization.
- May require additional configuration for comprehensive reporting.

**Example**: Synthesizing a RISC-V core with separate modules for ALU and control units.



### Flattened Synthesis

**Definition**: Collapses all modules into a single, flat netlist, eliminating hierarchy.

**Process**:
- Yosys uses the `flatten` command to merge modules.
- Enables whole-design optimization across module boundaries.

**Advantages**:
- Maximizes optimization by considering the entire design.
- Produces a unified netlist, streamlining downstream processes.

**Disadvantages**:
- Longer runtime for complex RISC-V SoCs.
- Loss of hierarchy complicates debugging.
- Increased memory usage due to netlist complexity.

**Example**: Flattening a RISC-V pipeline for aggressive optimization.

![Flattened Synthesis Output](flat syn.png)

### Comparison Table

| Aspect                | Hierarchical Synthesis                | Flattened Synthesis                  |
|-----------------------|---------------------------------------|--------------------------------------|
| **Hierarchy**         | Preserved                            | Collapsed                           |
| **Optimization Scope**| Module-level                         | Whole-design                        |
| **Runtime**           | Faster for large designs             | Slower for large designs            |
| **Debugging**         | Easier (traces to RTL)               | Harder (no module boundaries)       |
| **Output Complexity** | Modular structure                    | Single, complex netlist             |
| **Use Case**          | Modularity, analysis, reporting      | Maximum optimization                |

## Flip-Flop Coding Styles

Flip-flops are the backbone of sequential logic in RISC-V designs, storing state information. Below are efficient Verilog coding styles for different flip-flop behaviors.

### Asynchronous Reset D Flip-Flop

**Verilog Code**:

```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
![d flip flop with asynchronous reset](dff_asyncres.png)
**Functionality**:
- Resets output `q` to `0` immediately on `async_reset = 1`.
- Captures input `d` on the rising edge of `clk` when reset is low.

**Use Case**: Critical for RISC-V reset circuits requiring immediate state initialization.

### Asynchronous Set D Flip-Flop

**Verilog Code**:

```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
![d flip flop with asynchronous reset](dff_async_set.png)
**Functionality**:
- Sets output `q` to `1` immediately on `async_set = 1`.
- Captures input `d` on the rising edge of `clk` when set is low.

**Use Case**: Useful for initializing RISC-V control signals to a known high state.

### Synchronous Reset D Flip-Flop

**Verilog Code**:

```verilog
module dff_syncres (input clk, input async_reset, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```

**Functionality**:
- Resets output `q` to `0` on the rising edge of `clk` when `sync_reset = 1`.
- Captures input `d` otherwise.

**Use Case**: Ideal for synchronous RISC-V pipelines where reset aligns with clock edges.

![Flip-Flop Waveform](dff_syncres.png)

## Simulation and Synthesis Workflow

This section outlines the workflow for simulating and synthesizing flip-flop designs using open-source tools, tailored for RISC-V SoC development.

### Icarus Verilog Simulation

**Steps**:
1. Compile the Verilog code with a testbench:
   ```bash
   iverilog dff_async_reset.v tb_dff_async_reset.v
   ```
2. Run the simulation:
   ```bash
   ./a.out
   ```
3. View the waveform using GTKWave:
   ```bash
   gtkwave tb_dff_async_reset.vcd
   ```

**Output**:
- ![Simulation Waveform](dff_asyncres.png)

### Yosys Synthesis

**Steps**:
1. Launch Yosys:
   ```bash
   yosys
   ```
2. Read the Liberty file:
   ```tcl
   read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
3. Read the Verilog file:
   ```tcl
   read_verilog /path/to/dff_async_reset.v
   ```
4. Synthesize the design:
   ```tcl
   synth -top dff_async_reset
   ```
5. Map flip-flops to the library:
   ```tcl
   dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
6. Perform technology mapping:
   ```tcl
   abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
7. Visualize the gate-level netlist:
   ```tcl
   show
   ```

**Output**:
- ![Gate-Level Netlist](synth_dff_asyncres.png)

## Key Takeaways

- **Timing Libraries**: The Sky130 PDK’s `.lib` file provides critical timing and power data for RISC-V SoC synthesis.
- **Synthesis Strategies**: Hierarchical synthesis preserves modularity, while flattened synthesis maximizes optimization, each suited to different RISC-V design needs.
- **Flip-Flop Coding**: Proper coding of asynchronous and synchronous flip-flops ensures reliable state management in RISC-V pipelines.
- **Workflow Mastery**: Using Icarus Verilog and Yosys enables robust simulation and synthesis, critical for verifying RISC-V designs.
- **Practical Application**: The labs reinforce best practices for RTL coding, synthesis, and visualization, aligning with RISC-V SoC tapeout requirements.

## Additional Resources

- **RISC-V Specification**: Refer to the [RISC-V Instruction Set Manual](https://riscv.org/technical/specifications/) for designing optimized RISC-V cores.
- **Yosys Documentation**: Explore the [Yosys Manual](http://www.clifford.at/yosys/) for advanced synthesis techniques.
- **Sky130 PDK**: Learn more about the Sky130 process via the [OpenROAD Project](https://theopenroadproject.org/).
- **Icarus Verilog**: Check the [Icarus Verilog Wiki](http://iverilog.fandom.com/) for simulation tips.
- **GTKWave**: Use the [GTKWave User Guide](http://gtkwave.sourceforge.net/) for waveform analysis.
- **Verilog Best Practices**: Visit [ChipVerify](https://www.chipverify.com/) for Verilog coding guidelines.
