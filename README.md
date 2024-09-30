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

### lab1

/home/bharath/VLSI/vsd to invoke DC compiler

```
csh
dc_shell
echo $target_library //It generates your_library.db
echo $link_library
read_verilog DC_WORKSHOP/verilog_files/lab1_flop_with_en.v
write -f verilog -out lab1_net.v
sh gvim lab1_net.v


The Verilog syntax of lab1_flop_with_en.v

```


```
module lab1_flop_with_en ( input res , input clk , input d , input en , output reg q);
always @ (posedge clk , posedge res)
begin
	if(res)
		q <= 1'b0;
	else if(en)
		q <= d;	
end
endmodule

```


![Screenshot (64)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/aea55f4e-d3bf-4a21-931d-7f660d20d628)
![Screenshot (73)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/4382dd0c-74f6-4703-8789-05c792060bd4)


![Screenshot (65)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/ce5fc56d-dd56-42ed-8b20-c811e5b5b14a)

```
csh
dc_shell
set target_library /home/bharath/vsd/DC_WORKSHOP/lib/sky130_fd_sc_hd__tt_025C_1v80.db
set link_library {* $target_library}
read_db DC_WORKSHOP/lib/sky130_fd_sc_hd__tt_025C_1v80.db
read_verilog DC_WORKSHOP/verilog_files/lab1_flop_with_en.v
link
compile
write -f verilog -out lab1_net_sky130.v
sh gvim lab1_net_sky130.v

```

![Screenshot (74)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/15ffd838-025f-4cf5-b582-413df8d16eb5)







### lab2

Commands to write the DDC file

```

write -f verilog -out lab1_net_sky130.v //Command to launch Design Vision 


csh
design_vision
start_gui //If GUI doesn't start automatically


read_ddc lab1.ddc //the command to open DDC 

```

![labs1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/a1671961-2770-49d9-9b7a-05c6f5ed5440)
![labls3](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/bb687db4-ca34-4948-92d3-73b1b3ed6f77)
![labls1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b0d240e0-deb1-413a-8ed5-25e9706a79b7)



### lab3


<img width="1218" alt="labls2" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/0216bb55-9824-4cf3-9970-f82508ed8ecb">

```
set target_library /home/sukanya/VLSI/sky130RTLDesignAndSynthesisWorkshop/DC_WORKSHOP/lib/sky130_fd_sc_hd__tt_025C_1v80.db
set link_library {* $target_library}

```
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



## Labs


```
module lab8_circuit (input rst, input clk , input IN_A , input IN_B , output OUT_Y , output out_clk);
reg REGA , REGB , REGC ; 

always @ (posedge clk , posedge rst)
begin
	if(rst)
	begin
		REGA <= 1'b0;
		REGB <= 1'b0;
		REGC <= 1'b0;
	end
	else
	begin
		REGA <= IN_A | IN_B;
		REGB <= IN_A ^ IN_B;
		REGC <= !(REGA & REGB); 
	end
end

assign OUT_Y = ~REGC;

assign out_clk = clk;

endmodule

```


Command flow for verilog to read lab8_circuit.v
```
csh
dc_shell
read_verilog lab8_circuit.v
link
compile_ultra
```

ATTACH THE PIC

ATTACH THE PIC

ATTACH THE PIC

GET_CELLS commands

```
get_attribute [get_cells U9] is_hierarchical

```

```
get_cells * -hier -filter "is_hierarchical == false"
or
get_cells * -hier -filter "is_hierarchical == true"

```

ATTACH THE PIC

```
get_attribute [get_cells REGA_reg] ref_name
```

```
foreach_in_collection my_cell [get_cells * -hier] {
set my_cell_name [get_object_name $my_cell];
set rname [get_attribute [get_cells $my_cell_name] ref_name];
echo $my_cell_name $rname;
}
```

output:

ATTACH THE PIC


```
write -f ddc -out lab8_circuit.ddc
```

```
read_ddc lab8_circuit.ddc
```

ATTACH THE PIC



Syntax to get the nets of the design Design Vision

```
get_nets *
```

Syntax to check type of net connected to

```
all_connected N1
```

ATTACH THE PIC


## Lab 2 - get_pins, get_clocks, querying_clocks

Syntax to get all pins

```
get_pins *

```

Synatx to read all the pins individually


```

foreach_in_collection my_pin [get_pins *] {
set pin_name [get_object_name $my_pin];
echo $pin_name;
}

```

OUTPUT:

ATTACH THE PIC

Syntax to check direction of a pin

```
get_attribute [get_pins REGC_reg/RESET_B] direction
```

Syntax to know the direction of all pins
```
foreach_in_collection my_pin [get_pins *] {
set my_pin_name [get_object_name $my_pin];
set dir [get_attribute [get_pins $my_pin_name] direction];
echo $my_pin_name $dir;
}
```

Syntax to get the pins with all the clock attibute
```

foreach_in_collection my_pin [get_pins *] {                                                                                                                                                                                         set my_pin_name [get_object_name $my_pin];                                                                                                                                                                                            set dir [get_attribute [get_pins $my_pin_name] direction];                                                                                                                                                                    if { [regexp $dir in] } {
if { [get_attribute [get_pins $my_pin_name] clock ] } {
echo $my_pin_name;
}
}
}


```

Syntax of query_clock_pin_sm.tcl
```

foreach_in_collection my_pin [get_pins *] {
	set my_pin_name [get_object_name $my_pin];
        set dir [get_attribute [get_pins $my_pin_name] direction];                                                                                              
	if { [regexp $dir in] } {
		if { [get_attribute [get_pins $my_pin_name] clock ] } { 
 			echo $my_pin_name;

		}
	}
}


```

ATTACH THE PIC

ATTACH THE PIC

## Lab 3 - create_clock waveform

Syntax to create a clock with period of 10ns

```
create_clock -name MYCLK -period 10 [get_ports clk]

```

Syntax to get the period of the clock
```
get_clocks *
```

Syntax to check whether the clock is generated or not. If false then it is a master clock.
```
get_attribute [get_clocks MYCLK] period
```

Syntax to know the information about all clock
```
report_clocks *
```

ATTACH THE PIC

