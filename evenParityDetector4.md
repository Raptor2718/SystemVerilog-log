## Even parity detector with n = 4

on the 4th clock cycle, an asynchronous output ```error``` was asserted if the input changes such that the total number of 1's in the sequence of 4 bits was odd. But this was faulty bacause in the testbench, I set the inputs to change 1/4 cycle before the transition and remain for 1 cycle. So the third bit will remain for 3/4th of the 4th cycle, causing an error.
Here, the sequence, '0110' is sent between 160ps and 240ps with each bit lasting 20ps. The fault mentioned above causes an error. 
<img width="1231" alt="image" src="https://github.com/Raptor2718/SystemVerilog-log/assets/106425621/e6f11644-0217-47e2-a27c-6462ce072e70">


