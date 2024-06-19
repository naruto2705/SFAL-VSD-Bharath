# Floorplanning & Placement and library cells
### Floorplanning considerations


## Utilization Factor & Aspect Ratio

Two parameters are of importance when it comes to floorplanning namely, Utilisation Factor and Aspect Ratio. They are defined as follows:

Utilisation Factor =  Area occupied by netlist
                     __________________________
                        Total area of core

Aspect Ratio =  Height
               ________
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

