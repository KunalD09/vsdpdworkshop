# Physical Design workshop using OpenLane/Sky130 

# **DAY 1**

# Introduction to Openlane tool flow

OpenLANE is an opensource tool or flow used for opensource tape-outs. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Opensource Process Design Kit.

# How to talk to computers?
The RISC-V Instruction Set Architecture (ISA) is a language used to talk to computers whose hardware is based on RISC-V core. If a user wishes to run a certain application software on a computer, its corresponding C/C++/Java program must be converted into instructions by the compliler. The ouput of the compiler is hardware dependent. These instructions go as inputs to the assembler which outputs binary language that the hardware logic in the chip layout can make sense of. According to the bits received, the digital logic consisting of gates performs the function required by the user of the application software.

# SoC Design & OpenLANE
Components of opensource digital ASIC design
The design of digital Application Specific Integrated Circuit (ASIC) requires three enablers or elements - Resistor Transistor Logic Intellectual Property (RTL IPs), Electronic Design Automation (EDA) Tools and Process Design Kit (PDK) data.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/861e7da7-6992-4eca-8fd4-3222a019624e)

Opensource RTL Designs: github, librecores, opencores
Opensource EDA tools: QFlow, OpenROAD, OpenLANE
Opensource PDK data: Google Skywater130 PDK
The ASIC flow objective is to convert RTL design to GDSII format used for final layout. The flow is essentially a software also known as automated PnR (Place & route).

Simplified RTL2GDS Flow

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/a5be821f-9413-4415-aba5-00873a919790)

RTL2GDS flow

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/793047e4-a6bc-4f6b-9af2-652bcc809e09)

a. Synthesis: RTL Converted to gate level netlist using standard cell libraries (SCL)
b. Floor & Power Planning: Planning of silicon area to ensure robust power distribution
c. Placement: Placing cells on floorplan rows aligned with sites
d. Global Placement: for optimal position of cells
e. Detailed Placement: for legal positions and meeting timing
f. Routing: Connecting nets to the cells and meeting timing considering real clocks.
g. Signoff: Physical (DRC, LVS) and Timing verifications (STA)

# Openlane ASIC flow

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/08fc4fb0-4468-41aa-8b0f-635497be1118)

The ASIC design flow is an iterative process that is not static for every design. The details of the flow may change depending on applications, IP requirements, DFT insertion, and SDC constraints, however the base concepts still remain same. The flow can be broken down into below steps:

Architectural Design – A system engineer will provide the VLSI engineer with specifications for the system that are determined through physical constraints. The VLSI engineer will be required to design a circuit that meets these constraints at a microarchitecture modeling level.

RTL Design/Behavioral Modeling – RTL design and behavioral modeling are performed with a hardware description language (HDL). EDA tools will use the HDL to perform mapping of higher-level components to the transistor level needed for physical implementation. HDL modeling is normally performed using either Verilog or VHDL. One of two design methods may be employed while creating the HDL of a microarchitecture:

a. RTL Design – Stands for Register Transfer Level. It provides an abstraction of the digital circuit using:

i. Combinational logic
ii. Registers
iii. Modules (IP’s or Soft Macros)

b. Behavioral Modeling – Allows the microarchitecture modeling to be performed with behavior-based modeling in HDL. This method bridges the gap between C and HDL allowing HDL design to be performed

c. RTL Verification - Behavioral verification of design

d. DFT Insertion - Design-for-Test Circuit Insertion

e. Logic Synthesis – Logic synthesis uses the RTL netlist to perform HDL technology mapping. The synthesis process is normally performed in two major steps:

f. GTECH Mapping – Consists of mapping the HDL netlist to generic gates what are used to perform logical optimization based on AIGERs and other topologies created from the generic mapped netlist.

g. Technology Mapping – Consists of mapping the post-optimized GTECH netlist to standard cells described in the PDK.

h. Standard Cells – Standard cells are fixed height and a multiple of unit size width. This width is an integer multiple of the SITE size or the PR boundary. Each standard cell comes with SPICE, HDL, liberty, layout    (detailed and abstract) files used by different tools at different stages in the RTL2GDS flow.

