Day 3: Logic Optimization in RTL Synthesis

Welcome to Day 3 of the RISC-V SoC Tapeout Program 🚀
Today, we move beyond basic synthesis and dive into logic optimization techniques applied during RTL-to-gates mapping. These methods ensure our designs are area-efficient, power-aware, and performance-driven, preparing them for physical design and eventually tapeout.

Table of Contents

🔹 Why Optimization is Needed

🔹 Constant Folding & Propagation

🔹 Sequential Optimizations

Unused FF Removal

Constant-driven FFs

Reset Simplification

🔹 Advanced Techniques

State Encoding Optimization

Logic Cloning

Retiming

🔹 Lab Experiments

🔹 Key Takeaways


1. Why Optimization is Needed

Before diving in, ask yourself:
➡️ Is my circuit minimal in terms of logic?
➡️ Can I reduce area without altering functionality?
➡️ Are there flip-flops or states that don’t contribute to the final output?

Without optimization, synthesis produces a correct design, but not necessarily an efficient one. Optimization ensures:

✅ Reduced gate count (smaller area → cheaper silicon).

✅ Lower power consumption (critical in SoCs).

✅ Better timing closure (higher frequency operation).

2. Constant Folding & Propagation

Constant folding replaces expressions involving constants with their simplified values during synthesis.

Example

module opt_check (input a , input b , output y);
    assign y = a ? b : 1'b0;
endmodule

Analysis:

If a=0, y=0.

If a=1, y=b.
So effectively → y = a & b.

 After optimization, the multiplexer collapses into a single AND gate.

3. Sequential Optimizations

Sequential elements (flip-flops, latches) are expensive in silicon. Tools try to remove or simplify them.

🟢 Case 1: Removing Unused Flip-Flops

If a register’s output is not connected to any primary output or used internally, it gets optimized away.

🟢 Case 2: Constant-driven Flip-Flops

If a DFF always loads 1 or 0, the tool replaces it with a constant net instead of a physical register.

module dff_const (input clk, input reset, output reg q);
always @(posedge clk or posedge reset) begin
    if(reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end
endmodule


After optimization: q is tied to constant 1 (once reset is released). Saves area and reduces clock tree load.

🟢 Case 3: Reset Simplification

If reset conditions don’t affect functionality, they may be removed or absorbed into logic.

4. Advanced Techniques
🔹 State Encoding Optimization

For FSMs, synthesis tools choose the best encoding:

Binary encoding → fewer FFs, more complex logic.

One-hot encoding → more FFs, simpler logic (good for speed).

Gray encoding → minimizes bit toggles (good for power).

The tool may re-encode automatically for performance or power benefits.

🔹 Logic Cloning

Duplicating logic to balance fanout and improve timing.

Example: Instead of one AND gate driving 50 loads, synthesis may create two AND gates, each driving 25 loads.

Benefit → Reduced delay, better clock frequency.

🔹 Retiming

Repositioning registers across combinational logic to balance path delays.

Moves FFs closer to critical paths.

Keeps overall latency same, but improves maximum clock speed.

This is a graph-theory-based optimization where flip-flops are shifted without altering I/O behavior.

5. Lab Experiments

In this lab, we practiced optimization using Yosys with the Sky130 PDK library.

 Flow Commands
# Read library and RTL
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v

# Synthesis
synth -top opt_check

# Optimization passes
opt_clean -purge
opt_reduce
opt_rmdff

# Technology mapping
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

# Show netlist
show


Experiments

Lab 1: Constant propagation on simple mux → reduced to AND gate.

Lab 2: Sequential DFF always 1 → optimized into a wire.

Lab 3: FSM state reduction → fewer flip-flops after re-encoding.

Lab 4: Retiming applied to balance critical path delays.


6. Key Takeaways

>Optimization is not just coding clean Verilog, but ensuring synthesis tools can simplify the design effectively.
> Constant folding, redundant FF removal, and state optimization reduce area and power.
>Retiming and cloning improve timing closure for high-speed SoCs.
> Each lab demonstrates how RTL coding style directly affects the synthesized netlist.

 >>Summary

Day 3 introduced us to logic optimization in RTL synthesis, focusing on constant propagation, sequential simplifications, state machine optimizations, cloning, and retiming.

These techniques are essential for real-world tapeout, where silicon cost, performance, and power efficiency determine the success of a chip.













