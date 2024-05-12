# SFAL-VSD Program



<details> 

<summary> DAY 0 </summary>
## DAY-0
### Installing YOSYS

```
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys 
$ sudo apt install make (If make is not installed please install it) 
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make 
$ sudo make install
```

![Screenshot from 2024-04-27 21-20-21](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/293bb95e-ed24-4377-b4f3-3ff0b4e641b6)






### Installing iverlog
```
$ sudo apt-get install iverilog

```
![Screenshot from 2024-04-20 21-37-48](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/314bff04-2f23-45af-9cf1-3613d2de56a0)







### GTKwave
```
$ sudo apt update
$ sudo apt install gtkwave
```
![Screenshot from 2024-04-20 21-54-09](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/4bc90917-29f8-48cc-96fd-c601dd75080b)

</details> 



<details> 
<summary> DAY 1 Introduction to Verilog RTL Design and Synthesis </summary>
## Day 1 - Introduction to Verilog RTL Design and Synthesis

# Introduction to  iverilog

Iverilog simulator
The simulator is the tool that will be used to check the design according to the specs.

The test bench will contain:

* a Stimulus generator
* the design (instantiated)
* Stimulus observer

<img width="762" alt="iverilog" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/5e5f92df-ee42-43c4-b87f-5a2864659ff2">

-The test bench does not have primary inputs and outputs.