i. Post-Synthesis STA Analysis: Performs setup analysis on different path groups.

j. Floorplanning – Goal is to plan the silicon area and create a robust power distribution network (PDN) to power each of the individual components of the synthesized netlist. In addition, macro placement and blockages must be defined before placement occurs to ensure a legalized GDS file. In power planning we create the ring which is connected to the pads which brings power around the edges of the chip. We also include power straps to bring power to the middle of the chip using higher metal layers which reduces IR drop and electro-migration problem.

k. Placement – Place the standard cells on the floorplane rows, aligned with sites defined in the technology lef file. Placement is done in two steps: Global and Detailed. In Global placement tries to find optimal position for all cells but they may be overlapping and not aligned to rows, detailed placement takes the global placement and legalizes all of the placements trying to adhere to what the global placement wants.

l. CTS – Clock tree synteshsis is used to create the clock distribution network that is used to deliver the clock to all sequential elements. The main goal is to create a network with minimal skew across the chip. H-trees are a common network topology that is used to achieve this goal.

m. Routing – Implements the interconnect system between standard cells using the remaining available metal layers after CTS and PDN generation. The routing is performed on routing grids to ensure minimal DRC errors.

**DAY-1 LABS**

Openlane flow initial run command to enter into bash mode is docker command as given in image below.

To run the entire flow, there is a script available flow.tcl which will run the flow from synthesis to GDSII file generation. We can run the script in interactive mode as well where we can run each from synthesis to GDSII flow separately.

To enter into interactive mode, enter following command

**./flow.tcl -interactive**

To invoke openlane, following command is used

**package require openlane 0.9**

Before running synthesis, it is important to set the environment variables, library paths and design files which can be done by the prep -design command. The design chosen here is RISC-V 32a design which is a simple processor design.

The following command sets the paths to picorv32a design files, constraints, and TCL scripts to run the physical design steps.

**prep -design picorv32a**

As shown in image below, the prep -design command does following items

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/37778d2c-ce6a-45ec-a2ce-174881d4e992)

1. sources the <design>/config.tcl file
2. set the Process Design Kit (PDK) root directory which is SKY130A.
3. Standard cell library used here is sky130_fd_sc_hd
4. Extracts the number of metal layers from the .tlef file. There are 5 metal layers available in sky130A PDK.
5. Merges the LEF files - The merged.lef file contain the metal layer area information and cell area information for the process corner which are required for the implementation steps.
6. It creates a runs/ directory where all the log files, results, reports and config files are stored.

There are many config files which contains switches to set the synthesis, floorplan, placement and routing strategies. 
1. Openlane has default settings
2. config.tcl file overrides the default settings
3. <process_corner>_config.tcl file (example - sky130A_sky130_fd_sc_hd_config.tcl) overrides the config.tcl settings - this file can be modified to change the settings according to requirements.

To run synthesis, run the following command. It will invoke Yosys tool and ABC tool which converts the RTL to the gate-level netlist. 
 
**run_synthesis**
         
Calculation of the Flop ratio
                  
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/1109df6e-5981-402e-95d8-b4d0444e5527)
         
According to synthesis results,
Number of D flip-flops = 1613
Total number of cells  = 14876
Flop ratio = (1613/14876) * 100 = 10.84%
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/bada6a9a-effb-4fe9-a3ef-ccb24a2dc0b4)
         
1. We may check the success of the synthesis step by checking the synthesis folder for the synthesized netlist file (.v file)
2. The synthesis statistics report can be accessed within the reports directory. It is usually the last yosys file since files are listed chronologically by date of modification.
3. The synthesis timing report is as follows:    
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/d9a83083-186a-4766-9683-4c328941f7fe)

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/8d2d58f3-6545-4907-a5e1-3e9a8cc8a4de)

Current synthesis results look bad because this synthesis run does not have timing driven switches set.
 
# **DAY 2**

# Floorplanning and PLacement and Library cells

# Lec 1:

**a. Floorplanning concepts**

