# Physical Design workshop using OpenLane/Sky130 

# Floorplanning and PLacement and Library cells

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

Similarly, when the inverters switch from logic 0 to logic 1, there is huge demand of current to charge the parasitic capacitances which causes drop in voltage called as Voltage droop. 

Since these phenomenon can destroy the functionality of the circuit, we must have power grid or power mesh as shown in image below. It means instead of having a common power supply, each sub-regions have VDD and GND nets local to them so that they can draw power from the nearby power supply net or discharge the current to nearest GND without causing voltage droop or ground bounce.

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c92be436-02c7-4c13-be91-3b192fa20a7b)

**e. Pin-placement and logical cell placement blockage**



![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/1836f1b5-caed-4328-a366-805d95c85b43)



