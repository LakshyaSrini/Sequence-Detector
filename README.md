# Sequence-Detector
Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

Verilog Code for Sequence Detector Using Moore FSM

module moore_sequence_detector(
    input  clk, 
    input  reset, 
    input  seq_in, 
    output reg detected
); 

localparam  S0 = 3'b000,
            S1 = 3'b001,
            S2 = 3'b010,
            S3 = 3'b011,
            S4 = 3'b100;
reg [2:0] current_state, next_state;

always @(posedge clk or posedge reset) begin
    if (reset)
        current_state <= S0;
    else
        current_state <= next_state;
end

// Next state logic and detected output logic
always @(*) begin
    case (current_state)
        S0: begin
            if (seq_in) 
                next_state = S1;
            else 
                next_state = S0;
        end
        S1: begin
            if (seq_in) 
                next_state = S1;
            else 
                next_state = S2;
        end
        S2: begin
            if (seq_in) 
                next_state = S3;
            else 
                next_state = S0;
        end
        S3: begin
            if (seq_in) 
                next_state = S4;
            else 
                next_state = S2;
        end
        S4: begin
            if (seq_in) 
                next_state = S1;
            else 
                next_state = S0;
        end
        default: 
            next_state = S0;
    endcase
end

// Detected logic in the sequential block
always @(posedge clk or posedge reset) begin
    if (reset)
        detected <= 0;
    else if (current_state == S4)
        detected <= 1;
    else
        detected <= 0;
end

endmodule

![Moore](https://github.com/user-attachments/assets/781f81f0-0aab-4ab4-8926-4f549ec3e8a1)


Verilog Code for Sequence Detector Using Mealy FSM

module mealy_seq_detector(input  clk, 
    input  reset, 
    input  seq_in, 
    output reg detected
); 

localparam S0 = 3'b000,
           S1 = 3'b001,
           S2 = 3'b010,
           S3 = 3'b011,
           S4 = 3'b100;
reg [2:0] current_state, next_state;

always @(posedge clk or posedge reset) begin
    if (reset)
        current_state <= S0;
    else
        current_state <= next_state;
        end

// Next state logic and detected output logic
always @(*) begin
next_state = current_state;
detected = 0;
    case (current_state)
        S0: begin
            if (seq_in) 
                next_state = S1;
            else 
                next_state = S0;
                end
        S1: begin
            if (seq_in) 
                next_state = S1;
            else 
                next_state = S2;
        end
        S2: begin
            if (seq_in) 
                next_state = S3;
            else 
                next_state = S0;
        end
        S3: begin
            if (seq_in) 
            begin
                next_state = S1;
                detected = 1;
                end
            else 
                next_state = S2;
        end
        
        default: 
            next_state = S0;
    endcase
end
endmodule

![mealy](https://github.com/user-attachments/assets/af172726-c73b-447d-91a4-1b25e96242a9)

Testbench for Sequence Detector (Moore FSMs)

// sequence_detector_tb.v
`timescale 1ns / 1ps
module seqdetector_moore_tb;
reg clk;
reg reset;
reg seq_in;
wire detected;

// Instantiate the sequence detector module
seqdetector_moore uut (
    .clk(clk),
    .reset(reset),
    .seq_in(seq_in),
    .detected(detected)
);

// Clock generation
initial begin
    clk = 0;
    forever #5 clk = ~clk;  // Clock period of 10 time units
end

// Test sequence
initial begin
    // Initialize inputs
    reset = 1;
    seq_in = 0;
    
    // Apply reset
    #10;
    reset = 0;

    // Apply test sequence
    #10 seq_in = 1;  // Apply '1'
    #10 seq_in = 0;  // Apply '0'
    #10 seq_in = 1;  // Apply '1'
    #10 seq_in = 1;  // Apply '1'
    #10 seq_in = 0;  // Apply '0'

    // Test with some other values
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 seq_in = 1;
    #10 seq_in = 1;
    
    // Finish the simulation
    #50 $finish;
end

// Monitor signals
initial begin
    $monitor("Time=%0t | reset=%b | seq_in=%b | detected=%b", $time, reset, seq_in, detected);
end

endmodule

![Screenshot 2024-10-15 173554](https://github.com/user-attachments/assets/0d8088bd-58f2-4b67-992c-76061041acc8)

Testbench for Sequence Detector (Mealy FSMs)

module mealy_seq_detector_tb;
reg clk;
  reg reset;
  reg seq_in;

  // Outputs
  wire detected;

  // Instantiate the module under test
  mealy_seq_detector dut (
    .clk(clk),
    .reset(reset),
    .seq_in(seq_in),
    .detected(detected)
  );

  // Clock generation
  always #5 clk = ~clk;

  // Testbench code
  initial begin
    // Initialize inputs
    clk = 0;
    reset = 1;
    seq_in = 0;

    // Reset the module
    #10 reset = 0;

    // Test case 1: Detect sequence "101"
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 seq_in = 1;
    #10 $display("Sequence 101 detected: %b", detected);

    // Test case 2: No sequence detected
    #10 seq_in = 0;
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 $display("No sequence detected: %b", detected);

    // Test case 3: Sequence partially detected
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 seq_in = 0;
    #10 $display("Sequence partially detected: %b", detected);

    // Finish the simulation
    #10 $finish;
  end
endmodule

![Screenshot 2024-10-16 183459](https://github.com/user-attachments/assets/70b5878f-43ae-4858-b76a-525c32aa01ae)


Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