For creating floorplan, considering the core and die size is very important. To compute the core and die size we need to know the dimensions of the standard cells and the nets. 
For example, there are 2 flip-flops and 2 gates as shown in below image

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/999edc1c-a555-47f2-a2cf-e6199282608b)

Assume height and width of each cell is 1 unit. By keeping all the cells inside the core as shown in below image, the total area occupied by the netlist is 4sq units. It means the entire core has been occupied and there is ZERO space left for placing any cells or nets. So, we need to select the bigger size core to place the entire netlist.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/b2c92428-7524-45ad-bc3a-c3880856afe7)

Therefore, two parameters are important for floorplanning, Utilization factor and Aspect ratio. 
They are defined as follows

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/91753a4a-a546-4b68-8968-f135728db99b)

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/40d2cf5a-3520-42e6-b32b-405d93f4faa7)


A Utilisation Factor of 1 means 100% utilisation leaving no space for extra cells or nets. 
However, practically, the Utilisation Factor is 0.5-0.6. 
Likewise, an Aspect ratio of 1 implies that the chip is square shaped. Any value other than 1 implies rectangular chip.

**b. Pre-placed cells**

Consider the circuit as shown in image below, the circuit is divided into 2 parts also called IP's. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c6c1f138-9ee4-44f2-96c9-6a767fa1d86f)

There can be many other IP's available such as memories, clock gates etc. 
These IP's are placed manually by the user before running automated place-and-route, so they are called pre-placed cells.

Therefore, the P&R tool cannot touch the placement of these IP's (pre-placed cells).

**c. De-coupling capacitors**

Consider the circuit shown in the below image receives power from the VDD source. Since there are resistances and inductances present in the wire, there is a voltage drop on the power supply nets and circuit receives less supply voltage. For example, if the supply voltage is 1V and volatge drop is 0.3V, then circuit will receive only 0.7V. It means the "logic 1" of the circuit can only reach 0.7V level only. If the output (logic 1) is fed to subsequent circuit, then 0.7V should lie within the noise margin range to be detected as logic 1 otherwise the circuit may enter into metastable state. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/67f6099f-2e7c-45e1-8ec9-6ae91b0a8f4d)

Therefore, to solve the voltage drop issue, physical designers place decoupling capacitors near to the circuit as shown in the image below. So, the volatge drop is less and circuit can produce a proper logic 1 voltage level.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/3063a307-7c7a-43e1-ada7-4d1ef0e1e4c1)

*Refer: Crosstalk videos for de-coupling capacitors and equations

**d. Power-planning**

Consider the circuit shown in image below, only one power source supplies power to the entire circuit. The orange line is a 16-bit bus fed into the inverter. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/28ee3c3a-1578-4849-a937-ee6349c006fc)

The issue seen because of having only one power supply is as follows. 

As shown in the image below, the input to the inverter is 1110_0011_1010_0101 which generates 0001_1100_0101_1010. when the inverters switch from logic 1 to logic 0, the parasitic capacitances discharge simultaneously which increases the volatge level at the ground pin of the power supply. This phenomenon is called Ground bounce. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/1836f1b5-caed-4328-a366-805d95c85b43)

Similarly, when the inverters switch from logic 0 to logic 1, there is huge demand of current to charge the parasitic capacitances which causes drop in voltage called as Voltage droop. 

Since these phenomenon can destroy the functionality of the circuit, we must have power grid or power mesh as shown in image below. It means instead of having a common power supply, each sub-regions have VDD and GND nets local to them so that they can draw power from the nearby power supply net or discharge the current to nearest GND without causing voltage droop or ground bounce.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c92be436-02c7-4c13-be91-3b192fa20a7b)

**e. Pin-placement and logical cell placement blockage**

As shown in the image below, the pins are placed between the core and die and planning for pin-placement is done by backend engineers for which they need to understand the design. The pin-blockage is placed between the pins to avoid placing the logic cells between core and die. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/8d355506-12f0-4b00-86c2-70354a46e46b)
 
# Lec 2:

**a. Netlist binding and initial placement**