Syntax to query the attributes of all clock pins

```
foreach_in_collection my_pin [get_pins *] {
	set my_pin_name [get_object_name $my_pin];
        set dir [get_attribute [get_pins $my_pin_name] direction];                                                                                              
	if { [regexp $dir in] } {
		if { [get_attribute [get_pins $my_pin_name] clock ] } { 
			set clk [get_attribute [get_pins $my_pin_name] clocks]; # 	set clk_name [get_object_name [get_attribute [get_pins $my_pin_name] clocks]];
			set clk_name [get_object_name $clk];
 			echo $my_pin_name $clk_name;

		}
	}
}
```

Output is shown below

ATTACH THE PIC

Do not create a clock on the pin but rather create one on the port. The clock created on the pin will not reach to any pin of any flop.

Syntax to remove the clock, BAD_CLK is the clock name

```
remove_clock BAD_CLK
```

```
create_clock -name MYCLK -period 10 [get_ports clk] -wave {5 10}
```

```
create_clock -name MYCLK -period 10 -wave {0 2.5} [get_ports clk]
```

The order of command doesn't matter when creating a clock.


### Lab 4 - Clock Network Modelling - Uncertainty, report_timing

Syntax to model the source latency

```
set_clock_latency -source 1 [get_clocks MYCLK]
```

Syntax to model the network latency
```
set_clock_latency 1 [get_clocks MYCLK]
```

Syntax to model uncertainity for max delay or setup which is default
```
set_clock_uncertainty 0.5 [get_clocks MYCLK]
```

Syntax to model uncertainity for max delay or hold
```
set_clock_uncertainty -hold 0.1 [get_clocks MYCLK]
```

If no clock is present, report_timing shows path is unconstrained

Syntax to report clock to register

```
report_timing -to REGC_reg/D
```

ATTACH THE PIC

After modeling the clock for following commands

```
dc_shell> set_clock_latency -source 2 [get_clocks MYCLK]
1
dc_shell> set_clock_latency 1 [get_clocks MYCLK]
1
dc_shell> set_clock_uncertainty 0.5 [get_clocks MYCLK]
1
dc_shell> set_clock_uncertainty -hold 0.1 [get_clocks MYCLK]
```

ATTACH THE PIC


Lab 5 - IO Delays


Syntax to know the modeling of ports and pins
```
report_port verbose
```


Syntax to model the input port delay

```
set_input_delay -max 5 -clock [get_clocks MYCLK] [get_ports IN_A]
```

Syntax to know the timing around port IN_A

```
report_timing -from IN_A
```

ATTACH THYE PIC

Syntax to model the transition in port IN_A and write to a file a

```
report_timing -from IN_A -trans -net -cap -nosplit > a
```

ATTACH THE PIC

Syntax to set the hold time for port IN_A(or IN_B)

```
set_input_delay -min 1 -clock [get_clocks MYCLK] [get_ports IN_A]
```
Syntax to check the hold timing for port IN_A

```
report_timing -from IN_A -trans -net -cap -nosplit -delay_type min > a
```

ATTACH THE PIC

Syntax to set a max transition for port IN_A

```
set_input_transition -max 0.3 [get_ports IN_A]
```

Syntax to set min transition for port IN_A

```
set_input_transition -min 0.1 [get_ports IN_A]
```

Syntax to model the max delay of output port

```
set_output_delay -max 5 -clock [get_clocks MYCLK] [get_ports OUT_Y]
```

Syntax to model the min delay of output port

```
set_output_delay -min 1 -clock [get_clocks MYCLK] [get_ports OUT_Y]
```

Syntax to model a max load for output port

```
set_load -max 0.4 [get_ports OUT_Y]
```

Syntax to model a min load for output port

```
set_load -min 0.1 [get_ports OUT_Y]
```

3 - Generated Clock






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
<summary>  DAY 11 - Introduction to Baby SoC  </summary>

<img width="1255" alt="10c1" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/a266598c-61ee-4408-bb69-c313f98ec8d0">
<img width="1336" alt="10c2" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/aa28e161-3c1b-4f1a-8219-0c46853175ad">
<img width="1375" alt="10c3" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/554b19eb-99ab-4691-9c6d-544eb19819ef">
<img width="1362" alt="10c4" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7e61720e-97de-42b6-bb7a-1e8c04f4d150">

<img width="713" alt="10c5" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/82220abd-edad-4ace-880b-0c3dc0bec63a">

<img width="1356" alt="10c6" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/23613151-df77-4c73-a8e8-8cced202cf93">
<img width="1283" alt="10c7" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/ada1f103-6191-415a-a677-3f160e98d90d">
<img width="1299" alt="10c8" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b0a52d5f-181e-41a6-98f7-cd851548ca06">
<img width="1309" alt="10c9" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/1d71442b-bb51-445f-8db0-669c5dd67753">


