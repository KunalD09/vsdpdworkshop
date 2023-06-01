# Physical Design workshop using OpenLane/Sky130 

# Floorplanning and PLacement and Library cells
a. Floorplanning concepts

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

b. Pre-placed cells

Consider the circuit as shown in image below, the circuit is divided into 2 parts also called IP's. 

![image](https://github.com/KunalD09/vsdpdworkshop/assets/18254670/c6c1f138-9ee4-44f2-96c9-6a767fa1d86f)

There can be many other IP's available such as memories, clock gates etc. 
These IP's are placed manually by the user before running automated place-and-route, so they are called pre-placed cells.

Therefore, the P&R tool cannot touch the placement of these IP's (pre-placed cells).

