Day 1: Getting Started with Verilog RTL and Synthesis 🚀

Welcome to Day 1 of the RTL Workshop!
This session is about laying the foundation for digital design using Verilog, performing simulation with Icarus Verilog (iverilog), and understanding the basics of synthesis with Yosys. By the end of today, you’ll simulate your first design, visualize waveforms, and generate a gate-level netlist.

📌 Topics Covered

What is a design, testbench, and simulator?

Installing and using iverilog and GTKWave

Hands-on lab: 2:1 Multiplexer simulation

Basics of logic synthesis using Yosys

Gate libraries and their role in technology mapping
1️⃣ Design, Testbench & Simulator

Design: The RTL code written in Verilog that describes the circuit’s functionality.

Testbench: A verification environment that applies stimulus to the design and checks its outputs.

Simulator: A tool (like iverilog) that executes the Verilog design + testbench and produces output waveforms.

2️⃣ Simulation with Icarus Verilog

Make sure you have the required tools installed:
sudo apt install iverilog
sudo apt install gtkwave
Steps to run simulation:

Compile design and testbench:
iverilog good_mux.v tb_good_mux.v
Run the simulation:
./a.out
View the waveform:
gtkwave tb_good_mux.vcd

3️⃣ Lab Design: 2-to-1 Multiplexer

Here’s the RTL code for a simple multiplexer:
module good_mux (input i0, input i1, input sel, output reg y);
  always @(*) begin
    if (sel)
      y = i1;
    else
      y = i0;
  end
endmodule



    Inputs: i0, i1 (data lines), sel (selection signal)

    Output: y (selected value)

    Logic: If sel=1, y=i1, otherwise y=i0


4️⃣ Introduction to Yosys

Yosys is an open-source synthesis tool that transforms RTL into a gate-level netlist.

Why do we need libraries (.lib)?

They describe standard cells like AND, OR, NAND, flip-flops, etc.

Each gate has different sizes, drive strengths, power consumption, and delays.

The synthesis tool chooses the most suitable gate variant during mapping.

5️⃣ Synthesis Flow with Yosys

Open Yosys:

yosys

Run the following commands inside Yosys:
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog good_mux.v
synth -top good_mux
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
show
This produces a technology-mapped netlist and lets you visualize the design at the gate level.

Summary

Understood design, testbench, and simulator.

Installed and used iverilog + GTKWave for simulation.

Wrote and tested a 2:1 multiplexer.

Learned how Yosys uses libraries for synthesis.

Generated a gate-level schematic from RTL.






