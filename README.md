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
<summary>  DAY 11  </summary>

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
<summary>  DAY 12  </summary>

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
<summary>  DAY 13  </summary>

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

(ATTACH THE PIC)


Converting avsdpll.lib file to avsdpll.db file

Syntax to convert the avsdpll.lib files to avsdpll.db


```
cd /home/sukanya/VSDBabySoC/src/lib
lc_shell
read_lib avsdpll.lib
write_lib avsdpll -format db -output avsdpll.db
```


The read_lib command shows some errors for avsdpll.lib. The errors are shown below.\

ATTACH THE PIC


The modification for avsdpll.lib is highlighted in the right with the original shown on the left. The power VDD and ground GND pins are removed along with other commented parts.

(ATTACH THE PIC)

After the modification, the avsdpll.lib is converted to avsdpll.db successfully.


(ATTACH THE PIC)


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
set link_library {* /home/bharath/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.db /home/sukanya/VSDBabySoC/src/lib/avsdpll.db /home/sukanya/VSDBabySoC/src/lib/avsddac.db}
set search_path {/home/bharath/VSDBabySoC/src/include /home/sukanya/VSDBabySoC/src/module} 
read_file {sandpiper_gen.vh  sandpiper.vh  sp_default.vh  sp_verilog.vh clk_gate.v rvmyth.v rvmyth_gen.v vsdbabysoc.v} -autoread -top vsdbabysoc 
link 
compile_ultra
write_file -format verilog -hierarchy -output /home/sukanya/VSDBabySoC/output/vsdbabysoc_net.v
report_qor > report_qor.txt
```

(ATTACH THE PIC)
(ATTACH THE PIC)


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
(ATTACH THE PIC)

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
set link_library {* /home/bharath/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.db /home/sukanya/VSDBabySoC/src/lib/avsdpll.db /home/sukanya/VSDBabySoC/src/lib/avsddac.db}
set search_path {/home/bharath/VSDBabySoC/src/include /home/sukanya/VSDBabySoC/src/module} 
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
<summary>  DAY 14  </summary>

Download the timing libraries in the path /home/bharath/VSDBabySoC/src/timing_libs from https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing


Syntax to convert .lib to .db for each

```
cd /home/bharath/VSDBabySoC/src/timing_libs
lc_shell
read_lib sky130_fd_sc_hd__ff_100C_1v65.lib
write_lib sky130_fd_sc_hd__ff_100C_1v65 -format db -output sky130_fd_sc_hd__ff_100C_1v65.db
```


Syntax to perform synthesis with SDC constraints

```
cd /home/bharath/VSDBabySoC
dc_shell
set target_library /home/bharath/VSDBabySoC/src/timing_libs/sky130_fd_sc_hd__ff_100C_1v65.db
set link_library {* /home/bharath/VSDBabySoC/src/timing_libs/sky130_fd_sc_hd__ff_100C_1v65.db /home/sukanya/VSDBabySoC/src/lib/avsdpll.db /home/sukanya/VSDBabySoC/src/lib/avsddac.db}
set search_path {/home/bharath/VSDBabySoC/src/include /home/sukanya/VSDBabySoC/src/module} 
read_file {sandpiper_gen.vh  sandpiper.vh  sp_default.vh  sp_verilog.vh clk_gate.v rvmyth.v rvmyth_gen.v vsdbabysoc.v} -autoread -top vsdbabysoc 
link
read_sdc /home/sukanya/VSDBabySoC/src/sdc/vsdbabysoc_synthesis.sdc
compile_ultra
write_file -format verilog -hierarchy -output /home/sukanya/VSDBabySoC/output/vsdbabysoc_net_ff_100C_1v65.v
report_qor > report/report_qor_ff_100C_1v65.txt
```

Repeat the above process for all the PVT corners inside timing_libs Table below shows the Worst Negative Slack (WNS) and Worst Hold Slack (WHS) for PVT corners

(ATTACH THE PIC)

They are represented in graphical format as follows

(ATTACH THE PIC)


 </details> 


 <details> 
<summary>  DAY 15  </summary>

 </details> 

  <details> 
<summary>  DAY 16  </summary>

 </details> 


  <details> 
<summary>  DAY 17  </summary>

 </details> 

  <details> 
<summary>  DAY 18  </summary>

 </details> 

  <details> 
<summary>  DAY 19  </summary>

 </details> 

  <details> 
<summary>  DAY 20  </summary>

 </details> 




