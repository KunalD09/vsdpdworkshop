# Physical Design workshop using OpenLane tool flow/Sky130A PDK 

# Table of Contents

DAY-1: Inception of opensource EDA, OpenLANE and SKY130A PDK

    1. Introduction to Openlane tool flow
    
    2. How to talk to computers?
    
    3. Soc Design & OpenLANE
    
    4. Openlane ASIC flow
    
    5. DAY-1 LABS: Openlane flow 

DAY-2: Floorplanning and introduction to library cells

    1. Floorplanning and PLacement and Library cells
    
       Lec 1: 
       
            a. Floorplanning concepts
            
            b. Pre-placed cells
            
            c. De-coupling capacitors
            
            d. Power-planning
            
            e. Pin-placement and logical cell placement blockage
            
       Lec 2:
            a. Netlist binding and initial placement
            
            b. Optimize placement using wire-length and capacitance
            
            c. Final placement optimization
            
            d. Need for library and characterization
            
       Lec 3:
       
            a. Inputs for Cell Design flow
            
            b. Circuit design steps
            
            c. Layout Design steps
            
            d. Characterization Flow
            
       Lec 4:
       
            a. Timing Threshold Definitions
            
            b. Propagation delay and Transient time
            
    2. DAY-2 LABS: Floorplan run on OpenLANE & view in Magic tool
    
DAY-3: Design library cell and SPICE simulation

    1. DAY-3 LABS: SPICE Simulations using NGSPICE
    
DAY-4: Pre-layout timing analysis

    1. DAY-4 LABS: Synthesis, Floorplan, Placement and Clock Tree Synthesis of picorv32a design
    
DAY-5: Final steps for RTL2GDSII generation

    1. DAY-5 LABS: Power distribution network and routing


----------------------------------------------------------------------------------------------------------------------------------------------------------


# **DAY-1: Inception of opensource EDA, OpenLANE and SKY130A PDK**

# 1. Introduction to Openlane tool flow

OpenLANE is an opensource tool or flow used for opensource tape-outs. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Opensource Process Design Kit.

# 2. How to talk to computers?
The RISC-V Instruction Set Architecture (ISA) is a language used to talk to computers whose hardware is based on RISC-V core. If a user wishes to run a certain application software on a computer, its corresponding C/C++/Java program must be converted into instructions by the compliler. The ouput of the compiler is hardware dependent. These instructions go as inputs to the assembler which outputs binary language that the hardware logic in the chip layout can make sense of. According to the bits received, the digital logic consisting of gates performs the function required by the user of the application software.

# 3. SoC Design & OpenLANE
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

# 4. Openlane ASIC flow

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

**5. DAY-1 LABS: Openlane flow**

Openlane flow initial run command to enter into bash mode is docker command as given below.

**docker**

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
 
# **DAY-2: Floorplanning and introduction to library cells**

# 1. Floorplanning and PLacement and Library cells

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

# Lec 3:

**a. Inputs for Cell Design flow**
 
Inputs for the cell design consists of following items.
 
1. Process Design Kits (PDKs) - It consists of Design Rule Check (DRC) file also called tech file (.tlef)
2. Layout vs Schematic (LVS) file
3. SPICE models (consists of parameters for the technology node i.e. MOS transistors)
4. library and user-defined specs.

**b. Circuit Design steps**
 
While designing circuit following things are of importance:
 
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

The outputs of the layout include following items:
 
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


**2. DAY-2 LABS:**

**Floorplan run on OpenLANE & view in Magic tool**
    
Important files in increasing priority order:
    
      1. floorplan.tcl - it contains the system default settings
    
      2. config.tcl - it overrides the default settings in floorplan.tcl
    
      3. sky130A_sky130_fd_sc_hd_config.tcl - the settings in this file will override config.tcl file settings
         