I. **Library:** All the combinational logic gates (example AND, OR, NOR, etc), and sequential elements (example, flip-flops) have specific dimensions. The width and height of these cells are determined during library characterization by the foundry. The delay information, power pins, input and output pins of these cells are packaged into libraries which is provided by the foundry. The cells are of diffrent dimensions because they are meant to drive heavy loads in the circuit. For example, 2-input AND gate has multiple variants in one library. The 2-input AND gate may have 4sq units, 8sq units and 16sq units dimensions. Even though the funtionality of each of the AND gate is same, due to the size difference they can drive different loads. The 2-input AND gate with area 4sq units may drive a load of 1 unit with least amount of delay whereas the 2-input AND gate with 16sq units can drive load of 10 units. Except area, rest of the information is present in library.

II. **Placement:** In placement stage, we place cells within the core as shown in the image below. No cells are placed on the pre-placed cells shown in gray color in the image.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/e5d28dcb-ac7f-4dd0-a57f-59a2a52543d6)

**b. Optimize placement using wire-length and capacitance** **and c. Final placement optimization**

As shown in the image below, the cells are placed based on the pin-placement. For exmaple, the orange flip-flop (F1) is placed near to Din1 pin because the interconnect delay is less and signal integrity is maintained. Signal Integrity (SI) means the data driven by the pin is successfully captured by orange flip-flop (F1). Similarly, the orange flip-flop (F2) is placed near to Dout1 to maintain signal integrity.
In case of yellow colored circuit which is abutted meaning placed very close to each other has almost 0ps of interconnect delay. For the signal to be captured successfully by yellow flip-flop (F1) we need repeaters called buffers to maintain the signal integrity (also called signal strength).

Similarly, all the cells are placed by the tool maintaining signal integrity of the signals.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/95476c37-4526-462e-a8b8-85d0c97c91e7)

**d. Need for library and characterization**
All the combinational logic gates (example AND, OR, NOR, etc), and sequential elements (example, flip-flops) have specific dimensions. The width and height of these cells are determined during library characterization by the foundry. The delay information, power pins, input and output pins of these cells are packaged into libraries which is provided by the foundry. The cells are of diffrent dimensions because they are meant to drive heavy loads in the circuit. For example, 2-input AND gate has multiple variants in one library. The 2-input AND gate may have 4sq units, 8sq units and 16sq units dimensions. Even though the funtionality of each of the AND gate is same, due to the size difference they can drive different loads. The 2-input AND gate with area 4sq units may drive a load of 1 unit with least amount of delay whereas the 2-input AND gate with 16sq units can drive load of 10 units. Except area, rest of the information is present in library.

Libraries are of different types:
1. Standard cell library
2. IO library
3. PLL library
4. Power library
5. Noise Library
6. Derate library
         i. AOCV library
         ii. POCV library
         iii. SOCV library

**e. Congestion aware placement using Replace**

# Lec 3:

**a. Inputs for Cell Design flow**
Inputs for the cell design consists of following items.
1. Process Design Kits (PDKs) - It consists of Design Rule Check (DRC) file also called tech file (.tlef)
2. Layout vs Schematic (LVS) file
3. SPICE models (consists of parameters for the technology node i.e. MOS transistors)
4. library and user-defined specs.

**b. Circuit design steps**
While designing circuit following things are of importance
1. Cell Height - the cell height is determined by the power supply and GND tracks height. So, the cell height is equal to distance between 2 power supply tracks.
2. Supply Voltage - All the standard cells have to operate at particular supply volatge which is an user-defined specifications
3. Metal layers requirement - For example, Metal1 layer should be used for power supply only which could be an user specified requirement.

Circuit design flow consists of 3 different steps:
1. Circuit design step
2. Layout design step
3. Characterization step

This lecture explains about circuit design step. For circuit design, the logic function is converted to transistor level circuit.
For example, the given function is Fn = (B+D).(A+C) + E.F, then it is implemented into transistor level circuit as shown in below image.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/314a381c-3660-4ba9-9042-1247fa1d4f3d)

**Art of layout -> Euler's path + Stick diagrams (explained in detail in different lectures)**

**c. Layout Design step**

