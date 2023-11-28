
5.2 Write a behavioral SystemVerilog model of a negative edge-triggered D
flip-flop with asynchronous active-low set and synchronous active-high reset.

      
```SystemVerilog 
module dff (output logic q, input logic d, clk, as_set_l, sy_reset_h);

always_ff @(negedge clk, negedge as_set_l) begin

if (~as_set_l) 
	q <= 1;
else if (sy_reset_h)
	q <= 0;
else
	q <= d;

end

endmodule
```
<br>
<br>
5.3 Write a SystemVerilog model of a negative edge-triggered T-type flip-flop.       

```SystemVerilog
module tff_n (output logic q, qbar, input logic t, clk, reset_n);

always_ff @(negedge clk, negedge reset_n) 

if (reset_n) 
	{q, qbar} = 'b01;
else if (t)
	{q, qbar} = {qbar, q};

endmodule
```
<br>
<br>
5.4 Write a SystemVerilog model of a 10-state synchronous counter that asserts
an output when the count reaches 10.

```SystemVerilog
module counter10 (output logic [3:0] count, logic reached10, input logic clk, reset);

always_ff @(negedge clk, posedge reset) begin

if (reset)
	count <= 'b0;
else if (count == 'b1001) begin
	count <= 'b0;
	reached10 <= 1; end
else 
	count <= count + 1;
end 

endmodule
```


<img width="1235" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/6caa8d35-50bf-4531-b045-fea9ac4ab26b">
<br>
<br>

5.5 Write a SystemVerilog model of an N-bit counter with a control input “Up.”
When the control input is 1, the counter counts up; when it is 0, the counter
counts down. The counter should not, however, wrap round. When the all 1s
or all 0s states are reached, the counter should stop.

```Systemverilog
module ncounter #(parameter N = 3) (output logic [N-1:0] count, input logic clk, up, reset);

always_ff @(posedge clk, posedge reset) begin

if 	(reset)	count <= 0;
else if	(up  && count < ('b1<< N)-1)	count <= count + 1; 
else if (~up && count > 'b0 )		count <= count - 1;

end
endmodule
```

<img width="1349" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/a930606f-caf4-488e-825b-09457767ddb3">
<br>
<br>
5.6 Write a SystemVerilog model of an N-bit parallel to serial converter.

```systemverilog
module npiso #(parameter N = 3) (output logic [N-1:0] q, input logic clk, pl, [N-1:0] p);

always_ff @(posedge clk) begin
if (pl) q <= p;
else q = q << 1;
end

endmodule
```
<br>
5.7 Write a SystemVerilog testbench for this parallel to serial converter.
<img width="1005" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/17c05daa-2506-4afc-ba73-11c677a91d82">

```systemverilog
module johnsoncounter #(parameter N = 4) (output logic [N-1:0] count, input logic clk, reset);

always_ff @(posedge clk, posedge reset) 
if (reset)
	count <= 0;
else
	count <= {count[N-2:0], ~count[N-1]};

endmodule
```
5.8 What are the advantages and disadvantages of ripple counters as opposed to
synchronous counters?
- internal delays in each counter gets added. The delay gets larger as the counter does.
<br>
<br>
5.9 Design a synchronous Johnson counter that visits eight distinct states in
sequence. How would this counter be modified such that any unused states
lead, eventually, to the normal counting sequence?

```systemverilog
module johnsoncounter #(parameter N = 4) (output logic [N-1:0] count, input logic clk, reset);

always_ff @(posedge clk, posedge reset) 
if (reset)
	count <= 0;
else
	if (~count[N-1] && ~count[0])
		count <= {{(N-1){1'b0}}, 1'b1};
	else
		count <= {count[N-2:0], ~count[N-1]};

endmodule

```

<img width="1109" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/305d3293-ecd0-4425-8e57-5978600c2e2c">