```
`timescale 1ns / 1ps
module tb_good_mux;
  // Inputs
  reg i0,i1,sel;
  // Outputs
  wire y;

      // Instantiate the Unit Under Test (UUT)
  good_mux uut (
  	.sel(sel),
  	.i0(i0),
  	.i1(i1),
  	.y(y)
  );

  initial begin
  $dumpfile("tb_good_mux.vcd");
  $dumpvars(0,tb_good_mux);
  // Initialize Inputs
  sel = 0;
  i0 = 0;
  i1 = 0;
  #300 $finish;
  end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```

#### Introduction to gtk wave

#### Intro logic synthesis 



<img width="611" alt="logic synthesis" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/106e9b17-3868-4849-bf8e-bf5ab8110c0b">

The generated netlist (verilog file) must be verified by comparing the behaviour with the RTL behaviour -> the waveforms should be the same - the same test bench can be used.
The front end library contains a collection of gate components like nand, nor etc. with different configurations, working speeds or other physical parameters. The parameters described in the libraries will model the design behaviour and SETUP, HOLD time or performance can be estimated more precisely.
The propagation delay is influenced also by the charging and discharging of the capacitance/loads, this will need an optimization of the cell size and cell number.

Faster cells - less delay, wider transistor, more area and power, possible hold violations Slower cells - more delay, narrow transistor, less area and power, setup and performance violations

</details> 

 <details> 
<summary> DAY 2 - Timing libs, Hierarchical vs Flat Synthesis </summary>
## DAY 2 - Timing libs, Hierarchical vs Flat Synthesis

Working with libs...

The following are some of the Timing Libs 
Design Inputs:
- Verilog Netlist (.v)
- Timing Constraints files (.sdc)
- Scan Chain File (.def)
Technology Inputs:
- Tech File (.tf or .left)
- Physical Libraries (Milky Way or .left)
- Timing (Liberty) Libraries (.db or .lib)
- RC Extraction Models (.tlupus(Synopsis, CapTables (Cadence), PTF (Mentor))
- Signoff RC Extraction Models (.nxtgrd)

  
![Screenshot from 2024-04-27 21-47-43](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/de271711-baed-4eb8-944a-5f10f5eb21e2)


### Hierarchical vs Flat Synthesis

A hierarchical design contains sub-modules identified in the picture below.

![Screenshot from 2024-04-27 21-59-20](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/652df9df-6c4e-4898-8c02-8dbc15555d44)

The RTL generated from Netlist might have different gates compared to the original verilog code, but the functionality will be the same. Overall it is the tools will  optimize the circuit:

* logical (NOR+INV = NAND) = electrical - a stacked PMOS can be replaced with a circuit with stacked NMOS
flatten command eliminates the sub_modules and will generate just a big module with the same elements.

 * Sub-module instantiation synth -top <module_name>:

* The Ux sub-modules are not seen anymore in the diagram or the code
* Preferred when we have multiple instances of the same module so we synthesize just one and copy the generated netlist in the main netlist
* Massive design will not work optimized for the tools so can be divided in smaller circuits
![Screenshot from 2024-04-24 13-04-46](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/09e0a451-696b-4f46-9acb-8ab0eeb641e9)


  ### Flop Coding Styles

  Flops are needed to eliminate the possible glitches generated by the different propagation delays of the signals in the combinational circuits or in between them. The flops are like storage elements.
The flops will restrict the glitches propagation because the flop output will change just on the edge of a clock, so even the input of the flop is glitchy the output will be stable - the input of the comb circuit will be stable so also the output will be more stable.
![d2-2](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/295fe2c6-45e4-4731-9741-6e5fbd269dac)


The value of the flop must be known all the time for these signals like reset or set are used to control the initial state. This can be synchronous or asynchronous.
(ATTACH THE PICTURE)

</details>


 <details> 
<summary> DAY 3 - Combinational and Sequential Optimizations </summary>

## DAY 3 - Combinational and Sequential Optimizations

### Intro to optimizations

#### Combinational Logic Optimisation

Squeezing the logic to get the most optimised design (in terms of Area and Power savings- PPA)

Constant Propagation ---> Direct Optimisation
Advantage
Boolean Logic Optimizations: Changing the boolean expression from Complex to Simple.

#### Sequential Logic optimization 

Constant Propogation: Output can be constant irrespective of gates
Constant propagation example : 1Y=((AB)+C)' in case A=0 -> Y=((0)+C)' = (C)'
The propagation of a constant can generate a more optimized combination different than the initial one: 6 vs 2 transistors used.
![IMG_0216](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/3b5c1973-d9bf-401c-808d-760c509bb653)



Sequential optimization:
State optimization - Optimization of unused state
Cloning - When flops are far away between them, the "driving" flop A can be doubled so the overall delays between B and C can be eliminated.
![d3-3](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/2fb8f827-bf31-4e00-a9d2-06b2d69c7da0)

Retiming: Lets assume the circuit below and Clk to Q delay Setup and hold time ~ 0.
![d-4](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/3de8b9ae-f87c-4d88-9d77-124186930330)


If initially we have 2 logic cells that can work at 200Mhz and 500Mhz and overall time needed to perform 2 states is ~7ns - the max working frequency will be limited to lower 200Mhz.
If possible, the logic can be changed and move some logic parts from first cell to second one and to keep the ~7ns execution time. But with more comparable working frequencies per cell we can optimize the overall working frequency.
#### Advanced Topics:

State Optimization: Optimization of unused states
Cloning: Reusing for mapping


* Boolean optimization example :
assign y=a?(b?c:(c?a:0)):(!c)
y=a'c'+a[bc+b'ac]=a'c'+abc+ab'c = a'c'+ac[b+b'] =a'c'+ac= a xor c

This is an example of const propag, k-map and boolean optimization.
![IMG_0217](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/5c160ea0-ea2d-4968-9d3b-cd36ec8f7081)
![IMG_0218](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/383ee9ab-868f-41ab-b043-183c4919e6bf)
![IMG_0219](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/d6e4bd6b-4cbf-4370-8c72-ad4f04d78761)




## Labs: Day 3
Commands to follow:
opt_clean -purge - command to execute optimizations
```
opt_clean -purge
```

Example of a mux with an input tight to 0 -> y=a'0+b =ab

```
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
Here you can see that was optimized to an AND gate.

![Screenshot from 2024-04-24 13-04-46](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/da413ad9-9403-45b1-9966-6c4eec46a5ba)

* Opt_check3 example:

```
module opt_check3 (input a , input b, input c , output y);  
	assign y = a?(c?b:0):0;  
endmodule
```
![Screenshot from 2024-04-27 18-38-55](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7ad976b4-ec19-4f7f-b855-c82a9069b1dd)

a'+a[c'0+cb] = 0+abc = ABC

* Opt_check4 example:

  ```
  module opt_check4 (input a , input b , input c , output y);
  assign y = a?(b?(a & c ):c):(!c);
  endmodule
  ```
  y= [[ac]b+b'c]a+a'c' = abc+ab'c+a'c' = ac[b+b']+a'c' = ac+a'c'

  (ATTACH THE PIC)

  * Multiple_module_opt2 example :
     The code, hierarchical and flatten design :
    (ATTACH THE PIC)

  * Optimized design :
    (ATTACH THE PIC)



#### Sequential Logic optimization 

* Sequential logic optimization:
In some cases libraries wil contain separated libraries for flops and lathes (sequential circuits) and separate combinational circuits.

```
-dfflibmap -liberty <filepath> - command to map the sequential circuits from the library, libraries in liberty format

```
First example is a flop with reset behavior -> a flop was inferred (#DFF_PP0)
Second example second with set behavior -> no flop identified.
The second circuit is an example of seq constant optimization.


(ATTACHED THE PIC)

![Screenshot from 2024-04-27 19-08-47](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/49c57e84-1aba-4f6f-b57f-b7d5d0503e3b)


![IMG_0224](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/51e6d840-3ab2-49db-b2f9-89f765e90a25)
![IMG_0226](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/acd35f72-32a0-41b4-ba3e-6c1ad238a0bf)
![Screenshot from 2024-04-27 21-08-00](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/63e4c083-1f9a-4ccf-b098-e0226e0861df)

In the diagram, one flop is with reset and one with set . The inverters are generated because the cells are active low and the code is used like an active high signal for reset and set.

Use cases with different set/reset combinations:
(ATTACH THE PICS)
 </details> 


 <details> 
<summary>  DAY 4 - GLS, blocking vs non-blocking and Synthesis-Simulation mismatch </summary>

## DAY 4 - GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

### GLS Concepts and Flow using Iverilog

GLS: Running the test bench with Netlist as DUT( Design Under Test)
Netlist is Logically the same as RTL Code(test Bench aligns with the design)

Gate level syntesis is needed because :

* we need to verify the correctness of the design after synthesis
* RTL does not contain the notion of timing
* with specific gate level implemenation the design timing can be modeled more accurate
* GLS needs to run with delay annotation ( for this the gate level models must be timing aware)
![IMG_0208](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/2a326ce6-4794-4988-bfc5-a546ba2d5fd2)


GLS verify the logical correctness of design after synthesis and Ensures whether the timing of the design are met or not (For this GLS needs to be run with delay Annotation).

![IMG_0209](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/de5091d8-a641-4d7f-93ef-163e1ab871b7)

Synthesis Simulation Mismatch
1. Missing Sensitivity List
2. Blocking vs Non-Blocking Assignments
3. Non Standard Verilog Coding

#### Missing Sensitivity List
Missing sensitivity list:
* Simulators work based on "activity " ( a change in input will trigger a change in output ).
* When "always" blocks are specified signals are needed to trigger the changes in the design - simulation will not behave as expected.
![IMG_0210](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/477d0b4d-4e24-4778-9d72-706074a190b3)


#### Blocking and Non-Blocking Statements in Verilog
* = Blocking: Execute the statements in the order it is written - the first statement is evaluated before the second statement
* <= Non-Blocking: Parallel evaluation - executes all "right hand" statements and assigns to "left hand" statement (e.g: a<=b&c)
* It is recommended non-blocking for sequential circuits, avoid as much as possible and double check your design on paper

#### Inside Always Block
![IMG_0211](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b5e8bd1b-0853-4b1a-9115-2b49cef6855c)

Caveats with Blocking Statements

![IMG_0214](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/8e1cecfb-c85e-4965-94b4-7f08e7fa3290)

![IMG_0215](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/4a74962a-9a67-4903-9776-6bbaba00bae0)

* Non-Standard verilog coding

  ```
  module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
  
  ```
* Labs, running iverilog on GLS :

  iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v

![IMG_0229](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/26e930ae-bb19-409e-83e3-654cebfd7d80)
![IMG_0228](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/62a47aca-1547-4aba-824a-b143fce71035)


  For synthesis mismatch behavior :


    module bad_mux (input i0 , input i1 , input sel , output reg y);
    always @ (sel)
    begin
	    if(sel)
		    y <= i1;
	    else 
		    y <= i0;
    end
    endmodule
    


In the RTL simulation clearly, we can see a bad mux behavior, a flop behavior.

![IMG_0230](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/af9f4233-2461-4af5-b861-2502aa234e2b)


Blocking caveat: Intended design is `y = (a|b)&c' , code is...

```
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

![IMG_0232](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/cf00b27d-cadc-48b6-a73f-e66ff40dd16f)

</details> 



<details> 
<summary>  DAY 5 DFT, dc_shell, icc2_shell, lc_shell commands </summary>


## DAY 5 DFT, dc_shell, icc2_shell, lc_shell commands

DFT: Technique facilitates the design to become testable after production(additional design). It is generally introduced during synthesis.

Ex: Mbist logic for Macro, flops- Scan chains, combinational circuits- test patterns 

Three levels of DFT

1) Chip Level testing
2) Board Level testing
3) System Level testing