After we have designed the transistor level circuit, it is important to find the Euler's path. As shown in image below, the PMOS and NMOS Euler's path are specified. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/28d6338a-21b4-43ef-a8ba-937ae078a5eb)

The next step is to draw a stick diagram of the schematic (transistor level circuit). The stick diagram will look as shown in image below

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/7cde4e52-f73c-4cc4-bbf0-d5c9f3409403)

According to the transistor level circuit, the drain of PMOS transistors E & F are connected to the drain of NMOS transistors A, C & E using a blue-colored metal layer.  

Based on the stick diagram and DRC rules, the layout is drawn as shown in below image.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/34a09075-2576-4c84-97b5-6338352bae35)

The outputs of the layout include following items
1. Circuit description language
2. GDSII file (goes to foundry for manufacturing)
3. LEF (contains area information of the layout)
4. Extracted SPICE netlist (.cir file that contains parasitics i.e. Resistances and capacitances of the layout)

The next step is Characterization of the circuit which produces 
1. Timing .libs
2. Power .libs
3. Noise .libs
4. Functionality of the circuit

**d. Characterization Flow**
For characterization, we need following information:
1. Extracted Spice netlist (.cir) file - It contains the connectivity information of the circuit, capacitances and resistances for each net and sub-circuit (.sub) file. All of these are defined in .cir file.
   Below image shows the .cir and sub-circuit file example.
   
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/d8b2d3f6-9553-44a2-b159-50288e7e16f5)

2. The sub-circuit file includes the PMOS and NMOS models provided by the foundry.
   PMOS and NMOS models consists of the parameters of the PMOS and NMOS transistors respectively.
   
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/135eda38-cf42-4630-9444-8c1e6bc30288)

Steps to perform the characterization:
1. Read the SPICE models for PMOS and NMOS.
2. Read the Extracted spice netlist (.cir) file. 
3. Recognize the behavior of the buffer.
4. Read the sub-circuit (.sub) file.
5. Connect the DC power supply to the inverters as shown in below image.
6. Apply the stimulus which can be a square wave or sine waive.
7. Connect the output load (Capacitor).
8. Write the command to simulate in .cir file. It can be a Transient analysis (.tran is the command) or DC analysis (.dc is the command). 
9. Feed all the above information to the software GUNA which will produce timing, power and noise .libs file.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/9d99e328-041b-4d89-84ef-dd24560a8c64)

# Lec 4:

The first is the timing characterization. All the waveforms below are taken from the inverter and buffer circuit described above.

**a. Timing Threshold Definitions:**

1. **Slew_low_rise_threshold:** As indicated by the arrow, the low value of the voltage of the rising waveform which is typically about 20%-30% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/e24c531e-fbe5-4d27-8c4d-1b2fd37e9f0b)

2. **Slew_high_rise_threshold:** As indicated by the arrow, the high value of the voltage of the rising waveform which is typically 70%-80% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/b09646d9-cf54-4d2e-80ba-44b872570847)

3. **Slew_low_fall_threshold:** As indicated by the arrow, the low value of the voltage of the falling waveform which is typically 20%-30% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/9be5ef66-7e16-4c97-9248-bcf378361c44)

4. **Slew_high_fall_threshold:** As indicated by the arrow, the high value of the voltage of the falling waveform which is typically 70%-80% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/b565226a-fe39-4783-96a6-9103a7566f4e)

5. **in_rise_threshold:** As indicated by the arrow, the value of the voltage of the input rising waveform which is typically 50% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/151e78ae-d605-42de-af2c-8ad1c7f5ce91)

6. **out_rise_threshold:** As indicated by the arrow, the value of the voltage of the output rising waveform which is typically 50% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/f38350d2-7664-4a36-9a3f-c587aa646c2d)

7. **in_fall_threshold:** As indicated by the arrow, the value of the voltage of the input falling waveform which is typically 50% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/cf6878bb-bf46-4405-87a9-37535e4bbb9b)

8. **out_fall_threshold:** As indicated by the arrow, the value of the voltage of the output falling waveform which is typically 50% of the VDD.
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c78d28ad-b098-4824-ad44-c49a48933e04)


