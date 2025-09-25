Day 4: Gate-Level Simulation (GLS), RTL Coding Practices, and Handling Synthesis-Simulation Mismatches

Welcome to Day 4 of the RISC-V SoC Tapeout Program 🚀
Today, we bridge the gap between RTL design and post-synthesis verification by studying Gate-Level Simulation (GLS), common coding styles in Verilog, and the root causes of synthesis vs simulation mismatches. These concepts are vital in SoC projects, where correctness must be ensured not only at RTL but also after mapping to standard cells.


Table of Contents

🔹 What is Gate-Level Simulation (GLS)?

🔹 Why GLS is Critical for SoC Tapeouts

🔹 Synthesis-Simulation Mismatches

Causes

Prevention strategies

🔹 RTL Coding Practices

Blocking (=) vs Non-blocking (<=)

Combinational vs Sequential Coding

Best Practices

🔹 Labs & Hands-On Work

🔹 Key Takeaways

1. What is Gate-Level Simulation (GLS)?

Gate-Level Simulation (GLS) is the process of simulating the post-synthesis netlist of a design (mapped to standard cells) to ensure that:

✅ The synthesized circuit matches RTL functionality

✅ Timing effects (delays, setup/hold) are captured

✅ Test features (scan chains, resets) work as expected

GLS Types

Zero-delay GLS → Functional verification only

Unit-delay GLS → Assumes fixed delays (rough timing check)

SDF-annotated GLS → Uses real delays extracted from synthesis or place-and-route

2. Why GLS is Critical for SoC Tapeouts

For a RISC-V SoC design, GLS ensures that:

🔹 Clock/reset strategy works under real gates

🔹 Integration of multiple RTL blocks (CPU, memory, peripherals) is functionally correct post-synthesis

🔹 Low-power logic (clock gating, power domains) is implemented as intended

🔹 Prevents late-stage surprises before fabrication (a failed GLS at tapeout = costly respin 💸)

3. Synthesis-Simulation Mismatches

A common pain point in VLSI: RTL sim ≠ GLS sim.
This happens when the RTL coding style does not translate cleanly into gates.

🔍 Causes

Use of non-synthesizable constructs (delays #10, $display, initial for synthesis logic).

Incomplete sensitivity lists in always blocks.

Wrong use of blocking (=) vs non-blocking (<=).

Tool-dependent interpretation of ambiguous code.

✅ Prevention Strategies

Stick to synthesizable Verilog only.

Use always @(*) for combinational logic.

Always provide default/else cases.

Use blocking (=) in combinational, non-blocking (<=) in sequential.

Verify both RTL and GLS against the same testbench.

4. RTL Coding Practices
Blocking (=) vs Non-blocking (<=)
| Aspect       | Blocking `=`                                   | Non-blocking `<=`                                   |
| ------------ | ---------------------------------------------- | --------------------------------------------------- |
| Execution    | Sequential (order matters)                     | Parallel (all updates end of cycle)                 |
| Suitable for | Combinational logic                            | Sequential (clocked) logic                          |
| Pitfall      | Race conditions if misused in sequential logic | Incorrect results if misused in combinational logic |

Example: Correct Usage
Combinational (Blocking)

always @(*) begin
  y = a & b;   // blocking
end

Sequential (Non-blocking)

always @(posedge clk) begin
  q <= d;      // non-blocking
end

5. Labs & Hands-On Work

We ran experiments using Yosys + Icarus Verilog + Sky130 PDK to explore GLS and mismatch cases.

 Lab 1: Synthesizing a 2:1 MUX

RTL written using ternary operator.

Verified functionality at RTL and GLS.

 Lab 2: Identifying Bad MUX Coding

// Wrong example
always @(sel) begin
  if (sel)
    y <= i1;   //  non-blocking used in comb logic
  else
    y <= i0;
end

issues: incomplete sensitivity list, misuse of <=.
Fix: always @(*) with blocking assignments.

Lab 3: Sequential Pitfall (Blocking Inside Clocked Block)

always @(posedge clk) begin
  q = d;  //  should be <=
end

This may cause mismatches at GLS. Corrected with non-blocking.

 Lab 4: GLS with Annotated Timing

Netlist simulated with unit-delay primitives.

Observed setup/hold violations under incorrect reset logic.



6. Key Takeaways

> GLS is mandatory before tapeout to ensure functional + timing correctness.
> Synthesis-simulation mismatches are usually caused by bad RTL coding practices.
> Use blocking in combinational, non-blocking in sequential.
> Always run GLS on the post-synthesis netlist with the same testbench.
> Clean, synthesizable RTL saves debug time during SoC integration.

 Summary

Day 4 focused on Gate-Level Simulation (GLS) and RTL best practices. We saw how incorrect Verilog coding leads to synthesis-simulation mismatches, and how GLS helps catch these issues before tapeout.

 In SoC projects, this stage is crucial because fixing errors after fabrication is nearly impossible. By mastering GLS and proper coding styles, we move one step closer to a successful RISC-V SoC tapeout.