Floorplan envrionment variables or switches:
    
      1. FP_CORE_UTIL - floorplan core utilisation
    
      2. FP_ASPECT_RATIO - floorplan aspect ratio
    
      3. FP_CORE_MARGIN - Core to die margin area
    
      4. FP_IO_MODE - defines pin configurations (1 = equidistant/0 = not equidistant)
    
      5. FP_CORE_VMETAL - vertical metal layer
    
      6. FP_CORE_HMETAL - horizontal metal layer
         
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
 
In Placement stage, the standard cells are placed in optimized way to meet the timing and area requirements. 

Placement occurs in 2 different ways:
    
1. Global placement: The main objective of global placement is to reduce the wire length between the standard cells so that we can meet timing requirements. The Overflow (OVFL) parameter is important. The value of OVFL should decrease and reach 0 which means the design does not have congestion issues and meets the design requirements. If the value is near to 1, then the design will fail to meet the requirements and we need to add constraints, change strategy of synthesis, and floorplan to improve the results.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/f3f2d6a6-bbe9-43cb-9924-04cbbab3f452)
 
The Overflow parameter is 0.099 which is close to 0.
 
2. Detailed placement: In detailed placement, legalisation is the main objective. Here, the cells must lie between power rails and the cells must be abutted with each other on the power rails to avoid the DRC violations.
 
To view the floorplan, use the following command,
 
**magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &**
 
After placement stage the chip looks as shown in the image below
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/18a9e366-fc92-469e-b114-8294b191a3fb)
 
The below image shows the layout view where the standard cells are placed between 
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/0e6c5226-da8e-4e4c-b5e2-85cafa58d07c)

# DAY 3: Design library cell and SPICE simulation
  
**1. DAY-3 LABS: SPICE Simulations using NGSPICE**

These labs focus on SPICE simulations of the inverter layout created in Magic layout tool. So, we use NGSPICE simulator to charatcerize the inverter cell.
 
To perform the SPICE simulation, following files are required:
      1. sky130A.tech: This file contains the details of the technology node specific items. For example, the information of metal layers, nwell, pwell, diffusion and the area information of the metal layers, nwell, diffusion layers and so on. This file is read in Magic layout to interpret the layout drawn of the particular circuit.  
      2. sky130A_inverter.mag: This file contains inverter layout created using Magic layout tool.
 
The command to invoke the magic file to open the inverter layout is as follows:

**magic -T sky130A.tech sky130A_inverter.mag &**
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/9a6122d8-4382-4fb2-a9c6-0013307f0983)

To run the spice simulation on the inverter, follow below steps

1. Extract the spice netlist of the layout using - **ext2spice**
   The sky130A_inv.spice is generated that contains the pmos and nmos dimensions, power, ground, input and output pins connection to the inverter.
 
2. Complete the spice deck by defining the input stimulus and output load. The completed spice deck is shown in below image.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/bf82d88b-3894-42e9-9247-4489af9f12ea)

The spice deck describes below circuit diagram on the inverter.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/04b3bffd-f58e-4e81-9ddc-c7b0a4002d7a)

3. To run the spice simulation, use the commands 
       1. **ngspice sky130A_inv.spice &**
       2. **plot y vs time a**

4. Measure the values of the rise and fall time at 50% and calculate the time difference, that will give the propagation delay of the inverter at that process corner.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/e0428220-2a38-479a-b4e7-f8e24c76dc78)

The red line is the output of the inverter which is a falling waveform.
The blue line is the input to the inverter which is a rising waveform.

Time difference between 50% of falling waveform and 50% of the rising waveform = 2.31 - 2.15 = 0.16ns 
 
# DAY-4: Pre-layout timing analysis

**1. DAY-4 LABS: Synthesis, Floorplan, Placement and Clock Tree Synthesis of picorv32a design**
 
**Pre-layout timing analysis:**

Before diving into the timing analysis section, the purpose of this step is to generate the lef file of the custom inverter used for spice simulation earlier can be used in the picorv32a design. The objective of this lab is to understand how to include the external cells (designed from same technology node i.e. SKY130A) in the synthesis and place-and-route stage.
 