Types of DFT Techniques:

1) Ad-hoc
2) Structured


![Screenshot (61)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7d56e23e-8534-4628-ae8e-8a677ad6145e)


dc_shell is the command line interface for Synopsys's Design Compiler, which is an RTL synthesis solution that allows users to optimize power, area, timing, and test simultaneously

![Screenshot (55)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/150cd92b-48ce-421b-a5c5-6a0d1b45e2c8)
![Screenshot (57)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/73d92e68-2745-4a19-9c84-7edd7795dee5)
![Screenshot (58)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/51f5af9d-8451-40e2-b3fa-8d5656895e35)



 </details> 

 
<details> 
<summary>  DAY 6  </summary>
	
![ls1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/62853c10-dab5-4319-8fe2-7349230f517e)
![ls2](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/930042c7-637d-4cfb-85fc-058a910b1eba)
![ls3](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/3d2b116b-fcb6-47e6-8b7b-d46ccf12d51f)
![ls4](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/39f6e15f-cff4-425b-84dc-1fb23f64af4d)
![ls5](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7a1f94f1-7935-46ae-9af4-17e893a78c14)

![ls6](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/1a1420d0-3f97-4645-8d3d-3abe8a86850b)
![ls7](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/d03df6b0-ca22-47bb-996e-385920b4d091)
![ls8](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/46080c7b-6cd9-4cfd-9117-c97d80687121)
![ls9](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/87476474-7263-4d4d-8527-edb68a125277)
![ls10](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/711f06c6-3a7b-470e-8552-92d51bd6921d)
![ls11](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/eddfac38-f89e-4ddb-8fd5-ea03ee4aa2f7)
![ls12](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b23f1e44-2f39-4e3f-a2a7-e4864a709b1c)
![ls14](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/a0c6f5ce-3e92-404e-bd53-e60569bce10c)
![ls15](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/65436a34-7def-4dff-874a-f9e9d9d403b0)


#### Labs

lab1
![Screenshot (65)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/ce5fc56d-dd56-42ed-8b20-c811e5b5b14a)
![Screenshot (70)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/d2d8fef6-b9a1-40ff-8fb4-4c5497a0f7df)

lab2
![labs1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/a1671961-2770-49d9-9b7a-05c6f5ed5440)
![labls3](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/bb687db4-ca34-4948-92d3-73b1b3ed6f77)
![labls1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b0d240e0-deb1-413a-8ed5-25e9706a79b7)



lab3