b. **Propagation delay and Transient time:**

**I. Propagation delay is defined as follows:**

1. Propagation delay for rising waveform of buffer = time at out_rise_threshold - time at in_rise_threshold
2. Propagation delay for falling waveform of buffer = time at out_fall_threshold - time at in_fall_threshold

For example, as shown in image below, the time at out_rise_threshold is 3.23ns and time at in_rise_threshold is 3.207ns
So, propagation delay = 3.23ns - 3.207ns = 0.023ns or 23ps

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/857df7f1-84a3-4b98-b829-c613efd172d7)

As shown in below image, the negative delays for the cell are not accepted, so choosing the correct threshold value is very important

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/522babbe-a98e-459c-baef-b7245d7bdb4f)

**II. Transition time:**

Transition time = time at Slew_high_rise_threshold - time at Slew_low_rise_threshold

For example, as shown in image below, for rising waveform, the difference in time is 26ps for input slew.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c48372d2-5e9b-4938-9154-b6454c54e8ca)


DAY-2 LABS

**Floorplan run on OpenLANE & view in Magic**
Importance files in increasing priority order:
      - floorplan.tcl - it contains the system default settings
      - config.tcl - it overrides the default settings in floorplan.tcl
      - sky130A_sky130_fd_sc_hd_config.tcl - the settings in this file will override config.tcl file settings
         
Floorplan envrionment variables or switches:
      - FP_CORE_UTIL - floorplan core utilisation
      - FP_ASPECT_RATIO - floorplan aspect ratio
      - FP_CORE_MARGIN - Core to die margin area
      - FP_IO_MODE - defines pin configurations (1 = equidistant/0 = not equidistant)
      - FP_CORE_VMETAL - vertical metal layer
      - FP_CORE_HMETAL - horizontal metal layer
         
Note: Usually, vertical metal layer and horizontal metal layer values will be 1 more than that specified in the files
         
To run the picorv32a floorplan in openLANE, use the below command

**run_floorplan**
         
To view the floorplan, Magic is invoked using below command after moving to the results/floorplan directory:
 
**magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &**

The floorplan of the picorv32a design looks as shown in the image below

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/8ba2b696-a797-4e36-a7ad-270094d24726)

By using left & right button of the mouse and "z" key, we can zoom into the floorplan. As shown below the horizontal IO pins are placed using metal 3 layer. Also, the decap_3 cells are placed on the left side of the floorplan. To select the IO pin, hover the cursor over the IO pin and press "s" key. 
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/70b0f623-f353-45d4-944a-ae0126cad390)

Type "what" command on the tkcon window to obtain the details of the IO pin, standard cells and any other cells in the design. The IO pins are placed equidistant because the FP_IO_MODE is set to 1.The metal layer for Horizontal IO pins is set to 2 in the floorplan default settings, so the openroad tool implemented using metal layer 3.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/f1226c77-797b-4dc6-aacd-62f418d74cdd)
         
The next step is to run the placement. Following command is used to run the placement.

**run_placement** 
 
In Placement stage, the standard cells are placed in optimized way to meet the timing and area requirements. Placement occurs in 2 different ways
    1. Global placement: The main objective of global placement is to reduce the wire length between the standard cells so that we can meet timing requirements. The Overflow (OVFL) parameter is important. The value of OVFL should decrease and reach 0 which means the design does not have congestion issues and meets the design requirements. If the value is near to 1, then the design will fail to meet the requirements and we need to add constraints, change strategy of synthesis, and floorplan to improve the results.
 
 ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/f3f2d6a6-bbe9-43cb-9924-04cbbab3f452)
 
 The Overflow parameter is 0.099 which is close to 0.
 
    2. Detailed placement: In detailed placement, legalisation is the main objective. Here, the cells must lie between power rails and the cells must be abutted with each other on the power rails to avoid the DRC violations.
 
To view the floorplan, use following command
 
**magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &**
 
After placement stage the chip looks as shown in the image below
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/18a9e366-fc92-469e-b114-8294b191a3fb)


