While designing the standard cells, it is critical to follow the guidelines that are described below
      1. The input and the output port of the standard cell must lie on the intersection of the horizontal and vertical tracks
      2. The width of the standard cell must be odd multiple of the track horizontal pitch.
      3. The height of the standard cell must be odd multiple of the track vertical pitch.
 
To ensure the above guidelines are followed, we need to activate the grid by pressing "g" key. 

The next step is to converge the grid with the track value. The track value is obtained from the track.info file. It contains the horizontal and vertical direction information and spacing between two layers. For example, the metal1 layer is at 0.23 microns and has spacing of 0.46 microns between 2 metal1 layers in horizontal direction.
 
**grid [xSpacing [ySpacing [xOrigin yOrigin]]]**
 
According to the guidelines, the input and the output port of the standard cell must lie on the intersection of the horizontal and vertical tracks which is shown in the image below.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/e7ac3e49-a20f-4a63-9d24-c929321b36aa)
 
As seen in the image below, the red arrow highlighting the inner rectangle occupies 3 boxes which is odd multiple of the X-spacing (i.e. 0.46um)

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/3cfb2665-0bd3-42f0-8c4a-ea559d26ce20)
 
Similarly, the inner rectangle occupies 7 boxes in vertical direction which is odd multple of the Y-spacing (i.e. 0.34um)
 
Therefore, the CMOS inverter layout is designed that meets all the requirements of the P&R tool.
 
To create the LEF, it is important to define the purpose of the port. For example, the VPWR and VGND ports are defined as "class inout".
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/a9f3fa85-2d6b-4c7b-85d5-3c346f18cab7)

Now we can extract the LEF file, use below steps to create the LEF file for the custom inverter.
**    1. save <filename>.mag (optional)
      2. lef write (this command will write the .lef file with the same name as of the cell name)**
 
The next part is the actual implementation (place-and-route) flow to generate the GDSII file. 
 
I. **Synthesis:** Synthesis is the first step of implementation that generates the gate-level netlist of the RTL design. In our case it is called picorv32a.synthesis.v. It is very crucial to solve timing violations at this step. 
 
In initial run, the config.tcl file had following variables and constraints set. The .libs were defined in the default settings in synthesis.tcl file. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/9ebacb7f-fafc-4760-9337-80b3520326ae)

Initial synthesis run produced very bad results as shown in the image below.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/5103ab68-4de6-49af-9a1c-8c14361bb129)

The chip area is 147712.918480 sq. um
 
WNS = -24.89ns

TNS = -759.46ns

The synthesis results are so bad because the switches to optimize the logic to meet timing requirements are not met.
 
So, after enabling following switches the synthesis timing results look much better
      1. set ::env(SYNTH_STRATEGY) "DELAY 1"
      2. set ::env(SYNTH_MAX_FANOUT) "4"
      3. set ::env(SYNTH_SIZING) "1"
      4. set ::env(SYNTH_BUFFERING) "1"
 
Below are the results after setting the above switches.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/927690a8-50a7-4271-959c-ba233c80962f)

Here the chip area has increased to 209181.872 sq. um

WNS = 0.0ns

TNS = 0.0ns
 
As we can see the difference, after enabling the synthesis switches improved the timing at the cost of area.
 
Another purpose of running this flow is to include the custom inverter in picorv32a design that was designed earlier. As shown in the image above the cell "sky130_vsdinv_new" is the custom inverter added by the synthesis tool while synthesizing. There are 2166 intances of the custom inverter in the synthesized netlist.
 
In this run, the synthesis produced good result. Let's assume synthesis timing was still negative. For example, WNS = -3.17ns. So, the question is what next? Should we continue with the floorplan or fix the timing at synthsis stage and then proceed. Of course, we have to meet timing at synthesis stage to make place and route smooth.
 
There are few ways to improve the timing at synthesis stage:
1. Add more exceptions such as multi-cycle paths and/or false paths.
2. Define clock relationships properly to avoid bogus violations between asynchronous clocks.
3. Replace the low drive strength cell with a higher drive strength cell.
4. If allowed, we can replace the high density (hd) cell with high speed (hs) cell.

