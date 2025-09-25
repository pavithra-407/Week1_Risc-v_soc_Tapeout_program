Day 2: Timing Libraries, Synthesis Methods & Flip-Flop Design ⏱️🔧

Welcome to Day 2 of the RISC -V SOC Tapeout program. Today we dive deeper into the link between RTL and real hardware, focusing on timing libraries, different synthesis styles, and coding strategies for flip-flops. These concepts are essential for making your RTL design realistic, efficient, and technology-aware.

📚 Table of Contents



⏱️ Timing Libraries
What are Timing Libraries?

A timing library (.lib file) acts as the bridge between RTL and physical silicon. It defines:

Functional behavior of each cell (e.g., AND, OR, NAND, DFF).

Timing information (propagation delays, setup/hold times).

Power consumption under various conditions.

Area footprint of each cell.

This ensures that synthesis tools map RTL into real, manufacturable cells with accurate timing and power characteristics.

Overview of the SKY130 PDK

The SKY130 PDK (Process Design Kit) is an open-source 130nm CMOS technology provided by SkyWater.

It includes transistor models, design rules, and libraries for digital, analog, and mixed-signal design.

For RTL synthesis, we mainly rely on standard-cell libraries like sky130_fd_sc_hd (high-density library).

📌 Example: sky130_fd_sc_hd__tt_025C_1v80.lib

Understanding tt_025C_1v80

The file name encodes process, voltage, and temperature (PVT) conditions:

tt → typical process corner (average device behavior).

025C → 25°C operating temperature.

1v80 → 1.8V supply voltage.

Together, this models a "typical" environment for cell characterization. Different .lib files exist for fast, slow, hot, or cold corners.

Inspecting a Liberty File

To explore the .lib file:

gedit sky130_fd_sc_hd__tt_025C_1v80.lib


Inside, you’ll see entries like:
cell (AND2X1) {
    area : 1.334400 ;
    pin(A) { direction : input; capacitance : 0.018; }
    pin(B) { direction : input; capacitance : 0.018; }
    pin(Y) { direction : output; function : "A & B"; }
}

This describes:

A 2-input AND gate.

Its area cost, input capacitances, and boolean function.

Timing tables that define delay values for various input/output conditions.


Hierarchical vs. Flattened Synthesis

Synthesis can be performed in two distinct styles:

Hierarchical Synthesis

Preserves the module hierarchy written in RTL.

Each submodule is synthesized individually.

Great for modularity, debugging, and reuse.

Advantages
✅ Faster compilation for large systems.
✅ Easier to debug (modules map directly to RTL).
✅ Supports IP reuse.

Disadvantages
❌ Limited cross-boundary optimizations.
❌ Larger netlists with duplicated logic.

📌 Example (Hierarchical Netlist):


Flattened Synthesis

All modules are merged into a single level before synthesis.

Allows the tool to optimize across boundaries.

Produces a monolithic netlist.

Advantages
✅ Enables global optimizations → better performance/area.
✅ Useful for final tapeout netlists.

Disadvantages
❌ Harder to debug (loss of RTL structure).
❌ Increased runtime and memory for big designs.

📌 Example (Flattened Netlist):

| Aspect             | Hierarchical 🌲                  | Flattened 🧩             |
| ------------------ | -------------------------------- | ------------------------ |
| Module Structure   | Preserved                        | Removed                  |
| Optimization Scope | Local (per module)               | Global (whole design)    |
| Compile Time       | Faster for large designs         | Slower for large designs |
| Debugging          | Easier (maps to RTL)             | Harder                   |
| Reusability        | High                             | Low                      |
| Typical Usage      | Early prototyping, modular flows | Final optimized netlists |


Flip-Flop Design Styles

Flip-flops are the basic storage elements in sequential logic. Coding style impacts synthesis results and library mapping.

1. Asynchronous Reset DFF

module dff_async_reset(input clk, input rst, input d, output reg q);
  always @(posedge clk or posedge rst)
    if (rst)
      q <= 1'b0;
    else
      q <= d;
endmodule

Reset immediately overrides the clock.

Common for designs that need instant reset (e.g., CPUs).

2. Asynchronous Set DFF

module dff_async_set(input clk, input set, input d, output reg q);
  always @(posedge clk or posedge set)
    if (set)
      q <= 1'b1;
    else
      q <= d;
endmodule

Set immediately forces q=1.

Useful for initialization circuits.


3. Synchronous Reset DFF
module dff_sync_reset(input clk, input rst, input d, output reg q);
  always @(posedge clk)
    if (rst)
      q <= 1'b0;
    else
      q <= d;
endmodule
Reset works only on clock edge.

Easier for timing analysis since reset is clock-aligned.


Simulation & Synthesis Workflow
Simulation (with Icarus Verilog)

iverilog dff_async_res.v tb_dff_async_res.v
./a.out
gtkwave tb_dff_async_res.vcd
Produces the waveform:


Synthesis (with Yosys)

Inside Yosys:

read_liberty -lib ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_res.v
synth -top dff_async_res
dfflibmap -liberty ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show


Produces a mapped netlist:


Key Takeaways

.lib files define the real-world properties of standard cells.

Hierarchical synthesis helps modularity, while flattening enables aggressive optimization.

Different DFF coding styles translate into different library cells during mapping.

Simulation validates logic, while synthesis prepares the design for physical implementation.



