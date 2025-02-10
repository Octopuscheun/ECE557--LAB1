# Project: SHA3
**Name:** Trevor(Yuzhe) Zhang 
**NetID:** yz972
**course:** ECE 557
**date:** 2/9/2025
## The way to run it:
Put the design file in main\scala\sha3\

Put the test file in test\scala\sha3\

Run the test files, and they will verify the functionality.

## Project introduction:
This project implements two essential transformation modules for the SHA-3 hashing algorithm:

Chi (χ) Transformation: A non-linear bitwise operation applied to a 5×5 state matrix.
Theta (θ) Transformation: A diffusion step that spreads bits across the state matrix.

**Inputs and Outputs declaration**
**Port Name**|**Input/output**|**Description**
| ---------- | -------------- | --------------- | 
|state_i|Input|5×5 state input vector (default 64-bit width)|
|state_o|Output|5×5 transformed state output vector|

### Port Declaration:
state_i: Represents the 5×5 Keccak state input.
state_o: Stores the result after applying the χ or θ transformation.
### Chi Module:
Chisel implementation:

```
val bc = Wire(Vec(25, UInt(W.W)))
for (j <- 0 until 5) {
  for (i <- 0 until 5) {
    bc(j * 5 + i) := io.state_i(j * 5 + i)
    io.state_o(j * 5 + i) := io.state_i(j * 5 + i) ^ (~bc((j * 5) + (i + 1) % 5) & bc((j * 5) + (i + 2) % 5))
  }
}

```
This Chisel modules is implemented according to its C version. 
### Design Validation:
A testbench is written to verify the correctness of the module, the basic test algorithm is same to the C version implementation.

the test result are show as below:

![image](https://github.com/user-attachments/assets/51de0e28-eb1e-44f2-b385-2ae46020f082)


```
// calculate groundtruth outputs(direct translation of C code)
        var st = state.clone
        var bc = Array.fill(5){BigInt(0)}
       for(j <-0 until 25 by 5)
       {
          for(i <-0 until 5)
          {
            bc(i) = st(j+i)
          }
          for(i<-0 until 5)
          {
            st(j+i) ^= (~bc((i+1)%5)) & bc((i + 2) % 5)
          }
       }
       val out_state = st
       //peekpoke testing
       for(i <-0 until 25)
       {
         c.io.state_i(i).poke(state(i))
       }
       c.clock.step(1)
       for(j <- 0 until 25)
       {
         c.io.state_o(j).expect(out_state(j))
       }
```

There is no failure report for valid and done signal, so their functionality are also correct.

### Theta Module:
Chisel implementation:

```

for (i <- 0 until 5)
  {
    bc(i) := io.state_i(i)^io.state_i(i+5)^io.state_i(i+10)^io.state_i(i+15)^io.state_i(i+20)
  }
  for(i <- 0 until 5)
  {
   t(i):= bc((i+4)%5) ^ ROTL(bc((i+1) % 5),1,W)
  }
  
  for(i <- 0 until 5){
    for(j <- 0 until 5)
    {
       io.state_o(i+j*5) := io.state_i(i+j*5) ^ t(i)
    }
  }

```
This Chisel modules is implemented according to its C version. 
### Design Validation:
A testbench is written to verify the correctness of the module, the basic test algorithm is same to the C version implementation.

the test result are show as below:(The testbench has been provided)

![image](https://github.com/user-attachments/assets/51de0e28-eb1e-44f2-b385-2ae46020f082)

There is no failure report for valid and done signal, so their functionality are also correct.
