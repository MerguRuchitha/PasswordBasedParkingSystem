module PasswordBasedParkingSystem(
    input [3:0] password,       // 4-bit password input
    input reset,                // reset signal
    input enter,                // signal to check password
    output reg gate_open        // output to control the gate
);

    reg [3:0] stored_password;  // stored password

    always @(posedge enter or posedge reset) begin
        if (reset) begin
            stored_password <= 4'b1010;   // preset password
            gate_open <= 0;               // initially gate is closed
        end
        else begin
            if (password == stored_password) begin
                gate_open <= 1;           // open the gate
            end
            else begin
                gate_open <= 0;           // keep the gate closed
            end
        end
    end
Endmodule

module Testbench;

    reg [3:0] password;         // 4-bit password input
    reg reset;                  // reset signal
    reg enter;                  // signal to check password
    wire gate_open;             // output to control the gate

    // Instantiate the parking system module
    PasswordBasedParkingSystem uut (
        .password(password),
        .reset(reset),
        .enter(enter),
        .gate_open(gate_open)
    );

    initial begin
        // Initialize inputs
        reset = 1;
        password = 4'b0000;
        enter = 0;

        // Wait for the reset to complete
        #10 reset = 0;
        #10 enter = 1; password = 4'b1010; // Correct password
        #10 enter = 0;
        #10 enter = 1; password = 4'b1111; // Incorrect password
        #10 enter = 0;
        #10 enter = 1; password = 4'b1010; // Correct password
        #10 enter = 0;

        // Finish simulation
        #10 $finish;
    end

    initial begin
        $monitor("Time: %0d | Password: %b | Gate Open: %b", $time, password, gate_open);
    end
endmodule
