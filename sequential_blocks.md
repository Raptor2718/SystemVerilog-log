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

```SystemVerilog
module tff_n (output logic q, qbar, input logic t, clk, reset_n);

always_ff @(negedge clk, negedge reset_n) 

if (reset_n) 
	{q, qbar} = 'b01;
else if (t)
	{q, qbar} = {qbar, q};

endmodule
```

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