Here, we will take the step 3. i.e. replace low-drive strength cell with a higher drive-strength cell.

Following are the steps to improve the slack for the synthesis results
1. Invoke the OpenSTA tool by using following command
 
   **sta pre_sta.conf**
 
2. pre_sta.conf file is used for running STA in OpenSTA tool 
 
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/e683694a-63b8-40bd-8e79-64cc5da78941)
 
3. create a separate <filename>.sdc file which has contents as given in the image below for reference.
 
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/b5f1172b-63d6-460b-ac5e-634b99d6fff0)

4. After running STA, we get the same result as shown in the image below.
 
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c601c227-ca0e-40e0-b172-3ebf9104dfc9)
 
5. As shown in the image below, the delay of the buf_1 cell is 0.85ns which is too high. 
 
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/280ecfca-8589-4380-a121-519954993b64)
 
   We need to replace the cell using the command given below
 
   **replace_cell _44322_ sky130_fd_sc_hd_buf_4**
   
   The command above says replace the cell _44322_ (instance name of the cell) with sky130_fd_sc_hd_buf_4 (has drive strangth of 4).
 
   So, after replacing the cell, WNS went down to -1.0535ns as shown in the image below.
 
   ![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/cca3d593-439d-4bc1-9934-6978799a000a)
 
   We can keep repeating the same strategy of replacing the cells with higher drive strength cell until we are satisfied with the results.
 
 6. So, after modifying the netlist we need to use the modified netlist in floorplan and further steps.
    The command used to create a new netlist using OpenSTA tool is as follows:
 
    **write_verilog <filename>.v** 
 
II. **Floorplan:** After synthesis completed successfully and satisfied with the result, run the floorplan command "run_floorplan". This creates the picorv32a.floorplan.def file which is fed into the placement stage. We can set the floorplan parameters described in earlier section to guide the tool to create optimized floorplan for the design.
 
The run_floorplan command executes below steps
1. init_floorplan
2. place_io
3. global_placement_or 
 
At this stage the Overflow converges to 0.099 which means it is good.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/9eca52e7-48e8-4d2a-abc4-336a4bfaa7cf)
 
Even WNS is positive as shown in below image.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/7b3ae327-f0dc-4dc1-88c3-c15c7385a28e)

4. tap_decap_or
 
This command generates the Endcaps and Tapcells and finally generates the floorplan.def file for placement stage
 
III. **Placement:** After successful completion of floorplan stage, we can run the placement command - **run_placement**. This command generates the picorv32a.placement.def file which is an input to clock-tree synthesis stage.
 
Following are the switches that we can set to guide the tool to optimize the placement of the design
 
a. PL_TARGET_DENSITY - this indicates how much the deisgn can spread across the core area. By default it is set to 0.4 by default.
 
"1" - the cells are placed very close to each other
 
"0" - the cells are spreaded as far as possible in the given core.
 
b. PL_TIME_DRIVEN - This parameter indicates the placement is timing driven or not
 
"1" - placement is timing driven

"0" - placement is not timing driven
 
c. PL_LIB - where we can set the libs for the placement
 
Below image shows placement of standard cells in the core.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/139a6705-907f-44b6-ad0e-79b647b7cbaa)
 
Below image shows the power rails are shared with standard cells. 
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/3271dce7-c228-45a6-a850-a268244195f2)
 
Below image shows the custom inverter (sky130_vsdinv_new - name of the custom inverter) is present in the picorv32a design.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/de1cfbcb-d6b7-4a64-8efb-29c289b5b7bb)
 
Below image shows the setup timing at post-placement stage. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/2e4710e2-f27e-44fb-a15d-bb80466de6d1)
 
To report the worst timing report, use below command
 
**report_checks -fields {net cap slew input_pin}**
 
