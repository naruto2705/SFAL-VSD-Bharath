# SFAL-VSD Program

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


![Screenshot from 2024-04-20 21-36-54](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/e4763fb2-f6b6-4bd5-8642-77f491c8a031)





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





### OpenSTA

### ngspice

### magic

### OpenLane



## Day 1 - Introduction to Verilog RTL Design and Synthesis

# Introduction to  iverilog

<img width="762" alt="iverilog" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/5e5f92df-ee42-43c4-b87f-5a2864659ff2">

# Introduction to gtk wave

# Intro logic synthesis 



<img width="611" alt="logic synthesis" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/106e9b17-3868-4849-bf8e-bf5ab8110c0b">


## DAY 2 - Timing libs, Hierarchical vs Flat Synthesis

## DAY 3 - Combinational and sequential Optimizations

### Intro to optimizations

Squeezing the logic to get the most optimised design (in terms of Area amnd Power savings)

Constant Propagation:
Advantage
Boolean Logic Optimizations: Changing the boolean expression from Complex to Simple.

#### Sequential Logic optimization 

Constant Propogation: Output can be constant irrespective of gates




#### Advanced Topics:

State Optimization: Optimization of unused states
Cloning: Reusing for mapping
Retiming


## DAY 4 - GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

### GLS Concepts and Flow using Iverilog

GLS: Running the test bench with Netlist as DUT( Design Under Test)
Netlist is Logically same as RTL Code(test Bench aligns with the design)
![IMG_0208](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/2a326ce6-4794-4988-bfc5-a546ba2d5fd2)


GLS verify the logical correctness of design after synthesis and Ensures whether the timing of the design are met or not (For this GLS needs to be run with delay Annotation).

![IMG_0209](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/de5091d8-a641-4d7f-93ef-163e1ab871b7)

Synthesis Simulation Mismatch
1. Missing Sensitivity List
2. Blocking vs Non-Blocking Assignments
3. Non Standard Verilog Coding

#### Missing Sensitivity List
![IMG_0210](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/477d0b4d-4e24-4778-9d72-706074a190b3)


### Blocking an Non-Blocking Statements in Verilog

#### Inside Always Block
![IMG_0211](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b5e8bd1b-0853-4b1a-9115-2b49cef6855c)

Caveats with Blocking Statements

![IMG_0214](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/8e1cecfb-c85e-4965-94b4-7f08e7fa3290)

![IMG_0215](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/4a74962a-9a67-4903-9776-6bbaba00bae0)

   






