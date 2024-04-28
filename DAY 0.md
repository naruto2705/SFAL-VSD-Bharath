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

#### Combinational Logic Optimisation

Squeezing the logic to get the most optimised design (in terms of Area amnd Power savings)

Constant Propagation ---> Direct Optimisation
Advantage
Boolean Logic Optimizations: Changing the boolean expression from Complex to Simple.

#### Sequential Logic optimization 

Constant Propogation: Output can be constant irrespective of gates
![IMG_0216](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/3b5c1973-d9bf-401c-808d-760c509bb653)




#### Advanced Topics:

State Optimization: Optimization of unused states
Cloning: Reusing for mapping
Retiming

![IMG_0217](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/5c160ea0-ea2d-4968-9d3b-cd36ec8f7081)
![IMG_0218](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/383ee9ab-868f-41ab-b043-183c4919e6bf)
![IMG_0219](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/d6e4bd6b-4cbf-4370-8c72-ad4f04d78761)


## Labs: Day 3


#### Sequential Logic optimization 

![Screenshot from 2024-04-27 19-08-47](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/49c57e84-1aba-4f6f-b57f-b7d5d0503e3b)


![IMG_0224](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/51e6d840-3ab2-49db-b2f9-89f765e90a25)
![IMG_0226](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/acd35f72-32a0-41b4-ba3e-6c1ad238a0bf)
![Screenshot from 2024-04-27 21-08-00](https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/63e4c083-1f9a-4ccf-b098-e0226e0861df)



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

   