where the fields section specify the nets through which the datapath traversed in the design, parasitic capacitance value on the net, the rise or fall time of the signal (also called slew), and input pin of the next cell.
 
**Please Note: The slack here is 0ns which is good but it may become negative or positive depending on the implementation steps. The actual timing computation is performed once the real clocks come into picture. Again, if the slack would have been negative at placement stage then timing ECO have to be performed to improve the QoR. To perform timing ECO, open the OpenSTA tool to analyze the failing path as described above and generate a new modified netlist for next steps.** 
 
 _Follow the steps below to discover the real world of VLSI...._ :)
 
IV. **Clock Tree Synthesis:** After successfully completing the placement stage, we need to run the Clock-Tree synthesis.
 
Following are the parameters that controls the clock-tree synthesis:
 
a. CTS_TARGET_SKEW - the clock skew globally should be in ps. By default it is set to 20ps.
    
b. CTS_ROOT_BUFFER - specify the cell to be inserted for the root clock. By default, clkbuf_16 is used which has higher driver stength.
    
c. CLOCK_TREE_SYNTH - Enable clock-tree synth for TritonCTS. TritonCTS is the tool that does the clock-tree synthesis.
    
d. CTS_TOLERANCE - an integer value that represents a tradeoff between QoR and runtime. Smaller the value, better is the QoR at the cost of               increased runtime.
 
Command to run the CTS - **run_cts**
 
This stage generates picorv32a.cts.def file used for routing. 
 
The timing results are the performed with real clocks. Real clocks mean the clock path has actual clock-buffer, interconnect and clock skew used for timing analysis.
 
Below image shows the setup timing slack met
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/f5cbf33a-e5e1-40e6-9b75-a1438219dae0)

Below image shows the hold time slack met
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c7b15f98-b48c-440a-90ef-baa648b295d0)
 
# DAY-5: Final steps for RTL2GDSII generation
    
**1. DAY-5 LABS: Power distribution network and routing**
    
V. **Power Distribution Network:** This step of the P&R flow is used for creating the power mesh inside the core. 
 
As shown in image below, the power rails are created using metal1 layer which is distributed to the standard cells that determines the height of the standard cell.
 
The pitch of the metal1 layer is 2.72um which means the height of the standard cell is also 2.72um.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/05ec843a-464a-4710-b13b-3947755cafd9)

Below image shows the power mesh created and the green line is a strap that is connected to the VDD (metal1 layer) through a via.
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/fe6b0912-59f3-4de6-81c6-7f5b31447b2a)
 
As shown in the image below, the red and blue box inside the core are connected to the red and blue IO pad respectively. To ensure the power reaches inside the chip, there is a strap created inside the chip. For example, there is a vertical red line created to distribute the power inside the chip is called a strap. 
 
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/5263715e-63a7-422b-afd3-4bf0e4ebb6ab)

VI. Routing: The routing stage is routing all the interconnects to the cells. 
 
Following are the parameters we can set to control the routing of the interconnects to obtain the best optimized results:
 
1. GLB_RT_MAXLAYER: The number of the highest metal layer that can be used for routing.
  
2. ROUTING_STRATEGY: The routing strategy ranges from 0-3 and 14 where 0 means it is least effort to route the interconnects and not timing driven and 14 means the tool makes high effort to produce the results.   
 
Routing can be run using the command:
 
**run_routing**

In this lab, we have used the ROUTING_STRATEGY as "0". 

Below image shows the timing results of the routing stage:
    
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/4a8ba82a-5689-4fbf-a1bd-c9f5da9a57df)

Below image shows there are 0 violations during the routing stage. It means there are 0 DRC violations in the design.
    
![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/30c3912c-cf42-47d3-b151-870bf9e440bf)
 
Two files that are output of the routing stage:

1. picorv32a.def
2. picorv32a.spef
    
After the post-layout simulations confirm that the design meets all the specifications and the design is ready to be taped out, we generate the GDSII file which is sent to foundry for fabrication. GDSII file generation is the final stage of the IC design.  
 
