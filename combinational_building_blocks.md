## 3 to 8 decoder using...

### boolean operators

```SystemVerilog 
module bool3to8 (output logic [7:0] x, input logic [2:0] y);

always_comb
begin
x[0] = ~y[2] & ~y[1] & ~y[0];
x[1] = ~y[2] & ~y[1] &  y[0];
x[2] = ~y[2] &  y[1] & ~y[0];
x[3] = ~y[2] &  y[1] &  y[0];
x[4] =  y[2] & ~y[1] & ~y[0];
x[5] =  y[2] & ~y[1] &  y[0];
x[6] =  y[2] &  y[1] & ~y[0];
x[7] =  y[2] &  y[1] &  y[0];
end

Endmodule
```

### conditional operator
```SystemVerilog 
module conditional3to8 (output logic [7:0] x, input logic [2:0] y);

always_comb begin
case (y)
0 : x = 1;
1 : x = 2;
2 : x = 4;
3 : x = 8;
4 : x = 16;
5 : x = 32;
6 : x = 64;
7 : x = 128;
endcase
end
Endmodule
```

### shift operator
```SystemVerilog 
module shift3to8 (output logic [7:0] x, input logic [2:0] y);

always_comb 
x = 8'b1 << y;

endmodule
```
<img width="1354" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/f04c744a-4c3c-4c62-a08e-567b71b99813">  

      
## 2n to n priority encoder. 

```SystemVerilog
module priority_encoder #(parameter N = 4)(output logic [$clog2(N)-1:0] y, logic valid, input logic [N-1: 0] x);

always_comb begin

if (x == 'b0) valid = 0;

for (int i = N-1; i >= 0; i = i - 1) begin
	if (x[i]=='1) begin
	y = i;
	valid = 1;
	break;
	end
end
end
Endmodule
```
### testbench
<img width="1352" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/e9433706-e9b5-4424-8298-eeffea98cab9">  

## n-input multiplexer


```SystemVerilog
module muxN #(parameter N = 4) (output logic y, input logic [N-1:0] x, logic [$clog2(N)-1:0] select);

always_comb begin

y = x[select];

end

endmodule
```
<img width="1354" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/7fb153ac-af5f-4822-a1bc-33cfc5849ce9">

## n-bit iterative comparator

```SystemVerilog

module ripple_comparator #(parameter N = 3) (output logic eqo, input logic [N-1:0] x, y, logic eqi);

logic [N-1: 1] eqn;
genvar i;

task automatic comp (output logic eqo_t, input logic x_t, y_t, eqi_t);
eqo_t = ~(x_t ^ y_t) & eqi_t;
endtask

always_comb
comp (eqn[1], x[0], y[0], eqi);

generate for (i = 1; i < N-1; i++) begin
always_comb 
comp (eqn[i+1], x[i], y[i], eqn[i]);
end
endgenerate

always_comb
comp (eqo, x[N], y[N], eqn[N-1]);

endmodule
```
