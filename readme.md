# Project: SHA3
**Name:** Trevor(Yuzhe) Zhang 
**NetID:** yz972
**course:** ECE 557
**date:** 2/15/2025
## The way to run it:
Put the design file in main\scala\sha3\

Put the test file in test\scala\sha3\

Run the test files, and they will verify the functionality.

## Important Information:
For the part 2, the testbench has been modified to accomendate for the C version result, so 
before starting to verify the functionality, the testbench's hash array should be replaced.

## Project introduction:
This part implements ctrl, dpath and top modules for the SHA-3 hashing algorithm:

ctrl module: A finite state machine to implement the reading data, excution, and output process.
dpath module: A data path manipulating for data xor, theta, RhoPi, Chi, Iota process.
top module: A top level to connect the stimulation with ctrl and dpath module.

### Control Module (CtrlModule):

**Inputs and Outputs declaration**
**Port Name**|**Input/output**|**Description**
| ---------- | -------------- | --------------- | 
|message_val|Input|Valid signal for input message data|
|message_rdy|Output|Ready signal for input message data|
|hash_val|Output|Valid signal for output hash data|
|hash_rdy|Input|Ready signal for output hash data|
|xor_start|Output|Start signal for XOR operations|
|keccak_start|Output|Start signal for Keccak operations|
|read_done|Input|Signal indicating data read completion|
|xor_done|Input|Signal indicating XOR operation completion|
|keccak_done|Input|Signal indicating Keccak operation completion|
|hash_done|Input|Signal indicating hash output completion|


this module manages the overall control flow of the SHA-3 process. The design divides the algorithm into six states:

  s_read_data: Reads input data.

  s_XOR_1: Performs the first XOR operation.(C_code update part)

  s_keccak_1: Executes the first Keccak round.(C_code update part)

  s_XOR_2: Performs the second XOR operation.(C_code final part)

  s_keccak_2: Executes the second Keccak round.(C_code final part)

  s_output: Outputs the final hash.

Each state transitions based on specific done signals received from the data path module.


### Data Path Module (DpathModule):

**Inputs and Outputs declaration**
**Port Name**|**Input/output**|**Description**
| ---------- | -------------- | --------------- | 
|message_in|Input|Input data vector for message|
|hash_out|Output|Output vector for computed hash|
|message_rdy_control|Input|Control signal for message ready|
|message_val|Input|Valid signal for input message data|
|hash_rdy|Input|Ready signal for output hash data|
|hash_val_control|Input|Control signal for hash valid|
|xor_start|Input|Start signal for XOR operations|
|keccak_start|Input|Start signal for Keccak operations|
|read_done|Output|Data read completion signal|
|xor_done|Output|XOR operation completion signal|
|keccak_done|Output|Keccak operation completion signal|
|hash_done|Output|Hash output completion signal|

this module handles data processing and the core SHA-3 operations:

  State Matrix: 25x64-bit register array for the state.

  Message Register: Stores input data for processing.

  Keccak Rounds: Implements two rounds of Keccak, using four submodules:

    ThetaModule

    RhoPiModule

    ChiModule

    IotaModule

  Pipelined Design: The Keccak operation is divided into four pipeline stages (Theta -> RhoPi -> Chi -> Iota(State_Renew)).

Outputs: Provides the final hash output after two rounds of Keccak.

### Top-Level Module (Sha3Accel):

**Inputs and Outputs declaration**
**Port Name**|**Input/output**|**Description**
| ---------- | -------------- | --------------- | 
|message|Input|DecoupledIO interface for input message|
|hash|OutputDecoupledIO interface for output hash|

this module integrates the control and data path modules and provides the interface for external communication:

Uses DecoupledIO interfaces for input messages and output hashes.

Connects control signals between CtrlModule and DpathModule.

Handles data flow between input, processing, and output stages.

### Design Validation:

A testbench is written to verify the correctness of the module. However, it actually asserts the result without involving the Final part of C version. Hence, Here we make a little modification to the hash array:

![image-2](https://github.com/user-attachments/assets/f9369b7c-6b23-40d9-ba04-ec3c674250dc)

the test result are show as below:(The testbench has been modified)

![image-1](https://github.com/user-attachments/assets/0d084093-7eb9-4b56-bb60-f33338473f8a)

There is no failure report for valid and done signal, so their functionality are also correct.

The waveform is shown as below. The st is the matrix state, which provides the finsh hash result for output port:
![top_level_Wave](https://github.com/user-attachments/assets/998180e4-8e7b-4d5f-86a8-ef3909374e79)


