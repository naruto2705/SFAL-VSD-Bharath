  <details> 
<summary>  DAY 1: Inception of EDA and PDK  </summary>


<img width="811" alt="OP1" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/363d1be2-eb70-4299-a561-52ab47f56cfd">
<img width="818" alt="OP2" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/50f25a0d-2223-4807-87d9-5ee724ed53c3">



<img width="1150" alt="OP3" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b4978c3c-e4ec-41ad-8788-10acaf0edd9c">
<img width="1145" alt="OP4" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/58ad08cf-af03-436b-b303-609a7d1cf4f5">
<img width="1133" alt="OP5" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/e286118e-9bd2-493c-a837-237fd744093b">



## Simplied RTS to GDS
<img width="1133" alt="OP6" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7f610da5-ef10-4ab5-92d4-62cdcc3f1538">
<img width="1131" alt="OP7" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/ec64291f-5eac-45af-8b0d-8ede619d8b57">
<img width="1129" alt="OP8" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/037628cd-7f6d-4073-8efa-5462c5e2cfa9">
<img width="1129" alt="OP8" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/037628cd-7f6d-4073-8efa-5462c5e2cfa9">
<img width="1132" alt="OP9" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/f8873f2f-157f-4363-912f-0b5ab5234603">
<img width="1130" alt="OP10" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/1e427bb3-38b9-46ca-a166-abc3836f70c4">
<img width="1133" alt="OP11" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/4a6da7d6-1963-4a85-ab71-72073f8fca63">
<img width="1132" alt="OP12" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/efc8b542-5f40-4df2-8b27-ce3a58dfb73b">
<img width="1132" alt="OP13" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/4eb7673f-fbf2-4635-a661-81ca980d941a">













 </details> 


  <details> 
<summary>  DAY 2: Good Floorplan vs Bad Floorplan  </summary>

<img width="1201" alt="OP14" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/9f328b3e-3eb3-4d35-92a7-55f3eb1103a9">
<img width="1175" alt="OP15" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/db0c67af-8cc8-4239-95a8-117872ae445a">
<img width="1168" alt="OP16" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/90ecf629-6cd4-4b5f-a68e-789fc2204f1d">
<img width="1152" alt="OP17" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b3eaedb9-83c0-42ea-844c-f07b7b1747df">
<img width="1129" alt="OP18" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/3753850e-2f1d-4d75-be59-906dc8d71a71">
<img width="948" alt="OP19" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/56a4642e-ce64-4249-a294-55634278e5e4">
<img width="1113" alt="OP20" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/0d032e8f-0a28-4add-a666-ea11974f648c">

<img width="1135" alt="OP21" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/6a36075b-9e34-41fe-a3b3-3daef31a16b0">
<img width="1094" alt="OP22" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/52737ce6-07ee-4ff2-b8e0-cdc285b3ab10">
<img width="1169" alt="OP23" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/e6f874b8-2faa-474d-8019-6411e988433d">
<img width="1196" alt="OP25" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/096ecea0-9767-448d-a83f-1451580ca59d">
<img width="1203" alt="OP26" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/bc9302c3-f925-40b1-b381-5e1a983d74e3">



1 - Chip Floorplan Considerations

Utilisation Factor =  Area occupied by netlist
                   ------------------------------
                        Total area of core

Aspect Ratio =  Height
             -----------
                Width

A Utilisation Factor of 1 signifies 100% utilisation leaving no space for extra cells such as buffer. However, practically, the Utilisation Factor is 0.5-0.6. Likewise, an Aspect ratio of 1 implies that the chip is square shaped. Any value other than 1 implies rectanglular chip.
Pre-placed cells

Once the Utilisation Factor and Aspect Ratio has been decided, the locations of pre-placed cells need to be defined. Pre-placed cells are IPs comprising large combinational logic which once placed maintain a fixed position. Since they are placed before placement and routing, the are known as pre-placed cells.
Decoupling capacitors

Pre-placed cells must then be surrounded with decoupling capacitors (decaps). The resistances and capacitances associated with long wire lengths can cause the power supply voltage to drop significantly before reaching the logic circuits. This can lead to the signal value entering into the undefined region, outside the noise margin range. Decaps are huge capacitors charged to power supply voltage and placed close the logic circuit. Their role is to decouple the circuit from power supply by supplying the necessary amount of current to the circuit. They pervent crosstalk and enable local communication.
Power Planning

Each block on the chip, however, cannot have its own decap unlike the pre-placed macros. Therefore, a good power planning ensures that each block has its own VDD and VSS pads connected to the horizontal and vertical power and GND lines which form a power mesh.
Pin Placement

The netlist defines connectivity between logic gates. The place between the core and die is utilised for placing pins. The connectivity information coded in either VHDL or Verilog is used to determine the position of I/O pads of various pins. Then, logical placement blocking of pre-placed macros is performed so as to differentiate that area from that of the pin area.
Floorplan run on OpenLANE & view in Magic

2 - Library Binding and Placement
3 - Cell Design and Characterization Flow 



### LAB:


## 2 - Library Binding and Placement


<img width="1209" alt="OP28" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/7376bb16-e080-454d-ba4c-e34db35c1f66">
<img width="1205" alt="OP29" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/b91035f2-3a13-4e10-a496-e88a61183714">
<img width="1206" alt="OP30" src="https://github.com/naruto2705/SFAL-VSD-Bharath/assets/34330742/69b8dffa-e534-49e7-8cfd-52aa6036bd58">


## 3 - Cell Design and Characterization Flow

 </details> 

  <details> 
<summary>  DAY 3: Design Library cell using magic layout and ngspice characterization  </summary>

 </details> 

  <details> 
<summary>  DAY 4: Prelayouttiming analysis and importance of good CTS  </summary>

 </details> 

  <details> 
<summary>  DAY 5: FInal steps to RTl to GDSII using triton route and OpenSTA  </summary>

 </details> 
# Floorplanning & Placement and library cells
### Floorplanning considerations


## Utilization Factor & Aspect Ratio

Two parameters are of importance when it comes to floorplanning namely, Utilisation Factor and Aspect Ratio. They are defined as follows:


    Importance files in increasing priority order:

    floorplan.tcl - System default envrionment variables
    conifg.tcl
    sky130A_sky130_fd_sc_hd_config.tcl

    Floorplan envrionment variables or switches:

    FP_CORE_UTIL - floorplan core utilisation
    FP_ASPECT_RATIO - floorplan aspect ratio
    FP_CORE_MARGIN - Core to die margin area
    FP_IO_MODE - defines pin configurations (1 = equidistant/0 = not equidistant)
    FP_CORE_VMETAL - vertical metal layer
    FP_CORE_HMETAL - horizontal metal layer

