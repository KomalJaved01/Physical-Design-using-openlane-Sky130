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





