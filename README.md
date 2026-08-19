# counter_using_clock_divider-
# EXPERIMENT – 3.B 4- bit Up/Down Counter and MOD-10 Counter using Clock Divider in FPGA

# Aim
To design and simulate a 4-bit Up/Down Counter and MOD-10 Counter using Verilog HDL, and verify their functionality using a Clock Divider in FPGA (Vivado 2023.1).

# Apparatus Required
Vivado 2023.1

# Procedure
Launch Vivado 2023.1
Open Vivado and create a new RTL project.
Design the Verilog Code
Write the Verilog code for:
4-bit Up/Down Counter
MOD-10 Counter
Clock Divider
Create the Testbench
Write a testbench to simulate counter behavior.
The testbench should apply clock and reset signals and monitor output.
Create the Verilog Files
Add design module and testbench into the Vivado project.
Run Simulation
Run behavioral simulation.
Observe the Waveforms
Verify counting sequence:
Up counter: 0 → 15
Down counter: 15 → 0
MOD-10: 0 → 9 → 0
Save and Document Results
Capture waveform screenshots and save logs.
NOTE: These files go in Design Sources:
   clock_divider.v
   up_down_counter.v
   mod10_counter.v
   top_counter.v  (Your Top Module)
So the Top Module (Using Clock Divider) must be placed in: Design Sources
After adding it: Right click top_counter- Select Set as Top
tb_counter.v - Testbench is used only for simulation.

# Code
# Clock Divider 
```
module clock_divider(
    input clk,
    input rst,
    output reg slow_clk
);

    reg [24:0] count;

    always @(posedge clk or posedge rst) begin
        if (rst) begin
            count <= 0;
            slow_clk <= 0;
        end
        else begin
            count <= count + 1;
            slow_clk <= count[24];   // Divide clock
        end
    end

endmodule


```
# 4-bit Up/Down Counter
```
module up_down_counter(
    input clk,
    input rst,
    input mode,          // 1 = Up, 0 = Down
    output reg [3:0] count
);

    always @(posedge clk or posedge rst) begin
        if (rst)
            count <= 4'b0000;
        else if (mode)
            count <= count + 1;
        else
            count <= count - 1;
    end

endmodule
```
# MOD-10 Counter
```
module mod10_counter(
    input clk,
    input rst,
    output reg [3:0] count
);

    always @(posedge clk or posedge rst) begin
        if (rst)
            count <= 4'b0000;
        else if (count == 4'd9)
            count <= 4'b0000;
        else
            count <= count + 1;
    end

endmodule
```
# Top Module (Using Clock Divider)
```
module top_counter(
    input clk,
    input rst,
    input mode,
    output [3:0] updown_out,
    output [3:0] mod10_out
);

    wire slow_clk;

    clock_divider cd(clk, rst, slow_clk);
    up_down_counter ud(slow_clk, rst, mode, updown_out);
    mod10_counter m10(slow_clk, rst, mod10_out);

endmodule
```
# Testbench

```

module tb_counter;

    reg clk, rst, mode;
    wire [3:0] updown_out;
    wire [3:0] mod10_out;

    top_counter uut(clk, rst, mode, updown_out, mod10_out);

    initial clk = 0;
    always #5 clk = ~clk;

    initial begin
        rst = 1;
        mode = 1;  // Up mode
        #20 rst = 0;

        #200 mode = 0;  // Down mode

        #300 $finish;
    end

endmodule
```
# Output Waveform:

# Conclusion
The 4-bit Up/Down Counter and MOD-10 Counter were successfully designed using Verilog HDL and verified through simulation in Vivado 2023.1. The clock divider was used to generate a slower clock suitable for FPGA implementation. The waveform analysis confirmed correct counting sequences in both up/down and MOD-10 modes.
