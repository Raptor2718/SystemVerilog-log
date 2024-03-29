## Even parity detector with n = 4
```SystemVerilog
module evenParityChecker4 (output logic error, input logic clk, rst, inp);

enum {a,b,c,d,e,f,g} p_state, n_state;

always_ff @(posedge clk, negedge rst)
	begin
	if (~rst)
		p_state <= a;
	else 
		p_state <= n_state;
	end

always_comb
	begin
	error = 0;
	n_state = a;
	case (p_state)
	a : n_state = inp ? b : c;
	b : n_state = inp ? e : d;
	c : n_state = inp ? d : e;
	d : n_state = inp ? g : f;
	e : n_state = inp ? f : g;
	f : error = inp ? 0 : 1;
	g : error = inp ? 1 : 0;
	endcase
	end

endmodule
```

on the 4th clock cycle, an asynchronous output ```error``` was asserted if the input changes such that the total number of 1's in the sequence of 4 bits was odd. But this was faulty bacause in the testbench, I set the inputs to change 1/4 cycle before the transition and remain for 1 cycle. So the third bit will remain for 3/4th of the 4th cycle, causing an error. This wouldn't be a problem if the bits were sampled at the end of the bit period.
Here, the sequence, '0110' is sent between 160ps and 240ps with each bit lasting 20ps. The fault mentioned above causes an error. 

  
<img width="1231" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/e6f11644-0217-47e2-a27c-6462ce072e70">

  
the 4th bit would occur during the 4th cycle (so before the proceeding transition) So, as in the case of the [one chain detector](https://github.com/Raptor2718/SystemVerilog-log/blob/main/one_chain_detector.md), I made the ```error``` synchronous and be asserted in the transition proceeding the cycle in which the system is in the state linked to the Mealy output.

```SystemVerilog
module evenParityChecker4 (output logic error, input logic clk, rst, inp);

enum {a,b,c,d,e,f,g} p_state, n_state;
logic n_error;

always_ff @(posedge clk, negedge rst)
	begin
	if (~rst)
		p_state <= a;
	else 
		p_state <= n_state;
		error <= n_error;
	end

always_comb
	begin
	n_error = 0;
	n_state = a;
	case (p_state)
	a : n_state = inp ? b : c;
	b : n_state = inp ? e : d;
	c : n_state = inp ? d : e;
	d : n_state = inp ? g : f;
	e : n_state = inp ? f : g;
	f : n_error = inp ? 0 : 1;
	g : n_error = inp ? 1 : 0;
	endcase
	end

endmodule

```
<img width="1186" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/5287f870-6ad2-4299-a066-ffe5552edac4">

## Another bit sequence detector

```z``` is asserted when a sequence of ```a``` = 1 and ```b``` = 1 -> ```a``` = 1 and ```b``` = 0 -> ```a``` = 0 and ```b``` = 0 is detected. 
```z``` is asynchronous this time as the previous bit can't assert ```z``` faultily (because each combination of the sequence is unique).

```SystemVerilog

module seqDet (output logic z, input logic a, b, clk, rst);

enum {s1, s2, s3} p_state;

always_ff @(posedge clk, negedge rst)
	begin
	if (~rst) 
		p_state <= s1;
	else 
		begin
		case (p_state)
		s1: 
		if ({a,b} == 'b11) 	p_state <= s2;
		s2: 
		if ({a,b} == 'b10) 	p_state <= s3;
		else 			p_state <= s1;
		s3: 
		if ({a,b} == 'b11)      p_state <= s2;
		else 			p_state <= s1;
		endcase
		end
	end

always_comb 
	begin
	z = '0;
	if (p_state == s3 && {a,b} == 'b00)
		z = '1;
	end

endmodule
```

<img width="1054" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/4c310092-9edb-45cc-bb5d-7290a6192f4c">

