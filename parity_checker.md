
## parity checker:

```systemverilog

module spc4 (output logic error, input logic b, clk, reset);

enum {start, o1, o2, o3, e1, e2, e3} state, next_state;

logic temp_error;
always_comb
begin: out
	temp_error = 0;
	case (state)
	e3: temp_error = b;
	o3: temp_error = !b;
	endcase
end

always_comb
begin: nextstate
	next_state = start;
	case (state)
	start	: next_state = b ? o1: e1;
	o1	: next_state = b ? e2: o2;
	e1	: next_state = b ? o2: e2;
	o2	: next_state = b ? e3: o3;
	e2	: next_state = b ? o3: e3;
	endcase
end;

always_ff @(posedge clk, negedge reset)
begin
	if (!reset) 
	state <= start;
	else 
	state <= next_state;
	error <= temp_error;
end

endmodule
```

## testbench:

```systemverilog
module spc4_tb; 

logic error;
logic b, clk, reset;
logic [3:0] b_array = 4'b0; 		

spc4 parity_checker (.*);

initial
begin
clk = '0;
reset = 0;
#5ps reset = 1;
for (b_array = 0; b_array < 16; b_array++) 
begin
for (int i = 0; i < 4; i++) 
	begin
	wait (clk == 0) b = b_array[i];
	wait (clk == 1);
	end
end
end
always #50ps clk = !clk;

endmodule
```

### Waveforms of testbench (+ state of the parity checker):

	
<img width="1299" alt="Screenshot 2023-12-16 at 23 59 41" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/9eac5b4a-1aca-44eb-aacb-378f9a994188">

# An interesting issue I came across...

* The output ```error``` wasn't synchronous. So according to the output evaluation combinational logic;
```systemverilog
always_comb
begin: out
	error = 0;
	case (state)
	e3: error = b;
	o3: error = !b;
	endcase
end
```
An error might appear regardless of the 4th bit as soon as the 3rd state is entered. The combinational logic will consider the current bit (3rd bit) and the 3rd state entered (```e3``` or ```o3```) as inputs and evaluate ```error```. This is wrong because the 4th bit (parity bit) hasn't even been considered!  
The parity bit is to occur somewhere in the third clock cycle (after entering the third state and before the next rising edge). So storing the ```error``` evaluated via the combinational logic, and asserting it only at the 4th rising edge (so when the state changes to ```start```), allows time for the parity bit to occur and be considered in evaluating ```error```. And this fixed the issue.

## My working for the design

![IMG_2834](https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/c0eddd25-83d4-4e82-8c6b-56be70eab5b6)

    
