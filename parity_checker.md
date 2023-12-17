waveforms of testbench (+ state of the parity checker):

	
<img width="1299" alt="Screenshot 2023-12-16 at 23 59 41" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/9eac5b4a-1aca-44eb-aacb-378f9a994188">

<br>
spc4.sv:
<br>

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
