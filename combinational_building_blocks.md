**4.3 Write SystemVerilog models ofa3to8decoder using (a) Boolean operators,
(b) a conditional operator, and (c) a shift operator. Write a testbench to
compare the three versions. **

```SystemVerilog 
module decoder3to8 (output logic [7:0] x, input logic [2:0] y);

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

Conditional3to8

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

Shift3to8

```SystemVerilog 
module shift3to8 (output logic [7:0] x, input logic [2:0] y);

always_comb 
x = 8'b1 << y;

endmodule
```
<img width="1354" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/f04c744a-4c3c-4c62-a08e-567b71b99813">

4.4 Write a SystemVerilog model of a 2n to n priority encoder. 


4.5 Write a model of an n-input multiplexer. Write a suitable testbench. 


4.6 A comparator is used to to determine whether two signals have equal values.
A one-bit comparator is described by
eqo = ˜(x ˆ y) & eqi;
where eqi is the result of the comparison of other bits and eqo is passed to the
next comparison operation. Write a model of an n-bit iterative comparator.