<img width="1218" alt="labls2" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/0216bb55-9824-4cf3-9970-f82508ed8ecb">

set target_library /home/sukanya/VLSI/sky130RTLDesignAndSynthesisWorkshop/DC_WORKSHOP/lib/sky130_fd_sc_hd__tt_025C_1v80.db
set link_library {* $target_library}
![Screenshot (80)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/c105f03e-6d67-45d4-87f0-472a049f712c)











 </details> 





<details> 
<summary> DAY 7 Basics of Static Timing Analysis </summary>
	<img width="1203" alt="sta1" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/083d683c-1e6f-4231-a9c8-5ab4fefbc6ac">
<img width="1215" alt="sta2" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/0eb39ad2-aa51-473f-83fa-e21e7307e0f8">
<img width="1223" alt="sta3" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/d243fc86-267c-4d3d-9ad3-b0bd4feda82a">
<img width="1149" alt="sta4" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/bb599909-9108-4910-b498-3833b1b881c1">
<img width="1180" alt="sta5" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/5232ddbc-150c-466d-a4f5-523aba6b23b5">
<img width="1163" alt="sta6" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/44ffb460-e56e-4b9a-b4e4-e09a239f7a10">
<img width="1216" alt="sta7" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/963f603b-9156-4f81-94d8-f7f91ad2954b">
<img width="1214" alt="sta8" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/473002e7-e8c0-4cab-9ac7-ae3d0a192d8d">
<img width="1220" alt="sta9" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/1c0dea60-4770-448d-bf5d-8220c25af91d">
<img width="1161" alt="sta10" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/e3d4a3d7-77f7-4fa7-8bed-81da8eb7d28d">
<img width="1177" alt="sta12" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/0a480787-0dbe-47b8-94cc-e832d60e90e7">
<img width="1197" alt="sta13" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/cf9366a9-782e-42a0-a243-82d44b0df861">
<img width="1223" alt="sta14" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/6815722b-5871-4021-b75c-f739ce9675bd">
<img width="1148" alt="sta15" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7ce78f04-a092-4de0-a4f1-44f5e9d4a2e8">
<img width="1229" alt="sta16" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/6c5a3917-012b-46df-8998-9187b75e71a9">
<img width="1184" alt="sta17" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/726a2cf5-65f7-4670-a575-c6868028d800">
<img width="1215" alt="sta18" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/12039724-7adf-4ea0-9ca5-39bc874e636b">	
<img width="1134" alt="sta19" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/66abaadd-c04e-4390-8027-8b0acd826d3a">


#### Labs

Delay Table Lookup :

The delay table lookup is a method for the characterization of standard cells in digital circuits. The delay table lookup helps to present the complex delay behaviour by breaking down the delay into smaller components, under specific input conditions. The delay values are stored in a table accessed on input, helping in analyzing delay estimation in the timing analysis. 

![stal2](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/ef5a4777-960d-4a48-b76c-48dde69e1c15)
![Screenshot (71)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/04cb0a62-ca51-45da-a630-065ba1bbd7ee)

![stal1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/e1b3e240-ce3c-47ce-a2ea-23b5d92b5fe2)

Unateness : 

1. Positive Unateness:  A rising transition on an input causes the output to rise and vice versa
2. Negative Unateness: A rising transition on an input causes the output to have a falling transition and vice versa
![stal3](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/e53651f4-a846-4706-ad3b-d99b7f6edd0a)



 
 </details> 



 <details> 
<summary>  DAY 8  </summary>

 </details> 

<details> 
<summary>  DAY 9  </summary>

 </details> 

 <details> 
<summary>  DAY 10  </summary>

 </details> 

 <details> 
<summary>  DAY 11  </summary>

 </details> 

 <details> 
<summary>  DAY 12  </summary>

 </details> 

 <details> 
<summary>  DAY 13  </summary>

 </details> 

 <details> 
<summary>  DAY 14  </summary>

 </details> 


 <details> 
<summary>  DAY 15  </summary>

 </details> 




