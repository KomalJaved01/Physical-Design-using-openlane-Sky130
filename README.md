# Physical-Design-using-openlane-Sky130

# Introduction
This workshop is about complete RTL to GDSII flow of picorv32a design using open source tools provided by OpenLane and Sky130A PDK.  Following are the main objectives of this workshop:
-	Basic inception of PDKs, libraries and openlane simulation flow
-	Incorporation of custom cell in library using ngspice and magic
-	Resolving design rules violation during layout
-	Design improvement by analyzing reports generated and controlling design parameters at each stage 
-	Preforming post synthesis, pre layout and post layout timing analysis 

# DAY1
Day 1 gives introduction to basic terminologies used in VLSI design, introduction to openlane, RISCV and Sky130 PDK.
## Basic Terminologies
### Core and Die
A core is an area in the chip where all the circuit resides. It encapsulates all the combinational circuit, soft and hard IPs, and nets. Die is an area of chip that encapsulates the core and IO pads. 
### IO Pads 
Io pads are the pins that act as the source of communication between core and the outside world. Their placement is crucial in circuit designing as they should be placed close to the circuit to which they communicate to avoid unnecessary delays, extra wire length and voltage drops. 
### RISCV ISA
An Instruction set architecture converts any programming language into hardware understandable language. RiscV provides an open source instruction set architecture. These instructions are then translated into 1s and 0s. Hardware should be designed such that it can perform the required operation as given by the instructions.
### OpenLane and Sky130
OpenLane encapsulates open source EDA tools, libraries, PDKs and designs that are required to generate GDSII of any given RTL design.  PDK is based on 130nm openpdk provided by Skywater. Following diagram gives insight to openLane flow mentioning tools required at each step.
![](op.png)
## Lab 1
### OpenLane setup
To setup openlane, first we need to clone the repository from github. Once the setup is done we may invoke the tools either interactively of automatically. To work in interactive mode, type the following command in the terminal:
```sh
./flow.tcl  -interactive  
```
This will open interactive shell like this:

![](op1.png)

Now we need to check whether required open lane package is installed by using
```sh
package require openlane 0.9
```
Next step is to prepare the design. All the designs reside in the design folder. We may use any design of our choice. This workshop focuses on picorv32a design. 
```sh
prepare -design picorv32a
```
![](op2.png)
Once preparation is complete, first step is to synthesize the design. Openlane performs this step using yosys and also performs post synthesis timing analysis while applying DRC checks specified by the designer in .sdc file using OpenSTA. To run synthesis, use the following command:
```sh
run_synthesis
```
Here is the netlist created after synthesis. It shows total no. of cells, wires, gates, flops, chip area etc. of the design. 

![](op3.png)

Once synthesis is completed, timing reports indicate whether timing constraints have been met or not. In our case slack is positive and is met:

![](op4.png)

# Day 2
Day 2 gave insight to floor planning, its related terms and  how to control configuration parameters.
## Basic terminologies
### Aspect Ratio
It is the ratio of width to height of cell. The width and g=height of vell are measured in microns where 1µm=1000DB (data base units). Aspect ratio of 1 means the chip is of  square shape. Aspect ratio other than 1 indicates rectangular shape chip.
### Utilization and utilization factor
Utilization defines how much area of the chip is occupied by the netlist. Normally the utilization is kept between 30 to 40% of total chip area to keep space for routing and clock tree buffers. Utilization factor is given as:
Utilization factor = Area occupied by netlist/Total chip area

![](op5.png)

### Floor planning
Floor panning is the step where we decide:
-	Arrangement of pre placed cells and macros in the chip
-	Location of IO pads
-	No. of power pads
-	Power distribution network

The main objectives of floor planning are to minimize:
- Chip area
-	Delay
-	Routing congestion

Floor planning requires following parameters:
-	Netlist
-	Area requirements
-	Power requirements
-	Timing constraints
-	Physical partitioning
-	I/O and macro placement (optional)

At the end of floor planning step we get:
-	Die/block area
-	IO pins placed
-	Macros placed
-	Power grid designed
-	Standard cell placement areas

### Pre placed cells
Pre placed cells are the cells whose location is fixed on a chip and they cannot be moved around during placement. The floor planning tools decide the placement of standard cells keeping in consideration placement of macros. Macros can be used several times in a design. Typical examples of macros are memory blocks, clock gating cells, comparators etc.
### Decoupling capacitors
Decoupling capacitors are used in the design to compensate the voltage drop of long wires and nets as considerable voltage drop may cause the circuit to function abnormally. Macros in the chip are always surrounded by decoupling capacitors commonly referred to as decap cells. 
### Power planning
Power planning ensures continuous voltage and ground supply to all the cells in the circuit thus preventing voltage bounce and voltage droops. During floor planning, a grid of multiple Vdd and GND rails is laid down and each cell is given power from specific rail.

![](op6.png)

## Lab 2
Lab 2 is about floor planning of the design synthesized in lab 1. We may control different parameters available in the file:
```sh
/openaneflow/configuration/Readme.md
```
To configure a certain parameter, type the following in command window:
```sh
set $env(parameter name) value
```
This will overwrite the value of that certain parameter already configured in either skr130 config file or design’s own config file. Fig below shows some of the configuration parmeters.

![](op7.png)

Tools available in openlane for floor planning are ioplacer, pdn and trapcell. To run floorplanning type the following command after design synthesis:
```sh
run_floorplan
```

![](op8.png)

Once floorplanning is completed, we may use magic tool to analyze the plan. To invoke magic following command is used:
```sh 
magic -T <path to .tech file> read lef <path to .lef file> read def <path to .def file>
```
Below diagrams show the layout obtained after floorplanning. 

![](op9.png)

Zoomed in version of layout shows decap cells and pins orientation. On selecting a particular pin and typing 'what' in the tkcon window we may get info of that part of laout such as metal layer.

![](op10.png)

To run placement type the following command 
```sh
run_placement
```

The figure below shows results after placement 

![](op11.png)

# Day 3
Day 3 is about cell design and characterization flow and how a cell is fabricated. We designed a custom cell (a CMOS inverter), extracted its spice netlist, created it lef file and included it in merged.lef file.
## Cell design Flow
### Required inputs
To make a custom cell, we should have PDKs, DRC rules, LVS models, spice models, library and user defined specs. User defined specs include parameters such as cell height, width, voltage, noise to margin ratio, metal layers, gate length etc. It is the responsibility of library developer to fulfill these specs.
### Design steps
Once the specs are finalized, first step is to design the circuit on transistor level. After it, its layout is  designed making sure that its satisfies all design rules  and then its characterization is done.
### Generated outputs
-	Lef
-	GDSII
-	Timing, noise, power libs











