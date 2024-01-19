## state machine

```systemVerilog
module tripple_1_det (output logic x, input logic clk, b, reset);

enum {start, one, two} state, next_state;

logic temp_x;

always_comb
	begin: out 
	temp_x = 0;
	if (b && state == two) 
		temp_x = 1;
	end

always_comb
	begin: states
	next_state = start;
	case (state)
	start	: next_state = b ? one: start;
	one	: next_state = b ? two: start;
	two	: next_state = b ? two: start;
	endcase
	end

always_ff @(posedge clk, negedge reset)
	begin
	state <= !reset ? start: next_state;
	x <= temp_x;
	end
	
	
endmodule
```

## textbench

```systemVerilog
module tripple_1_det_tb;

logic x, clk, b, reset;
tripple_1_det detector (.*);

logic [0:14] b_array = 15'b010110111011111;

initial
	begin
	clk = 0;
	reset = 0;
	#10ps reset = 1;
	for (int i = 0; i < 15; i++)
		@(negedge clk) #80ps b = b_array[i];
	end

always #100ps clk = !clk;

endmodule
```

### waveforms of testbench (+ state of the detector)

<img width="1440" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/44ba82f7-abac-4a96-bb12-8d32b03d0224">

## discussions

'X' is a synchronous output and temp_x stores the value that should be asserted to x in the next rising edge of the clock. (Just like the error output in the [parity checker](https://github.com/Raptor2718/SystemVerilog-log/blob/main/parity_checker.md).
But x_temp would take up another storage unit (a flip flop). Combining the output logic with the synchronisation behavioural block would save the need for x_temp. 
The need for a synchronous output is expected because x is infact a Mealy output and is therefore linked to a state transition and not a state itself. (This explains the observations in the [parity checker](https://github.com/Raptor2718/SystemVerilog-log/blob/main/parity_checker.md) as well)

### revised state machine

```systemverilog
module tripple_1_det (output logic x, input logic clk, b, reset);

enum {start, one, two} state, next_state;

always_comb
	begin: states
	next_state = start;
	case (state)
	start	: next_state = b ? one: start;
	one	: next_state = b ? two: start;
	two	: next_state = b ? two: start;
	endcase
	end

always_ff @(posedge clk, negedge reset)
	begin
	state <= !reset ? start: next_state;
	x <= (b && state == two);
	end
	
	
endmodule
```

## description of hardware

```systemverilog
module descriptive_tripple_1_det (output logic x, input logic b, clk, reset);

logic s1_b, s1_b_s0, s0, s1;

and and0 (s1_b, s1, b);
and and1 (s1_b_s0, s1_b, s0);

dff dff0 (s0, s1_b, clk, reset);
dff dff1 (s1, b, clk, reset);
dff dffx (x, s1_b_s0, clk, reset);

endmodule
```

And as expected, this works the same:

<img width="1223" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/8995f113-d2bb-4e40-873c-d30d917a6b42">