GitHub Repos’s for reference

    manili/VSDBabySoC: VSDBabySoC is a small mixed-signal SoC including PLL, DAC, and a RISCV-based processor named RVMYTH.(https://github.com/manili/VSDBabySoC#what-is-rvmyth)
    Devipriya1921/avsddac28nm (https://github.com/Devipriya1921/avsddac28nm)
    reneann713/PLL (https://github.com/ireneann713/PLL)
    lakshmi-sathi/avsdpll_1v8: 8x PLL Clock Multiplier IP with an input frequency range of 5Mhz to 12.5Mhz and output frequency range of 40Mhz to 100Mhz, giving a 8x multiplied clock at ~50% duty cycle on tt corner at room temperature. (https://github.com/lakshmi-sathi/avsdpll_1v8)

References

    https://microcontrollerslab.com/system-on-chip-soc-introduction/
    https://github.com/Devipriya1921/VSDBabySoC_ICC2









 </details> 

 <details> 
<summary>  DAY 12-Modelling of BabySoC  </summary>

SoC and What does modelling mean?

    SoC is a single-die chip has some different IP cores that could vary from microprocessors (completely digital) to 5G broadband modems (completely analog).
    SoC with equivalent functionality will have increased performance and reduced power consumption as well as a smaller semiconductor die area(PPA).



    Modeling and simulation involves use of a physical or logical representation of a given system to generate data and help determine decisions or make predictions about the system.
    Models are representations that can aid in defining, analyzing, and communicating a set of concepts. M&S is widely used in the VLSI domain.
    System models are specifically developed to
        support analysis, specification,
        design, and architectural planning
        verification, and validation of a system,
	Enhance communication related to system and functionality.

What are we modelling?

    Let us look into VSDBabySoC modelling. Here we are going to model and simulate the VSDBabySoC
    Some initial input signals will be fed into vsdbabysoc module,
    That will get the pll start generating the proper CLK for the circuit.
    The clock signal will make the rvmyth to execute instructions and some values are generated, these values are used by DAC core to provide the final output signal named OUT.
    So we have 3 main elements (IP cores) and a wrapper as an SoC and of-course there would be also a testbench module out there.

The task is to model the 3 main IP cores

   1. RVMYTH 
   2. PLL 
   3. DAC 




<img width="873" alt="11c1" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/693d89a6-7aa9-4bd4-8158-70c4ffb5c3fb">
<img width="916" alt="11c2" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/487b5fcf-443c-4c53-ae90-17912cf9d1fb">



Phase Locked Loop

    A phase-locked loop (PLL) is an electronic circuit with a voltage or voltage-driven oscillator that constantly adjusts to match the frequency of an input signal.
    PLLs are used to generate, stabilize, modulate, demodulate etc
    Now, question is why do we need a PLL for our SoC?
    Before that how is a clock generated? Quartz crystal oscillator. For 100Mhz and below off chip oscillator will do, but for 100Mhz and above it won’t be good enough.-how?

Why off-chip clocks can’t be used all the time?

    The clock will be a supply for a lot of blocks on the chip, it will have delays due to long wires(if used only one clock source) - also reasons like clock jitter
    Some blocks might need 200Mhzs and some might need 100Mhz - point is different frequencies just on one small chip
    A concept of ppm(clock accuracy) comes in, when ever quartz is acquired, it comes with a x ppm error

What is this ppm error? {ppm - parts per million}

    For ex: 20ppm quartz used in watches this translates as 20/1e6 (2e-5) which gives an error over a day of 86400 * 2e-5 = 1.73 seconds per day so in a month it loses 30 x1.72 = 51 seconds or 1 minute a month
    Now, in terms of a chip, just imagine the mishap it will cause just due to very small error for ,microseconds, when the processor works at nanoseconds -------- it can be a huge blow.

<img width="958" alt="11c3" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/f11e7b7c-b1a7-4a87-896e-68f639e3d1ca">

<img width="895" alt="11c4" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b874e87e-b883-45df-a6c0-cb3fea94031d">

Digital-to-Analog Converter

    A Digital-to-Analog Converter (DAC) converts a digital input signal into an analog output signal.
    There are two types of DACs : a) Weighted Resistor DAC b) R-2R Ladder DAC

    
<img width="900" alt="11c5" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/df5c50a3-e0a5-4f25-ad35-15c4283f9f11">

<img width="891" alt="11c6" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/2a66bde1-b59b-4978-a2f6-de9692f959b3">

<img width="895" alt="11c7" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/c0b059e7-2124-446a-a923-d185c751168d">


Let's start modelling

    RVMYTH is a digital block, so yes we can use a HDL for designing and check its functionality using a testbench.
    But! DAC and PLL are analog what to do?
    Because verilog can’t synthesis analog design
    We are going to simulate it using verilog - we will be using data-types such real.
    Our goal is to be able to simulate “functionality” - to verify its logical correctness.

Tools used for pre-synthesis modeling

So we will be using verilog to model - and use iverilog to compile and simulate. Use GTKWave to see the waveforms & debug.

How do we model and simulate them?

    We will be using iverilog - Icarus Verilog is an implementation of the Verilog hardware description language compiler that generates netlists in the desired format. It supports the 1995, 2001 and 2005 versions of the standard, portions of SystemVerilog, and some extensions.
    Modelling and simulating on iverilog involves 2 main steps, namely:

    Compilation - iverilog builds the instance hierarchy and generates a binary executable a.out. This binary executable is later used for simulation.
    Simulation - During compilation, iverilog generates a binary executable.




    
<img width="900" alt="11c8" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/6e01f32b-cda8-4b28-9bc9-992479115441">

<img width="891" alt="11c9" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/ad9fcdd6-f044-43ba-b9a7-896d1734e006">
<img width="919" alt="11c10" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/23a0ab8c-2e4b-4a27-9da6-778cb78dd191">


## LAB


Steps to be followed for pre-synthesis modeling of BabySoC

The repo used for the reference is - https://github.com/manili/VSDBabySoC?tab=readme-ov-file#step-by-step-modeling-walkthrough

1. check the installation iverilog and GTKwave.


2. Install sandpiper-saas with the following commands
   
```
    cd ~
pip3 install pyyaml click sandpiper-saas

```

3. After installing, check if sandpiper-saas is present in the path with the command which sandpiper-saas. You should get a local path name as shown below.

```
   [bharath@sfalvsd ~]$ which sandpiper-saas
~/.local/bin/sandpiper-saas
```

4. Now we can clone this repository in an arbitrary directory (we'll choose home directory here):

```
cd ~
git clone https://github.com/manili/VSDBabySoC.git
```

5. RVMYTH is designed and created by the TL-Verilog language. So we need a way for compile and transform it to the Verilog language and use the result in our SoC. Here the sandpiper-saas will help us do the job.

```
cd VSDBabySoC
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```

The last command translates .tlv definition of rvmyth into .v definition.

6. Create an output directory inside VSDBabySoC using the command mkdir output.

7. Compile and Simulate the design.

```
    iverilog -o output/pre_synth_sim.out -DPRE_SYNTH_SIM src/module/testbench.v -I src/include -I src/module
    cd output
    ./pre_synth_sim.out
```

8. GTKwave tool

```
gtkwave pre_synth_sim.out

```

![Screenshot (82)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/811edf19-0541-4fa6-bcfb-7e16d4e0f4c7)

![Screenshot (83)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/1fa01bcb-8635-468f-9232-c111d7ddd4a4)













Analysis if picture above:
CLK:The input CLK signal of the RVMYTH core comes from the PLL.
reset: Input reset signal of the RVMYTH core comes from an external source.
OUT: Output OUT signal of the VSDBabySoC module comes from the DAC.
RV_TO_DAC[9:0]:The 10-bit output [9:0] OUT port of the RVMYTH core, comes from the RVMYTH register #17.
OUT: The real datatype wire which can simulate analog values, the output wire real OUT signal of the DAC module, it comes from the DAC. It is changing the Data Format of the signal to Analog -> Step .


 </details> 

 <details> 
<summary>  DAY 13- Post Synthesis Simulation  </summary>

Reference github repositories

    https://github.com/nurnazahah/sd-training/blob/main/readme.md#day-12
    https://github.com/Devipriya1921/VSDBabySoC_ICC2/blob/main/vsdbabysoc.sdc


## Synthesizable and non-synthesizable constructs in verilog


## Why do pre-synthesis? Why cannot just do post-synthesis?

    Pre-synthesis simulation is done according to the logic that we have designed for and written -> only functionality
    Post synthesis simulation/GLS (gate level simulation) is done after synthesis considering each gate delays into account, also reports the violations in both functionality and timing


## Gate Level Simulation (GLS)

    'gate level' refers to netlist view of a circuit, usually produced by logic synthesis
    RTL simulation is pre-synthesis while GLS is post-synthesis
    Netlist view is a complete connection list consisting of gates and IP models with full functional and timing behavior
    RTL simulation is zero delay environment and events, generally occurs on the active clock edge. Whereas GLS can be zero delay too, but it is more often used in unit delay or full timing mode

    Using synopsys’s DC shell (Design Compiler)
    DC RTL synthesis solution enables users to meet today's design challenges with concurrent optimization of timing, area, power and test.

## Why post-synthesis simulation is needed?

    To ensure each and every gate delays are taken into account
    Pre-synthesis in iverilog and GTKwave that has done from the previous labs would be compared
    To observe the output generated
    Note that post-simulation output should be matched with pre-simulation output


![13c1](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/12c725fd-d7b0-4c88-832c-9904f781394f)


 Lab - Converting .lib file to .db file

Use the link for reference - https://github.com/nurnazahah/sd-training/blob/main/readme.md#topic-post-synthesis-simulation


Converting .lib file to .db file

The first step is to convert .lib file to .db file. We need .db format for avsddac.lib, avsdpll.lib & sky130_fd_sc_hd__tt_025C_1v80.lib using Synopsys Library Compiler (lc_shell).

The .lib files, avsddac.lib, avsdpll.lib, and sky130_fd_sc_hd__tt_025C_1v80.lib are present in the location /home/sukanya/VSDBabySoC/src/lib.
Converting avsddac.lib file to avsddac.db file

Syntax to convert the avsddac.lib files to avsddac.db

```
cd /home/bharath/VSDBabySoC/src/lib
lc_shell
read_lib avsddac.lib
write_lib avsddac -format db -output avsddac.db

```

The conversion of avsddac.lib to avsddac.db is successful as shown in screenshot below. If the code runs successfully, it will return 1.
![Screenshot (84)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/f7ff9505-1c63-491e-aa4f-894dbe7942b1)



Converting avsdpll.lib file to avsdpll.db file

Commands to convert the avsdpll.lib files to avsdpll.db


```
cd /home/Bharath/VSDBabySoC/src/lib
lc_shell
read_lib avsdpll.lib
write_lib avsdpll -format db -output avsdpll.db
```


The read_lib command shows some errors for avsdpll.lib. The errors are shown below.\

ATTACH THE PIC


The modification for avsdpll.lib is highlighted in the right with the original shown on the left. The power VDD and ground GND pins are removed along with other commented parts.




Converting sky130_fd_sc_hd__tt_025C_1v80.lib file to asky130_fd_sc_hd__tt_025C_1v80.db file

Download the latest sky130_fd_sc_hd__tt_025C_1v80.lib from the path - https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing Synatx to download the raw file in Linux

```
wget https://raw.githubusercontent.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/master/timing/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Syntax to convert the sky130_fd_sc_hd__tt_025C_1v80.lib files to sky130_fd_sc_hd__tt_025C_1v80.db

```
cd /home/bharath/VSDBabySoC/src/lib
lc_shell
read_lib sky130_fd_sc_hd__tt_025C_1v80.lib
write_lib sky130_fd_sc_hd__tt_025C_1v80 -format db -output sky130_fd_sc_hd__tt_025C_1v80.db
```

The screenshot for successful conversion is shown below.

(ATTACH THE PIC)

## LAB: Synthesis and Post Synthesis (Gate Level) Simulation 

Syntax to perform synthesis

```
cd /home/bharath/VSDBabySoC
dc_shell
set target_library /home/bharath/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.db
set link_library {* /home/bharath/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.db /home/bharath/VSDBabySoC/src/lib/avsdpll.db /home/bharath/VSDBabySoC/src/lib/avsddac.db}
set search_path {/home/bharath/VSDBabySoC/src/include /home/bharath/VSDBabySoC/src/module} 
read_file {sandpiper_gen.vh  sandpiper.vh  sp_default.vh  sp_verilog.vh clk_gate.v rvmyth.v rvmyth_gen.v vsdbabysoc.v} -autoread -top vsdbabysoc 
link 
compile_ultra
write_file -format verilog -hierarchy -output /home/bharath/VSDBabySoC/output/vsdbabysoc_net.v
report_qor > report_qor.txt
```

![Screenshot (87)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/df568aa2-53c4-467c-ba0c-60cf5c651d3d)


The quality of report is saved in the file report_qor.txt.

Syntax to perform post-synthesis simulation

```
cd /home/bharath/VSDBabySoC
iverilog -DFUNCTIONAL -DUNIT_DELAY=#1 -o ./output/post_synth_sim.out ./src/gls_model/primitives.v ./src/gls_model/sky130_fd_sc_hd.v ./output/vsdbabysoc_net.v ./src/module/avsdpll.v ./src/module/avsddac.v ./src/module/testbench.v
cd output
./post_synth_sim.out
gtkwave dump.vcd
```

The output of GLS is shown below. 
![Screenshot (85)](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/6590aee3-1406-4b7d-8921-80ca5dcf942c)


Comparing the post-synthesis (top) and pre-synthesis (bottom) output shows they match.
(ATTACH THE PIC)


## Lab - Synthesis with SDC Constraints

SDC constraints are written in file vsdbabysoc_synthesis.sdc under the folder /home/bharath/VSDBabySoC/src/sdc. The constraints are

```
set_units -time ns
set_max_area 8000
set_load -pin_load 0.5 [get_ports OUT]
set_load -min -pin_load 0.5 [get_ports OUT]
create_clock [get_pins pll/CLK] -name clk -period 10 -waveform {0 5}
set_clock_latency 1 [get_clocks clk]
set_clock_latency -source 2 [get_clocks clk]
set_clock_uncertainty 0.5  [get_clocks clk]
set_max_delay 10 -from [get_pins dac/OUT] -to [get_ports OUT]
set_input_delay -clock clk -max 4 [get_ports VCO_IN]
set_input_delay -clock clk -min 1 [get_ports VCO_IN]
set_input_delay -clock clk -max 4 [get_ports ENb_CP]
set_input_delay -clock clk -min 1 [get_ports ENb_CP]
set_input_transition -max 0.4 [get_ports VCO_IN]
set_input_transition -min 0.1 [get_ports VCO_IN]
set_input_transition -max 0.4 [get_ports ENb_CP]
set_input_transition -min 0.1 [get_ports ENb_CP]
```

Syntax to perform synthesis with SDC constraints

```
dc_shell
set target_library /home/bharath/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.db
set link_library {* /home/bharath/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.db /home/bharath/VSDBabySoC/src/lib/avsdpll.db /home/bharath/VSDBabySoC/src/lib/avsddac.db}
set search_path {/home/bharath/VSDBabySoC/src/include /home/bharath/VSDBabySoC/src/module} 
read_file {sandpiper_gen.vh  sandpiper.vh  sp_default.vh  sp_verilog.vh clk_gate.v rvmyth.v rvmyth_gen.v vsdbabysoc.v} -autoread -top vsdbabysoc 
link
read_sdc /home/bharath/VSDBabySoC/src/sdc/vsdbabysoc_synthesis.sdc
compile_ultra
write_file -format verilog -hierarchy -output /home/sukanya/VSDBabySoC/output/vsdbabysoc_net_sdc.v
report_qor > report_qor_sdc.txt
report_timing -nets -attributes -input_pins -transition_time -delay_type max > report_setup_sdc.txt
report_timing -nets -attributes -input_pins -transition_time -delay_type min > report_hold_sdc.txt
```

The quality of report is saved in the file report_qor_sdc.txt and timing reports are saved in the files report_setup_sdc.txt and report_hold_sdc.txt

Syntax to perform post-synthesis with SDC constraints simulation

```
cd /home/bharath/VSDBabySoC
iverilog -DFUNCTIONAL -DUNIT_DELAY=#1 -o ./output/post_synth_sdc_sim.out ./src/gls_model/primitives.v ./src/gls_model/sky130_fd_sc_hd.v ./output/vsdbabysoc_net_sdc.v ./src/module/avsdpll.v ./src/module/avsddac.v ./src/module/testbench.v
cd output
./post_synth_sdc_sim.out
gtkwave dump.vcd
```

The output of GLS with SDC constraints is shown below. 

(ATTACH THE PIC)

Comparing the post-synthesis with sdc constraints (top) and pre-synthesis (bottom) output shows they match in behavior but output value varies.

(ATTACH THE PIC)

## Analysis with and without SDC

Comparing the QoR without SDC and with SDC shows that clk is not considered when SDC wasn't included.



(ATTACH THE PIC)


(ATTACH THE REPORT)











 </details> 

 <details> 
<summary>  DAY 14- Synopsis: DC and Timing Analysis  </summary>

Download the timing libraries in the path /home/bharath/VSDBabySoC/src/timing_libs from https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing


Syntax to convert .lib to .db for each

```
cd /home/bharath/VSDBabySoC/src/timing_libs
lc_shell
read_lib sky130_fd_sc_hd__ff_100C_1v65.lib
write_lib sky130_fd_sc_hd__ff_100C_1v65 -format db -output sky130_fd_sc_hd__ff_100C_1v65.db
```


Commands for synthesis with SDC constraints

```
cd /home/bharath/VSDBabySoC
dc_shell
set target_library /home/bharath/VSDBabySoC/src/timing_libs/sky130_fd_sc_hd__ff_100C_1v65.db
set link_library {* /home/bharath/VSDBabySoC/src/timing_libs/sky130_fd_sc_hd__ff_100C_1v65.db /home/sukanya/VSDBabySoC/src/lib/avsdpll.db /home/sukanya/VSDBabySoC/src/lib/avsddac.db}
set search_path {/home/bharath/VSDBabySoC/src/include /home/sukanya/VSDBabySoC/src/module} 
read_file {sandpiper_gen.vh  sandpiper.vh  sp_default.vh  sp_verilog.vh clk_gate.v rvmyth.v rvmyth_gen.v vsdbabysoc.v} -autoread -top vsdbabysoc 
link
read_sdc /home/Bharath/VSDBabySoC/src/sdc/vsdbabysoc_synthesis.sdc
compile_ultra
write_file -format verilog -hierarchy -output /home/sukanya/VSDBabySoC/output/vsdbabysoc_net_ff_100C_1v65.v
report_qor > report/report_qor_ff_100C_1v65.txt
```

Repeat the above process for all the PVT corners inside timing_libs Table below shows the Worst Negative Slack (WNS) and Worst Hold Slack (WHS) for PVT corners

(ATTACH THE PIC)




 </details> 


 <details> 
<summary>  DAY 15- Floorplanning & Power Planning of BabySoC  </summary>

(ATTACH THE PIC)


Physical Design is the process of translating the gate level netlist into a physical layout. This physical layout consists of various metal shapes and sizes which can be drawn onto masks and manufactured on the silicon wafer.

The Physical Design process can be broken down into multiple stages as illustrated below. It is often an iterative process where a number of optimizations are performed at each step to meet the design performance, area & power requirements.

### Floorplanning :

Floorplanning is the first step of physical design. The design is first partitioned into various smaller subsystems based on the system architecture and design requirements. Floorplanning determines the aspect ratio and area of the layout. Here we create the placement rows for standard cells and fix the placement of I/Os around the boundary. Any macros in the design are also placed during the floorplan stage.

Power planning is also typically done during floorplanning. The power grid network is created to distribute power to all the std cells rows, macros and all other components of the design. If there are any special IPs being used in the design then all the IP integration guidelines are also considered in floorplanning.

A good floorplan is very critical to the overall quality of your design.

(ATTACH THE PIC)

Logic Placement :

In this stage all the standard cells in the design are placed and assigned a legal location. After the placement EDA tools performs a number of optimizations to improve placement and congestion. A bad placement might lead to larger area utilization and timing issues.

EDA tools also use timing driven placement algorithms to optimize the placement while considering the timing requirements of the design.

Clock Tree Synthesis :

During the Floorplanning & placement stage, the clock is considered as an ideal network. The optimizations in the placement stage are performed based on the assumption of an ideal clock reaching to all flops at the same time. In CTS, a clock network is created to distribute the clock to all flops. This clock tree is built using buffers or inverters along the clock paths of design in order to achieve zero/minimum skew based on design requirement. A good quality clock network is very crucial to meet the timing requirements of the design.

Routing :

Once all the standard cells are legally placed and the clock network is synthesized, all the connecting data nets need to be laid out on the metal layers. This is done during the routing stage. After routing all the nets, a number of optimizations are performed based on the design timing requirements and analysis.

Timing Analysis & Signoff :

After the design Routing, static timing analysis is performed on the design. This step is critical to analyze the performance of the design. During STA, we break down the design into timing paths and calculate the signal propagation delay along each path. Then each path is checked for violations of timing constraints. If any path is violated then these violations need to be addressed before signing off on the design.

The timing signoff ensures that all the design elements are meeting the specified timing requirements and the design is working at the desired frequency.

Physical Verification & Signoff :

After the routing is completed, the layout must be completely verified to ensure its correct electrical and logical functionality. The physical verification signoff ensures that the design meets all the fabrication specified rules and can be easily manufactured. Various checks that are performed at this stage includes :

    DRC (Design Rule Check)
    LVS (Layout Vs Schematic)
    ERC (Electrical Rule Check)
    Antenna check
    EM (Electromigration Analysis)

Once the physical verification is done, the layout is streamed out in the form of a GDSII or OASIS file for fabrication which is called design tapeout.

What is Floorplanning? :

In VLSI design, Floorplanning is a crucial step in physical design that involves placing blocks and macros in the chip or core area.

The primary objectives of floorplanning are to minimize area, timing, wire length, and power consumption while ensuring easy routing and reliability.

Here are the key aspects of floorplanning:
Inputs for Floorplanning :

    Gate level netlist (.v)
    Physical & Logical Libraries. (.lefs & .libs for all standard cell,macros,IO Pads etc.)
    Synopsys design constraints (.sdc).
    RC Tech File (TLU+ file) - to determine RC values of interconnect layers/metal layers of technology node used in our design, and hence provide RC values for computation of wire delays.
    Technology File (.tf).
    Physical Partitioning information of the design.
    Floorplanning Parameters like height,width,aspect ratio etc.

Outputs of Floorplanning

    Die/Core Area: The physical description of the ASIC design.
    IO Pad Information: The placement of I/O pins.
    Placed Macros Information: The placement of macros.
    Standard Cell Placement Areas: The areas where standard cells are placed.
    Power Grid Design: The power distribution plan.
    Blockages: The defined regions where cells cannot be placed.

Sources:

    https://www.vlsisystemdesign.com/floorplanning/
    https://www.vlsi4freshers.com/2020/01/floorplanning.html
    https://www.physicaldesign4u.com/2019/12/floorplanning-floor-planning-is-art-of.html
    http://vlsibegin.blogspot.com/p/floorplanning_7.html
    https://vlsitutor.com/nots/introduction-to-floorplan/

Types of floorplan techniques used in Full Chip plan :

    Abutted Floorplan: This technique involves channel-less placement of blocks, where there is no gap between the blocks.
    Non-Abutted Floorplan: In this technique, blocks are placed with a gap between them, and connections are made through routing nets.
    Mix of Both: This approach combines abutted and non-abutted techniques, using both channels and direct connections.

    (ATTACH THE PIC)

Floorplan Control Parameters

    Aspect Ratio: The ratio of the height to the width of the chip, which affects routing resources and congestion.
    Core Utilization: The percentage of the core area occupied by standard cells, macros, and blockages.

Floorplan Steps

    Define Width and Height: Determine the size of the core and die.
    IO Pin Placement: Place I/O pins at the boundary of the chip.
    Power Planning: Plan the power grid and power distribution.
    Macro Placement: Place macros manually using flylines.
    Standard Cell Row Creation: Create areas for standard cell placement.
    Blockages: Define blockages to ensure proper placement and routing.

    (ATTACH THE PIC)

Key Terms

    Standard Cell Row: The area where standard cells are placed, divided into rows with varying heights.
    Flylines: Virtual connections between macros and IO pads, helping in logical placement and reducing routing resources.
    Halo (Keep Out Margin): The region around fixed macros where other macros and standard cells cannot be placed.

Issues with Bad Floorplanning

    Area and Power Consumption: A bad floorplan can increase the area and power consumption of the chip.
    Reliability: It can affect the reliability of the chip.
    Timing Closure: A bad floorplan can make timing closure difficult.

Qualifying a Good Floorplan

    Meet Timing and Congestion Constraints: Ensure that the floorplan meets timing and congestion constraints.
    Optimize Area and Power: Optimize the area and power consumption of the chip.
    Ensure Routing and Placement: Ensure that the floorplan allows for easy routing and placement.

Automatic Floorplan Options

    Automatic Macro Placement: Most PnR tools provide automatic floorplan options, but these may not always produce optimal results.

Macro Placement Tips

    Understand Pins and Orientation: Understand the pin requirements and orientation of macros.
    Follow Data Flow: Place macros following the data flow and hierarchy.
    Ensure Proper Orientation: Ensure that all macro pins point towards the core logic.
    Channel Size: Ensure that channels between macros are large enough for routing and power grids.

Blockages

    Soft Blockages: Partial blockages that can be removed during placement.
    Hard Blockages: Permanent blockages that cannot be removed.
    Partial Blockages: Blockages that can be removed during placement but are used to prevent congestion.

What is Power Planning ?

Power planning in VLSI design is the process of creating a power distribution network (PDN) to provide power to all the components of the chip, including macros, standard cells, and other cells. The main objectives of power planning are:

    Maintain a stable voltage across the chip with minimal noise.
    Avoid electromigration and self-heating issues.
    Consume minimal chip area and wiring resources
    Meet IR drop budget targets for reliable operation.

The key steps in power planning are:

    Calculating the number of power pins required, based on the total power consumption of the chip.
    Determining the number of power rings, stripes, and rails needed to distribute power uniformly.
    Sizing the width of power rings and stripes to handle the required current.
    Analyzing IR drop and taking measures to keep it within acceptable limits.

The power distribution hierarchy consists of:

    Power pads that bring power from outside the chip.
    Power rings around the core area.
    Power stripes that distribute power horizontally and vertically across the core.
    Power rails that connect the power stripes to the standard cells.

Power planning is typically done during the floorplanning stage, before signal routing. It involves creating a power mesh using the top metal layers, which have lower resistance compared to lower metal layers.

The power mesh is connected to the standard cells through power rails.

Proper power planning is critical in modern VLSI designs to ensure reliable operation and meet power budget constraints, especially in deep sub-micron technologies where power has become a primary concern over area and performance.

    (ATTACH THE PIC)

Lab - Floorplanning of VSDBabySoC :

    Downloading Physical design Collaterals :
        git clone https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master - to download all Technology file (.techlef) for skywater130nm pdk and all the .lef files for all standard cells.
        git clone https://github.com/bharath19-gs/synopsys_ICC2flow_130nm - to download Technology file (.tf) for skywater130nm pdk and RC Tech file (parasitics file) in .itf format for the PDK.
        git clone https://github.com/kunalg123/icc2_workshop_collaterals - to download all the scripts to setup and run Physical Design flow in ICC2 Compiler tool.

    The Interconnect Technology Format (ITF) file is a critical component in the physical design of integrated circuits (ICs). Here is a concise summary of what the ITF file contains and how it is used:

The ITF file provides a detailed description of the process technology, including the physical attributes of the conductor and dielectric layers. Specifically, it specifies:

    The thickness, minimum width, and minimum spacing of each conductor layer.
    The sheet resistance (RPSQ) of each conductor layer.
    The thickness and dielectric constant (ER) of each dielectric layer.
    The resistivity (RHO) and area (AREA) of each via layer.

This information is used to accurately model the parasitic resistance and capacitance (RC) of the interconnect in the design.

The ITF file is a critical input for parasitic extraction tools to generate the RC parasitics needed for timing, signal integrity, power, and reliability analysis.

Additionally, the ITF file can be used to generate TLU+ files, which are another important technology file used in physical design.

```
To convert .itf file to .tluplus format,perform the following steps :

1) cd `/home/subhasis/VSDBabySoC/synopsys_ICC2flow_130nm/synopsys_skywater_flow_nominal/itf_files`
2) In Linux Terminal,
    grdgenxo -itf2TLUPlus -i skywater130.nominal.itf -o skywater130.nominal.tluplus # to generate TLUplus RC Tech file from .itf file format using StarRC tool.

```

Add  (ATTACH THE PIC) 10+


 </details> 

  <details> 
<summary>  DAY 16- Placement of BabySoC  </summary>

 </details> 


  <details> 
<summary>  DAY 17- CTS </summary>
Clock Tree Synthesis (CTS) is a critical process in VLSI design, aimed at ensuring that the clock signal is efficiently distributed across all sequential elements in a circuit. This process is essential for maintaining timing integrity, minimizing power consumption, and optimizing performance.
Overview of Clock Tree Synthesis

CTS involves connecting the clock signal from the clock port to the clock pins of sequential cells while minimizing insertion delay and balancing skew. The clock network is typically categorized as a high fanout net, which requires special handling due to its significant power consumption—often accounting for 30-40% of total chip power—and its susceptibility to electromigration (EM) effects.
Key Objectives of CTS

    Minimize Insertion Delay: This is crucial for ensuring that the clock signal reaches all components in a timely manner, thus maintaining the overall performance of the design.

    Balance Skew: Skew refers to the difference in arrival times of the clock signal at different sequential elements. Balancing skew is vital to ensure synchronous operation of the circuit.

    Power Optimization: Since the clock network consumes a substantial amount of power, optimizing its design can lead to significant energy savings.

Steps in Clock Tree Synthesis

The CTS process typically includes the following steps:

    Preparation: This involves checking the legality of the design, ensuring power connections are correct, and verifying that the timing quality of results (QoR) is acceptable.

    Clustering: Grouping sink pins based on their geometric locations to facilitate better skew management.

    Buffer Insertion: Automatically inserting buffers and inverters along the clock paths to manage load and reduce insertion delay.

    Balancing: Using clock buffers and inverters to achieve a balanced clock distribution across the design.

    Post-Conditioning: Final adjustments to ensure that all design rules are met and that the clock tree operates within specified parameters for skew and insertion delay.

Types of Clock Tree Structures

Several structures can be utilized for building the clock tree, including:

    H-Tree Structure: A balanced tree structure that minimizes skew.
    X-Tree Structure: Similar to the H-tree but optimized for different geometries.
    Geometric Matching Algorithm (GMA): A method for optimizing the layout of the clock tree.
    Pi Tree Structure: A structure that balances loads effectively.
    Fishbone Structure: A more complex design that can handle varying loads and distances.

Inputs and Outputs of CTS
Inputs Required for CTS

    Placement Database (DB): Contains the netlist after placement, including various technology files and specifications.
    Clock Tree Specification File: Defines the requirements and constraints for the clock tree.
    Library Files: Include information on clock buffers and inverters used in the design.

Outputs of CTS

After the CTS process, the outputs typically include:

    A netlist that reflects the clock tree configuration.
    Timing reports detailing setup and hold times.
    Skew and latency reports to assess clock performance.

Quality Checks Post-CTS

After completing the CTS, several checks are necessary to ensure the clock tree meets design goals:

    Insertion Delay: Must meet target values.
    Skew Balancing: Should be within acceptable limits.
    Signal Integrity: Ensuring minimal crosstalk and other noise effects.
    Power Consumption: Evaluating the clock tree's power usage to ensure it aligns with design specifications.

In summary, Clock Tree Synthesis is a fundamental aspect of VLSI design that directly impacts the performance, power efficiency, and reliability of integrated circuits. Proper execution of CTS ensures that the clock signal is effectively distributed, enabling synchronous operation of all components within the design.

Citations: [1] https://vlsitalks.com/physical-design/cts/ [2] https://anysilicon.com/clock-tree-synthesis/ [3] https://vlsitutor.com/nots/clock-tree-synthesis/ [4] https://signoffsemiconductors.com/clock-tree-synthesis-1/ [5] https://www.physicaldesign4u.com/2020/02/clock-tree-synthesis.html
 </details> 




  <details> 
<summary>  DAY 18- Routing </summary>
Routing is a crucial stage in the VLSI design flow that creates physical connections between signal pins by following design rules. The main goals of routing are to minimize total wire length and vias, complete routing within the design area, meet timing constraints, and avoid design rule violations.
Types of Routing

There are three main types of routing:

    Pre-routing (also known as power routing) which is done during power planning.
    Clock routing performed while building the clock tree in the clock tree synthesis (CTS) stage.
    Signal routing done after CTS to connect all signal pins.

Routing Flow

The routing flow consists of four main stages:

    Global Routing: Divides the core area into global cells (gcells) and finds the shortest path for each net using algorithms like maze routing and Steiner tree. It assigns nets to specific gcells but does not define actual tracks.

    Track Assignment: Assigns actual metal layers to global routes while fixing some design rule violations. However, many DRC, signal integrity, and timing violations still remain.

    Detailed Routing: Completes the actual metal and via connections between pins. It fixes all remaining violations through multiple iterations. The block is divided into switch boxes (Sboxes) for routing.

    Search and Repair: Performed after the first detailed routing iteration to locate and fix any remaining shorts or spacing violations[2][3].

Routing Constraints

Key routing constraints include:

    Design rule constraints related to manufacturing
    Performance constraints to meet timing
    Routing density constraints to avoid congestion
    Constraints on off-grid routing
    Blocked routing regions

Routing Outputs

The main outputs of routing are:

    Geometric layout of all nets in GDS format
    SPEF file for parasitics
    Updated SDC file with routed timing

In summary, routing is a critical step that completes the physical connections in the design while meeting various constraints. Efficient routing is essential for manufacturability and performance closure in modern VLSI designs.

Citations: [1] https://vlsitalks.com/physical-design/routing/ [2] https://www.vlsi4freshers.com/2020/01/routing.html [3] http://vlsibegin.blogspot.com/p/routing.html [4] https://www.vlsi-backend-adventure.com/routing.html [5] https://signoffsemiconductors.com/routing/
Lab - Routing of VSDBabySoC :

    Min & Max Routing Layer,set up in icc2_common_setup.tcl & top.tcl :

 </details> 

  <details> 
<summary>  DAY 19- STA using Prime Time  </summary>

 </details> 

  <details> 
<summary>  DAY 20- ECO using Prime Time </summary>

Engineering Change Orders (ECOs) play a crucial role in the VLSI (Very Large Scale Integration) design process, particularly in accommodating changes and rectifying errors in semiconductor chip designs. This process is essential for enhancing design efficiency and minimizing costs associated with production.
Definition and Purpose of ECO

An Engineering Change Order (ECO) is a method used to implement changes in the design of a semiconductor chip after it has undergone various stages such as synthesis, placement, and routing. The primary purpose of an ECO is to allow for modifications that can address functional errors, optimize performance, or meet new specifications without restarting the entire design cycle. This is particularly important in the competitive semiconductor industry, where time-to-market is critical.
Types of ECO

ECOs can be classified into two main categories based on their implementation scope:

    All Layers ECO: This type involves changes that require modifications across all layers of the chip, including both base and metal layers. It is typically used when significant changes are needed, such as updates to hard macro cells or extensive modifications that cannot be confined to a few layers.

    Metal-Only ECO: This approach focuses on making changes only to the metal layers, minimizing the need to alter the base layers. This is often preferred to reduce costs associated with mask production, as the base layers are generally more expensive to modify.

ECO Process

The ECO process typically involves several key steps:

    Specification of Changes: The first step is to define the changes needed in the design.

    Netlist Comparison: The modified netlist is compared against the original "golden" netlist to identify differences.

    Placement and Routing Updates: The design undergoes placement adjustments for the new logic and routing optimizations to accommodate these changes.

    Verification: Formal verification is conducted to ensure that the changes meet the design specifications and do not introduce new errors.

Challenges in ECO Implementation

Despite the advantages of ECOs, challenges remain, particularly in terms of computational complexity and the physical constraints of the design. The need for automation in the ECO process is increasingly recognized, as manual implementations can be time-consuming and prone to errors. Current research focuses on developing more efficient ECO tools that can handle the intricate requirements of modern VLSI designs.
Conclusion

In summary, Engineering Change Orders are a vital component of the VLSI design flow, enabling designers to efficiently implement necessary modifications while preserving previously invested efforts. As the complexity of designs increases, the need for effective ECO processes will continue to grow, highlighting the importance of ongoing research and development in this area.

Citations: [1] https://www.synopsys.com/glossary/what-is-functional-eco.html [2] https://www.linkedin.com/pulse/engineering-change-order-physical-design [3] https://vlsiuniverse.blogspot.com/2013/05/engineering-change-order-eco.html [4] https://www.techsimplifiedtv.in/2023/11/what-is-engineering-change-ordereco-in.html [5] https://people.eecs.berkeley.edu/~alanmi/publications/other/date20_eco1.pdf [6] https://www.youtube.com/watch?v=n63oF94F6JA

 </details> 





