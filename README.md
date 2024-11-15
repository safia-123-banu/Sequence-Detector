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

// moore_sequence_detector.v
module moore_sequence_detector (
    input clk,
    input reset,
    input seq_in,
    output reg detected
);

   
    localparam S0 = 3'b000, 
               S1 = 3'b001, 
               S2 = 3'b010, 
               S3 = 3'b011, 
               S4 = 3'b100;
    reg [2:0] current_state, next_state;
    always @(posedge clk or posedge reset) 
    begin
        if (reset)
            current_state <= S0;  
        else
            current_state <= next_state;  
    end

    
    always @(*) 
    begin
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
                    next_state = S4;
                else 
                    next_state = S2;
            end
            S4: begin
                if (seq_in) 
                    next_state = S1;
                else 
                    next_state = S0;
                detected = 1;  
            end
            default: next_state = S0;
        endcase
    end
endmodule




![Screenshot 2024-10-10 140432](https://github.com/user-attachments/assets/18416953-d43e-4328-a612-25a1fad35e1d)






Verilog Code for Sequence Detector Using Mealy FSM

// mealy_sequence_detector.v
module mealy_sequence_detector (
    input wire clk, 
    input wire reset, 
    input wire seq_in, 
    output reg detected
);
    localparam S0 = 3'b000, 
               S1 = 3'b001, 
               S2 = 3'b010, 
               S3 = 3'b011;
    reg [2:0] current_state, next_state;
    always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;  
        else
            current_state <= next_state;  
    end


    always @(*) begin
        detected = 0;  
        next_state = current_state;  

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
                if (seq_in) begin
                    next_state = S1;
                    detected = 1;  
                end 
                else
                    next_state = S2;
            end
            default: next_state = S0;
        endcase
    end
endmodule







![Screenshot 2024-10-10 144107](https://github.com/user-attachments/assets/b3adee44-cf99-4289-88b4-3ddae73628aa)





Testbench for Sequence Detector (Moore and Mealy FSMs)
`timescale 1ns / 1ps // Specify the time scale
module tb_moore_sequence_detector;
    reg clk;
    reg reset;
    reg seq_in;
    wire detected;

    // Instantiate the Moore sequence detector
    moore_sequence_detector uut (
        .clk(clk),
        .reset(reset),
        .seq_in(seq_in),
        .detected(detected)
    );

    // Clock generation
    always #5 clk = ~clk; // Toggle clock every 5 ns

    initial begin
        // Initialize signals
        clk = 0;
        reset = 1;
        seq_in = 0;
        #10 reset = 0; // Release reset after 10 ns
        
        // Test sequence (example: 110)
        seq_in = 0; #10; // State S0
        seq_in = 1; #10; // State S1
        seq_in = 1; #10; // State S2
        seq_in = 0; #10; // State S3
        seq_in = 1; #10; // State S4 (detected)
        seq_in = 0; #10; // State S0
        seq_in = 1; #10; // State S1
        seq_in = 0; #10; // State S2
        seq_in = 1; #10; // State S3
        seq_in = 1; #10; // State S4 (detected)

        // End simulation
        #20 $stop; 
    end
endmodule

`timescale 1ns / 1ps // Specify the time scale

module tb_mealy_sequence_detector;
    reg clk;
    reg reset;
    reg seq_in;
    wire detected;

    // Instantiate the Mealy sequence detector
    mealy_sequence_detector uut (
        .clk(clk),
        .reset(reset),
        .seq_in(seq_in),
        .detected(detected)
    );

    // Clock generation
    always #5 clk = ~clk; // Toggle clock every 5 ns

    initial begin
        // Initialize signals
        clk = 0;
        reset = 1;
        seq_in = 0;
        #10 reset = 0; // Release reset after 10 ns
        
        // Test sequence (example: 110)
        seq_in = 0; #10; // State S0
        seq_in = 1; #10; // State S1
        seq_in = 1; #10; // State S2
        seq_in = 0; #10; // State S3
        seq_in = 1; #10; // State S1 (detected)
        seq_in = 0; #10; // State S2
        seq_in = 1; #10; // State S3 (detected)
        seq_in = 0; #10; // State S0
        seq_in = 1; #10; // State S1

        // End simulation
        #20 $stop; 
    end
endmodule





![Screenshot 2024-10-17 121117](https://github.com/user-attachments/assets/2a843ddb-8c10-4e9b-89f0-6bb9fe6f2151)





Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
