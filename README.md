# PHYSICAL-DESIGN-USING-OPENLANE-SKY130
AUTHOR: AVINASH TANTI

This repository documents my progress through the 2 Week Advanced Physical Design using OpenLANE/Sky130 workshop with complete RTL2GDSII flow organized by VSD as part of Level-3 of Chip Design for High School Program in collaboration with Intel India

## Section 1 - Inception of open-source EDA, OpenLANE, and Sky130 PDK DAY-1

## Introduction

**ASIC** stands for **Application-Specific Integrated Circuit**. An ASIC is a type of integrated circuit (IC) that is designed for a specific application or purpose. Unlike general-purpose ICs such as microprocessors or memory chips, which are designed to perform a wide range of functions, ASICs are tailored to perform a single function or a set of closely related functions efficiently.
## Types of ASICs

1. **Full-Custom ASICs**:
   - Designed from scratch, offering maximum performance and customization.
   - Require significant time, expertise, and cost for development.

2. **Semi-Custom ASICs**:
   - Utilize pre-designed and pre-verified blocks (IP cores or libraries) along with custom-designed portions.
   - Strike a balance between performance and design effort.

3. **Programmable ASICs (FPGAs)**:
   - While not strictly ASICs, Field-Programmable Gate Arrays (FPGAs) offer reconfigurable logic.
   - Can be programmed to perform specific functions, providing flexibility similar to ASICs but with less performance optimization.

## ASIC Design Flow

The ASIC design flow outlines the process of designing and fabricating an Application-Specific Integrated Circuit. The flow can be boardly be clasified into **Front End** & **Back End**:<br>

-**Front End** involes the following steps:

1. **Specification and Architecture Definition**:
   - Define the requirements and functionality of the ASIC.
   - Create the architectural design, including block diagrams and high-level logic.

2. **RTL Design and Simulation**:
   - Write Register Transfer Level (RTL) code using hardware description languages like Verilog or VHDL.
   - Simulate the RTL design to verify functionality and performance using tools like ModelSim or VCS.

3. **Synthesis & Gate Level Netlist**:
   - Convert the RTL code into a gate-level netlist using synthesis tools such as Synopsys Design Compiler or Cadence Genus.
   - Optimize the netlist for area, power, and timing constraints.<br>


-**Back End** involves the following steps:

4. **Static Timing Analysis (STA)**:
   - Perform STA to ensure that timing requirements are met and that there are no violations.
   - Address any timing issues through design modifications or constraints.

5. **Floorplanning and Place-and-Route**:
   - Define the physical layout of the ASIC including placement of functional blocks and routing channels.
   - Use tools like Cadence Innovus or Synopsys ICC for automated place-and-route.

6. **Physical Verification**:
   - Perform Design Rule Checking (DRC) and Layout vs. Schematic (LVS) checks to ensure layout correctness and adherence to manufacturing rules.
   - Address any violations found during physical verification.
   - GDS II file will be forwarded to foundry.
7. **Post-Silicon Validation**:
   - Fabricate the ASIC using semiconductor manufacturing processes.
   - Test and validate the fabricated ASIC to ensure functionality and performance meet specifications.

8. **Production and Deployment**:
   - Once validated, the ASIC is ready for mass production.
   - Deploy the ASIC in the target application or market.

![Design Flow]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/329f8418-93eb-4cf7-a33d-ce844012d08e)

<br><br><br>

## OpenLane:

**OpenLane** uses SkyWater's 130nm Process Design Kit (PDK) and incorporates many open-source Electronic Design Automation (EDA) tools for each of the above-mentioned flow steps. Some of the EDA tools used in OpenLane include:

- RTL Synthesis, Technology Mapping, and Formal Verification: Yosys + ABC
- Static Timing Analysis: OpenSTA
- Floor Planning: init_fp, ioPlacer, pdn, and tapcell
- Placement: RePLace (Global), Resizer, OpenPhySyn (formerly), OpenDP (Detailed)
- Clock Tree Synthesis: TritonCTS
- Fill Insertion: OpenDP/filler_placement
- Routing: FastRoute or CU-GR (formerly), TritonRoute (Detailed) or DR-CU
- SPEF Extraction: OpenRCX or SPEF-Extractor (formerly)
- GDSII Streaming out: Magic and KLayout
- DRC Checks: Magic and KLayout
- LVS check: Netgen
- Antenna Checks: Magic
- Circuit Validity Checker: CVC

The main goal of OpenLane was to create an open-source RTL to GDSII platform that generates a GDSII file without requiring human intervention. We can run OpenLane flow in **interactive mode** too using **"-interactive"** option. OpenLane design flow is described by the below image.

![OpenLane Design Flow]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/dc4855df-cec1-4bbc-8e01-4fffd0683d7b)
<br>

<br>
<br>
<br>


## Getting Started

To list the tools and PDKs we will be using, follow these steps in your terminal:

1. Navigate to the work folder on your desktop using the `cd` command:
   ```bash
   cd Desktop/
   cd work/
   cd vsdflow/
   cd work/
   cd tools/
   ls -ltr
![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 10_31_20 AM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/ef50c22e-12cb-4ea6-b6d1-499dac790d76)


2. Whatever work we will be doing, it will be in the openlane directory, to open it follow the below steps in terminal,
   ```bash
   cd Desktop/
   cd work/
   cd tools/
   cd openlane_working_dir/
   cd openlane/


3. To run OpenLane in interactive mode follow the following steps after opening the openlane directory as shown in above image:
   ```bash
   docker
   pwd
   ./flow.tcl -interactive
![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 11_40_27 AM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/e7efc385-c70f-4b3e-bd4b-b9fb45e87d41)

<br><br><br>
## The design flow of Picorv32a:


When we open the picorv32a directory in the terminal under the designs directory of the OpenLane directory, we will only see 3 files:

- `SRC` (which contains a Verilog-A file "picorv32a.v" & an SDC file that contains design constraints and timing assignments "picorv32a.sdc")
- `sky130A_sky130_fd_sc_hd_config.tcl`
- `config.tcl`

The order of timing and design parameters is as follows: `sky130A_sky130_fd_sc_hd_config.tcl` > `config.tcl` > `OpenLane default values`.

After opening OpenLane in interactive mode as shown above, we will begin the flow by creating various files needed using the command:

    `package require openlane 0.9<br>
    prep -design picorv32a`

If we list out the contents of picorv32a directory now we will see the new directory named runs created,
![Captures 3_28_2024 12_30_39 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/447c1b1b-8f20-4e4d-911c-671884346695)




If we open the runs directory, we will find various new directories like `results`,`reports`logs`,`temp` created to store the various outputs of the design flow.
![Captures 3_28_2024 12_34_25 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/746ef496-c437-4acc-bfcd-c9bcdfba7f42)



After preperation is completed we run synthesis using the command <br>
`run_synthesis`
<br>
openlane will run synthesis as well as Static Timing Analysis (STA) under the above command itself. After completion of the synthesis we will get a report as shown below.

![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 12_49_19 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/9e57ba65-928a-43e6-bfcd-955d481f05e1)


### Task-1:
**Flop Ratio:** It is the ratio of total number of D flip-flops to the total number of cells in a design.<br>
flop ratio = number of D flip-flop ÷ total number of cells<br>
<br>
![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 1_17_45 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/8dfac853-d159-4c57-b419-92e2bcadb1e1)

Total number of D flip-flop in the design = 1613 <br>
<br>
![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/ac67613d-e0d2-4d3f-aa90-17840d5e2e5e)

Total number of cells in the design = 14876<br>
therefore, flop ratio = 1613/14876 = 0.108429<br>
Therefore, the flop percentage = 10.84%

After synthesis, we initiate floor planning using:<br>

   `run_floorplan`

![Captures 3_28_2024 4_28_11 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/f2d90d6e-55c0-4071-8f05-5a0d696ce281)


![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 4_27_37 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/59668777-0588-426e-8cc5-891a292a1c15)


### Task-2:
**Die Area:**
![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/43823796-8276-4c77-839c-ad015e0311e0)


Area of die in micrometer = (660685*671405)*10^(12) = 443587.21um

#### Visualizing the floor plan using Magic:<br>
User
Area of die in micrometer = (660685*671405)*10^(12) = 443587.21um

#### Visualizing the floor plan using Magic:<br>
Open floor plan result & then pass the result to magic using below commands:
3. Whatever work we will be doing, it will be in the openlane directory, to open it follow the below steps in terminal,
  
         cd Desktop/
         cd work/
         cd tools/
         cd openlane_working_dir/
         cd openlane/
         cd designs/
         cd picorv32a/
         cd runs/
         cd 28-03_06-26/
         cd results/
         cd floorplan
         magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &


picorv32a floor plan in magic:
![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 5_01_06 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/075180bf-f6a8-4413-8fd1-e0a0824a90b5)

<br>
zommed view of the die (select the section you want to zoom by placing cursor on it & pressing `s` key from keyboard and press `z` for zoom in and press `shift+z` to zoom out)

![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/6be3fcc7-0d5b-4664-adbf-91a9777103e9)

After floor planning, we initiate placement using the following command in OpenLane:

      
      run_placement
 
![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 6_34_10 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/869b02f6-0d51-4f03-ac34-c04d23306dc4)


We can visualize the placement in Magic using the following in terminal in `placement` directory of `result` directory:
     
      
      
      magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &




![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 6_34_41 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/931eec16-fb18-45b0-b92a-ddccbed1abe1)


![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 6_35_00 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/f98cba70-b241-4cc6-a226-19bafbfaae4f)



Zoomed view of the placement with `power(Vdd & Vss) mesh` clearly visible as well as `standard cells` placed in `standard cell row`.
![vsdworkshop  Running  - Oracle VM VirtualBox 3_28_2024 6_59_26 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/ee6c59bb-dc5c-400b-8ed8-1018eaaaddcd)



# Day-3

## Importing vsdstdcell design from GitHub & analyzing using Magic Layout:

We need to clone the custom standard inverter repo [vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign).
Follow the following steps in terminal to clone the above mentioned repo into openlane directory.
       
         cd Desktop/work/tools/openlane_working_dir/openlane
         git clone https://github.com/nickson-jose/vsdstdcelldesign


We can verify weather the cloning was sucessful or not using `ls -ltr` to list the contents of openlane directory.
Now we need to copy the magic tech files from pdks directory to our vsdstcelldesign directory to avoid giving the complete address of tech file every time we use `magic -T` command to view the layout, navigateto the magic tech files and follow the steps shown below,



         cd /Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic
         cp sky130A.tech Home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 1_43_30 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/031697ec-7f35-4023-a15f-f9bb05e6678e)

         
<br> <br>         

now we can open the downloaded vsdstdcell inverter using magic tool,

      cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
      magic -T sky130A.tech sky130_inv.mag &
![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 3_07_38 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/107c1a44-3421-40e1-94bf-ce6138c61ad9)


### Verifying the different layers & connection of the inverter:

To know about a layer, place your cursor on it and press `s` from your keyboard, which will select the layer/cell, now type `what` in `tkcon` window of magic, it will give the name of the layer. 

![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 8_01_57 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/eb79f204-a5b5-4f93-a56d-45b9b0bede0a)

To verify or know about the connectivity of a layer, place your cursor on the layer and press `s` twice or thrice, magic will highlight all the connections of the desired layer.


Verifying the connectivity of output with the drain of PMOS & NMOS
![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 8_21_43 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/7aacaa60-52d4-4c58-bbc8-b5f8de3b7965)



Verifying the connectivity of source of PMOS to Vdd
![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 8_22_00 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/af1527ea-da16-4406-975e-330daefa1601)


### DRC error in Magic

Whenever a DRC error occurs, find the error by navigating to `DRC` in magic and clicking on `DRC Find next error`, the tkcon window will display what error we have & according to that design needs to be corrected.

![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 9_42_54 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/5c1dc88f-7bfd-4466-ad91-ebbb77c38790)



DRC error being highlighted
![vsdworkshop  Running  - Oracle VM VirtualBox 3_29_2024 9_43_04 PM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/6c6f4e32-789f-41b2-be26-75eabee157b3)

### SPICE extraction of inverter

To know in which which directory our tkcon window is opened used `pwd` command. Then use the command `extract all` command in tkcon window, this will create a new `.ext` in the same directory.

![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 10_27_43 AM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/a7ac6390-4287-44da-ab51-700763d0d3ef)



Verifying weather `.ext` file has been created or not.
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 10_27_51 AM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/0621a4b5-6536-444e-9a6a-cbc44193bdd8)




To get SPICE of `.spice` format, we use the following commands 


      ext2spice cthresh 0 rthresh 0
      ext2spice

To open the SPICE file, use the command `less (file name)`
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 10_34_41 AM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/ced6dba1-ed2f-4850-bdf1-fa7696928ffd)



To measure the size of unit grid box in magic, select one grid box & use the command `box` in `tkcon` window, whose output will look like the below image
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 10_54_46 AM]![image](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/69edf9a3-fb9f-403f-8a97-27304e73bbd3)



### Editing the SPICE file

`.spice` is edited as shown below in txt editor
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 11_40_28 AM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/c82377c4-4358-4517-9c74-b2cf8deb3b94)


after editing, install the ngspice simulator & run the `.spice` file as shown below
    
      

      sudo apt install ngspice 
      // password= vsdiat
      ngspice sky130_inv.spice

The above code should give you below output
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 11_42_21 AM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/7c12b973-5d9d-4e65-90b2-5c69c882c08d)


To plot the output, 


      plot y vs time a 

![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 11_48_13 AM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/d2b0bb1f-250b-421c-8345-05a31317aed9)
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 11_49_35 AM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/025bfaeb-fa58-4c3f-a70c-f0de7cb567de)


### Task-3
#### Characterization of the plot:
**Rise Transition:** The time taken by the output waveform to transit from a value of 20% of the maximum value to the 80 % of the maximum value.

Using right click, a new zoomed plot window cab be opened
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_07_22 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/b86c759b-166c-4617-811a-603cc5b49962)

![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_08_25 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/f5f38019-1d2d-4509-9f9b-398eaba23784)


ngspice will give the x & y coordinates of a point by just clicking on the points in plot.
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_22_30 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/b553a03c-deb5-4485-9830-cd9d89d3c804)

Therefore, Rise transition time = (2.24684 - 2.18233)ns = 0.06451ns 



**Fall Transition:** The time taken by the output waveform to transit from a value of 80% of the maximum value to the 20 % of the maximum value.
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_44_16 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/42db019b-45d7-456d-a4ad-c25fa84b033e)
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_43_55 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/3bc6033f-19f2-4335-85e6-6608deb44569)
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_43_21 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/02d4c66f-a20b-4c88-b300-06dd36055676)

Therefore the fall transition time = (4.09557 - 4.05304)ns = 0.04253ns 




**Rise Cell Delay /Rise Propagation Delay:** It is the diffrence time taken by Output to rise to 50% & Time taken by Input to fall to 50%
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_55_23 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/98f90509-f61c-4bd9-87ed-7120aee8abeb)
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 12_55_43 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/99fef724-adb7-4cb8-8ff4-eab3a4e961f9)
Therefor, Rise cell delay = (4.07805 - 4.05005)ns = 0.028ns


**Fall Cell Delay/ Fall Propagation Delay:** It is the difference time taken by Output to fall to 50% & Time taken by Input to rise to 50%
<br> similar to above we calculate the fall dealy


### Task-4
#### Identifying DRC errors in downloaded magic design & rectifying them:

Here we will download some deigns which have DRC errors and correct them as [the rules](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html)
<br><br>
We will download designs from [open circuits archive](http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz) following the below steps,


   
      // we will download it in home directory
      cd 
      wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
      // to extract the compressed file
      tar xfz drc_tests.tgz
      cd drc_tests
      // to list all the compnents of the folder
      ls -al
      // to open magic in better quality
      magic -d XR &

![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 3_36_22 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/dd8e73dd-6fc7-40ed-a261-b48a6046a0a3)
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 3_36_33 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/997df863-f060-4c5e-bb3a-b5f0fb86b581)


#### DRC failure to identify poly to polyresistor distance violation

![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 5_55_13 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/d74968d2-e373-4e22-82f3-5a24f8d4ffcd)
As we can see in the above image even tough the height of the box or distance between poly & polyresisitor is 21um, we are not getting a DRC error(incorrect poly.9), we need to rectify it.

Then to load the newly edited tech file into magic use the below code in `tkcon` window of magic,
      
      
     
      tech load sky130A.tech
      drc check

![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 8_18_48 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/d905d0dd-6200-48d2-ac5a-5cdb475b3b6e)
![vsdworkshop  Running  - Oracle VM VirtualBox 3_30_2024 8_21_03 PM](https://github.com/tejasbg19/VSDIAT_Advanced_Physical_Design_Using_Skywater130nm_PDK/assets/163899793/7b0533ee-9324-4428-9f3b-1f2f173c22c3)
As we can see magic is identifying the DRC violations.

**Incorrectly implemented poly.9 simple rule correction**

Screenshot of poly rules

![Screenshot from 2024-03-21 22-54-49](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/9260cf37-5933-44a1-8362-597183644334)

Incorrectly implemented poly.9 rule no drc violation even though spacing < 0.48u

![Screenshot from 2024-03-21 22-54-19](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/acfbcf69-020e-4b62-96bd-b7630aa74ef0)
![Screenshot from 2024-03-21 23-54-11](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a05bd29a-b181-4e26-826a-d32f12696b2c)

New commands inserted in sky130A.tech file to update drc

![Screenshot from 2024-03-21 23-58-44](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/dc30df54-3282-42f0-8e7d-fc4d8877ed64)
![Screenshot from 2024-03-21 23-09-50](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d112ca07-854c-41e7-8822-c269e21defea)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![Screenshot from 2024-03-21 23-13-11](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b18e8e07-ef0f-40fb-9b6d-8aae878a23c6)
![Screenshot from 2024-03-22 00-00-40](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d5afe8d8-691b-485d-a89a-8f901e18b56e)

**Incorrectly implemented difftap.2 simple rule correction**

Screenshot of difftap rules

![Screenshot from 2024-03-22 00-14-47](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/086b7a66-b60a-470a-b5c0-a5ac938ebec3)

Incorrectly implemented difftap.2 rule no drc violation even though spacing < 0.42u

![Screenshot from 2024-03-22 00-14-36](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a2d0d739-2df5-4eb5-ab78-c80d366e24e4)

New commands inserted in sky130A.tech file to update drc

![Screenshot from 2024-03-22 00-26-43](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b5892f9b-9c5d-4b1b-baa2-6fe45f3965b1)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![Screenshot from 2024-03-22 00-29-22](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a3f92160-6701-48fb-b6cf-e4c41dc4a531)

**Incorrectly implemented nwell.4 complex rule correction**

Screenshot of nwell rules

![Screenshot from 2024-03-22 00-51-34](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/4ad4901d-0b9a-4339-89e3-7bb3fce2766d)

Incorrectly implemented nwell.4 rule no drc violation even though no tap present in nwell

![Screenshot from 2024-03-22 00-52-51](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/87da8944-0ad8-455d-97ec-3909eac656c3)

New commands inserted in sky130A.tech file to update drc

![Screenshot from 2024-03-22 01-03-42](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/886c6930-6314-4a6f-97d9-6b8423444ac0)
![Screenshot from 2024-03-22 01-04-04](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d9808e9a-42c2-4421-9b82-2ef65a5a1ad7)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Change drc style to drc full
drc style drc(full)

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![Screenshot from 2024-03-22 01-10-25](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/49b1004d-f860-4ca7-86f4-4d79784a01cf)

## Section 4 - Pre-layout timing analysis and importance of good clock tree (26/03/2024 - 28/03/2024)

### Theory

### Implementation

* Section 4 tasks:-
1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.
2. Save the finalized layout with custom name and open it.
3. Generate lef from the layout.
4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.
5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow.
6. Run openlane flow synthesis with newly inserted custom inverter cell.
7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.
8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.
9. Do Post-Synthesis timing analysis with OpenSTA tool.
10. Make timing ECO fixes to remove all violations.
11. Replace the old netlist with the new netlist generated after timing ECO fix and implement the floorplan, placement and cts.
12. Post-CTS OpenROAD timing analysis.
13. Explore post-CTS OpenROAD timing analysis by removing 'sky130_fd_sc_hd__clkbuf_1' cell from clock buffer list variable 'CTS_CLK_BUFFER_LIST'.

* Section 4 - Tasks 1 to 4 files, reports and logs can be found in the following folder:

[Section 4 - Tasks 1 to 4 \(vsdstdcelldesign\)](https://github.com/fayizferosh/advanced-pd-using-openlane-sky130/tree/main/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign)

* Section 4 - Task 4 files, reports and logs can be found in the following folder:

[Section 4 - Task 4 \(src\)](https://github.com/fayizferosh/advanced-pd-using-openlane-sky130/tree/main/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src)

* Section 4 - Task 5 files, reports and logs can be found in the following folder:

[Section 4 - Task 5 \(picorv32a\)](https://github.com/fayizferosh/advanced-pd-using-openlane-sky130/tree/main/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a)

* Section 4 - Tasks 6 to 8 & 11 to 13 logs, reports and results can be found in following run folder:

[Section 4 - Tasks 6 to 8 & 11 to 13 Run \(24-03_10-03\)](https://github.com/fayizferosh/advanced-pd-using-openlane-sky130/tree/main/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03)

* Section 4 - Tasks 9 to 11 logs, reports and results can be found in following run folder:

[Section 4 - Tasks 9 to 11 Run \(25-03_18-52\)](https://github.com/fayizferosh/advanced-pd-using-openlane-sky130/tree/main/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52)

#### 1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.

Conditions to be verified before moving forward with custom designed cell layout:
* Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
* Condition 2: Width of the standard cell should be odd multiples of the horizontal track pitch.
* Condition 3: Height of the standard cell should be even multiples of the vertical track pitch.

Commands to open the custom inverter layout

```bash
# Change directory to vsdstdcelldesign
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of tracks.info of sky130_fd_sc_hd

![Screenshot from 2024-03-24 13-38-09](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/2a35eb22-dd5f-4b67-9712-cbd2a84b526a)

Commands for tkcon window to set grid as tracks of locali layer

```tcl
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```

Screenshot of commands run

![Screenshot from 2024-03-24 13-49-55](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d0d9c106-4e05-4e73-a7ed-3f718cb69b42)

Condition 1 verified

![Screenshot from 2024-03-24 13-51-55](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b74b31c8-cdc7-4dcb-9467-5a1787bfa5fe)

Condition 2 verified

```math
Horizontal\ track\ pitch = 0.46\ um
```

![Screenshot from 2024-03-24 13-55-07](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/e045e5b6-3592-4242-995d-de2049438ec5)

```math
Width\ of\ standard\ cell = 1.38\ um = 0.46 * 3
```

Condition 3 verified

```math
Vertical\ track\ pitch = 0.34\ um
```

![Screenshot from 2024-03-24 13-58-32](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a471b022-91ac-466a-8dd9-72b90f9c16c1)

```math
Height\ of\ standard\ cell = 2.72\ um = 0.34 * 8
```

#### 2. Save the finalized layout with custom name and open it.

Command for tkcon window to save the layout with custom name

```tcl
# Command to save as
save sky130_vsdinv.mag
```

Command to open the newly saved layout

```bash
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_vsdinv.mag &
```

Screenshot of newly saved layout

![Screenshot from 2024-03-24 14-33-20](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/0beb4300-2ebc-4364-8e3d-37fdb6d52f5b)

#### 3. Generate lef from the layout.

Command for tkcon window to write lef

```tcl
# lef command
lef write
```

Screenshot of command run

![Screenshot from 2024-03-24 14-35-55](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/6928c3dc-e633-414d-9ac1-71349cad4b9b)

Screenshot of newly created lef file

![Screenshot from 2024-03-24 14-37-19](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/15557990-33b4-4402-8c72-39b75da9ed07)

#### 4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.

Commands to copy necessary files to 'picorv32a' design 'src' directory

```bash
# Copy lef file
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```

Screenshot of commands run

![Screenshot from 2024-03-24 14-55-23](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/78559cee-ad3f-4301-83ae-df99f8417be3)

#### 5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow.

Commands to be added to config.tcl to include our custom cell in the openlane flow

```tcl
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

Edited config.tcl to include the added lef and change library to ones we added in src directory

![Screenshot from 2024-03-24 15-29-56](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/7b18f216-1160-4a65-91fd-998495ad3175)

#### 6. Run openlane flow synthesis with newly inserted custom inverter cell.

Commands to invoke the OpenLANE flow include new lef and perform synthesis 

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshots of commands run

![Screenshot from 2024-03-24 15-36-46](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/4170c3c5-1a95-4165-9461-03b298cc20ef)
![Screenshot from 2024-03-24 15-37-32](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/8f52942c-4b28-4abd-b9a0-d48f20a8255f)
![Screenshot from 2024-03-24 15-37-44](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/47849bfd-dc47-4d9c-9077-7fb672df4ead)
![Screenshot from 2024-03-24 15-45-08](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/0bc13ad3-d800-4681-b39d-8b64c9c9104f)

#### 7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.

Noting down current design values generated before modifying parameters to improve timing

![Screenshot from 2024-03-24 16-00-18](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/33fe575a-7459-4c59-8329-f142ba2099e5)
![Screenshot from 2024-03-24 16-13-01](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/13e42f0a-69e7-410d-b901-bc6c4976b7e1)

Commands to view and change parameters to improve timing and run synthesis

```tcl
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 24-03_10-03 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to display current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to display current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)

# Command to display current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshot of merged.lef in `tmp` directory with our custom inverter as macro

![Screenshot from 2024-03-24 23-46-25](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/55de3fc6-498d-4456-8e79-ae6e175d2ca6)

Screenshots of commands run

![Screenshot from 2024-03-24 17-09-04](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/62209cce-90c2-4c52-a218-25805b57ef3f)
![Screenshot from 2024-03-24 17-09-19](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/2d933bd5-a3b5-4d6d-a22f-5332bc3bf279)
![Screenshot from 2024-03-24 17-10-46](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a25b66af-9cf9-4ba9-adb6-f38ff85fa7cd)

Comparing to previously noted run values area has increased and worst negative slack has become 0

![Screenshot from 2024-03-24 17-11-08](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/81418082-747e-4702-b5ad-bb3e450eceb3)
![Screenshot from 2024-03-24 17-11-19](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a1bdb538-527c-4edd-877d-d4263e777321)

#### 8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.

Now that our custom inverter is properly accepted in synthesis we can now run floorplan using following command

```tcl
# Now we can run floorplan
run_floorplan
```

Screenshots of command run

![Screenshot from 2024-03-24 17-12-09](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/10a18995-0b7c-4f44-8ef4-cca9239652da)
![Screenshot from 2024-03-24 17-37-50](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/72966b69-cea0-4ae7-8dc0-c7130a8c750a)

Since we are facing unexpected un-explainable error while using `run_floorplan` command, we can instead use the following set of commands available based on information from `Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl` and also based on `Floorplan Commands` section in `Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md`

```tcl
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```

Screenshots of commands run

![Screenshot from 2024-03-24 23-38-07](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/07534ca7-b0db-4ea2-bbcd-ea7d44241d6c)
![Screenshot from 2024-03-24 23-38-54](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c9705150-f953-4372-a811-88cae6378d2f)
![Screenshot from 2024-03-24 23-39-56](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c16bccc6-c650-4a65-b1de-7035609520d7)

Now that floorplan is done we can do placement using following command

```tcl
# Now we are ready to run placement
run_placement
```

Screenshots of command run

![Screenshot from 2024-03-24 23-49-29](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/12788798-aac5-4cfb-9254-69fb3d4e8e70)
![Screenshot from 2024-03-24 23-51-08](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/41eaeae7-d398-417c-b89f-e9014a92a699)

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshot of placement def in magic

![Screenshot from 2024-03-25 00-16-54](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/9cb8b463-a0dd-402f-b881-2504686b8d04)

Screenshot of custom inverter inserted in placement def with proper abutment

![Screenshot from 2024-03-25 00-00-10](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/2fb0fc71-4784-4987-a55e-5d71dd35edbf)

Command for tkcon window to view internal layers of cells

```tcl
# Command to view internal connectivity layers
expand
```

Abutment of power pins with other cell from library clearly visible

![Screenshot from 2024-03-25 00-01-46](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b52d756d-430c-4e43-b514-db0084dc1794)
![Screenshot from 2024-03-25 00-05-35](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b8342668-c3fe-437e-b701-8c8be3740682)

#### 9. Do Post-Synthesis timing analysis with OpenSTA tool.

Since we are having 0 wns after improved timing run we are going to do timing analysis on initial run of synthesis which has lots of violations and no parameters were added to improve timing

Commands to invoke the OpenLANE flow include new lef and perform synthesis 

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Commands run final screenshot

![Screenshot from 2024-03-26 05-52-18](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/790d4852-7f1d-47b5-a64f-5735f6064b61)

Newly created `pre_sta.conf` for STA analysis in `openlane` directory

![Screenshot from 2024-03-26 05-53-06](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/0a02d055-f012-44bd-a750-4508bbfc771e)

Newly created `my_base.sdc` for STA analysis in `openlane/designs/picorv32a/src` directory based on the file `openlane/scripts/base.sdc`

![Screenshot from 2024-03-26 05-55-17](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/8c917d87-5507-4079-ba6b-facbf18c238f)
![Screenshot from 2024-03-26 05-55-38](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/e07d05db-2f06-47ab-bff7-2af88c39d001)

Commands to run STA in another terminal

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```

Screenshots of commands run

![Screenshot from 2024-03-26 06-04-28](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/32def177-5173-4dd7-ba3b-44e37846644c)
![Screenshot from 2024-03-26 06-05-07](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d8b8c46a-3a8b-458c-8c75-03f6577f5d17)
![Screenshot from 2024-03-26 06-05-53](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/ab396c91-430f-41ca-b9a4-dda72a91c4d5)
![Screenshot from 2024-03-26 06-08-51](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/aa1d50b0-9cb1-45bf-a641-b64842166b48)

Since more fanout is causing more delay we can add parameter to reduce fanout and do synthesis again

Commands to include new lef and perform synthesis 

```tcl
# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a -tag 25-03_18-52 -overwrite

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to set new value for SYNTH_MAX_FANOUT
set ::env(SYNTH_MAX_FANOUT) 4

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Commands run final screenshot

![Screenshot from 2024-03-26 06-20-29](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c5869cf5-ab95-46f9-9fd1-dc91e92455d8)

Commands to run STA in another terminal

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```

Screenshots of commands run

![Screenshot from 2024-03-26 06-22-31](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/0f3247fc-aaad-4e98-b23e-bdc9a361d08c)
![Screenshot from 2024-03-26 06-22-41](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/9fcc3975-0e03-4515-aee5-04b7c1c6a315)
![Screenshot from 2024-03-26 06-22-50](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/e19db773-3995-4af4-b0a3-188b02fdf454)
![Screenshot from 2024-03-26 06-23-01](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c22c85fb-1a94-4639-a731-da4c364d1a78)

#### 10. Make timing ECO fixes to remove all violations.

OR gate of drive strength 2 is driving 4 fanouts

![Screenshot from 2024-03-26 06-55-46](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/73c58332-a212-4a24-b853-e8cae3b385a6)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11672_

# Checking command syntax
help replace_cell

# Replacing cell
replace_cell _14510_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

Result - slack reduced

![Screenshot from 2024-03-26 07-02-44](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c50c6023-1836-468d-a8c3-955b02e4224c)
![Screenshot from 2024-03-26 07-04-08](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/6059a511-9977-4d9e-8dde-8939f0e1b8f7)
![Screenshot from 2024-03-26 09-42-15](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/df3dba3c-9445-4d51-9842-bf4410f05cf5)
![Screenshot from 2024-03-26 07-07-50](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/f141d1ff-4e21-4f9a-ab47-19579a686ac4)

OR gate of drive strength 2 is driving 4 fanouts

![Screenshot from 2024-03-26 09-46-23](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/3f66eed9-c43d-483e-ab85-21d70452b81f)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11675_

# Replacing cell
replace_cell _14514_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

Result - slack reduced

![Screenshot from 2024-03-26 09-49-29](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/d896c1fa-078e-4fe1-8e4d-60fae870d672)
![Screenshot from 2024-03-26 09-50-13](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a21abd89-b92a-4d28-b065-6b8b523026de)
![Screenshot from 2024-03-26 09-50-33](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/9b961126-3de7-450e-9789-5009ca7f6a16)

OR gate of drive strength 2 driving OA gate has more delay

![Screenshot from 2024-03-26 10-22-10](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/7669eeef-7b93-4cfd-a152-17eec772496a)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11643_

# Replacing cell
replace_cell _14481_ sky130_fd_sc_hd__or4_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

Result - slack reduced

![Screenshot from 2024-03-26 10-29-31](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c63cf6a1-582d-43e5-907f-e292b94cc086)
![Screenshot from 2024-03-26 10-29-55](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/6495c2e8-78c8-41d2-b783-628f36b982c5)

OR gate of drive strength 2 driving OA gate has more delay

![Screenshot from 2024-03-26 10-32-27](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/4185dc98-a065-49d3-a92f-fb4b02f23ee5)

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11668_

# Replacing cell
replace_cell _14506_ sky130_fd_sc_hd__or4_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

Result - slack reduced

![Screenshot from 2024-03-26 10-36-59](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/be71b3a4-efe1-4239-be5a-28ccebd2b7f4)
![Screenshot from 2024-03-26 10-37-14](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/2ae759b3-f9a7-478f-a34a-3f8706347eca)

Commands to verify instance `_14506_`  is replaced with `sky130_fd_sc_hd__or4_4`

```tcl
# Generating custom timing report
report_checks -from _29043_ -to _30440_ -through _14506_
```

Screenshot of replaced instance

![Screenshot from 2024-03-26 10-43-04](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/970b3cb7-fe10-4b5e-99c9-85059714f8f2)

*We started ECO fixes at wns -23.9000 and now we stand at wns -22.6173 we reduced around 1.2827 ns of violation*

#### 11. Replace the old netlist with the new netlist generated after timing ECO fix and implement the floorplan, placement and cts.

Now to insert this updated netlist to PnR flow and we can use `write_verilog` and overwrite the synthesis netlist but before that we are going to make a copy of the old old netlist

Commands to make copy of netlist

```bash
# Change from home directory to synthesis results directory
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/

# List contents of the directory
ls

# Copy and rename the netlist
cp picorv32a.synthesis.v picorv32a.synthesis_old.v

# List contents of the directory
ls
```

Screenshot of commands run

![Screenshot from 2024-03-26 10-54-15](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/90c90853-0664-44d7-8ff2-573621935870)

Commands to write verilog

```tcl
# Check syntax
help write_verilog

# Overwriting current synthesis netlist
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/picorv32a.synthesis.v

# Exit from OpenSTA since timing analysis is done
exit
```

Screenshot of commands run

![Screenshot from 2024-03-26 11-02-19](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c6c8ce7f-5219-41bc-a5a8-47b0e1c62c7c)

Verified that the netlist is overwritten by checking that instance `_14506_`  is replaced with `sky130_fd_sc_hd__or4_4`

![Screenshot from 2024-03-26 11-01-25](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1ddb66da-64cb-426d-8d78-30370c63dacb)

Since we confirmed that netlist is replaced and will be loaded in PnR but since we want to follow up on the earlier 0 violation design we are continuing with the clean design to further stages

Commands load the design and run necessary stages

```tcl
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 24-03_10-03 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts
```

Screenshots of commands run

![Screenshot from 2024-03-26 11-33-14](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a0f223bc-3cbb-4bb1-8c7e-d800f1a4efd7)
![Screenshot from 2024-03-26 11-33-22](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/9bd610a8-fd5e-452b-94b2-5a5fc76db5e9)
![Screenshot from 2024-03-26 11-35-40](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/283d9b5e-fc48-45a3-8548-c68fc8966f46)
![Screenshot from 2024-03-26 11-36-16](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/af866818-af6f-4bc4-96c0-cf3c99839003)
![Screenshot from 2024-03-26 11-37-36](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/55aa5130-8a9f-48d6-abc8-7aace68b58c8)
![Screenshot from 2024-03-26 11-38-26](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/755fc79a-2a77-402b-aa11-33799e31ee09)
![Screenshot from 2024-03-26 11-39-53](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/45fdc85e-cc32-4b08-954e-bd78dcec0891)
![Screenshot from 2024-03-26 12-00-48](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/5deb6b89-c81e-4b4d-a225-badc1f7c7299)

#### 12. Post-CTS OpenROAD timing analysis.

Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

```tcl
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/24-03_10-03/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Check syntax of 'report_checks' command
help report_checks

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```

Screenshots of commands run and timing report generated

![Screenshot from 2024-03-26 12-55-00](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/ee26dfa7-715a-4df7-97e7-4c6e54d16522)
![Screenshot from 2024-03-26 12-57-40](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/e04a4dfd-000c-406b-bdaa-f5314c4eedef)
![Screenshot from 2024-03-26 12-58-12](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/ac27d567-1b72-444d-a638-9be2db677ae2)
![Screenshot from 2024-03-26 13-09-57](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/e63cac70-072d-4453-992e-076b94f8f1a2)

#### 13. Explore post-CTS OpenROAD timing analysis by removing 'sky130_fd_sc_hd__clkbuf_1' cell from clock buffer list variable 'CTS_CLK_BUFFER_LIST'.

Commands to be run in OpenLANE flow to do OpenROAD timing analysis after changing `CTS_CLK_BUFFER_LIST`

```tcl
# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Removing 'sky130_fd_sc_hd__clkbuf_1' from the list
set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Checking current value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Setting def as placement def
set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/placement/picorv32a.placement.def

# Run CTS again
run_cts

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/24-03_10-03/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts1.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/24-03_10-03/results/synthesis/picorv32a.synthesis_cts.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Report hold skew
report_clock_skew -hold

# Report setup skew
report_clock_skew -setup

# Exit to OpenLANE flow
exit

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)

# Inserting 'sky130_fd_sc_hd__clkbuf_1' to first index of list
set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky130_fd_sc_hd__clkbuf_1]

# Checking current value of 'CTS_CLK_BUFFER_LIST'
echo $::env(CTS_CLK_BUFFER_LIST)
```

Screenshots of commands run and timing report generated

![Screenshot from 2024-03-26 13-42-03](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/2191f13b-ff76-4281-9d3c-52cbf12f9142)
![Screenshot from 2024-03-26 13-45-28](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/3dcd6efe-d31a-4f72-aede-be1cdd7b078f)
![Screenshot from 2024-03-26 13-48-01](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/2cb2c6fd-1e8c-4921-8553-7dcfb51258e5)
![Screenshot from 2024-03-26 13-48-13](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/70353613-86f2-432c-a1d8-821083e8c209)
![Screenshot from 2024-03-26 13-50-12](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/bf6c116b-e31c-4dce-b04f-a75430b1d03b)
![Screenshot from 2024-03-26 13-53-30](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/a26e9d23-d448-4512-8676-8a2b3fb22572)

## Section 5 - Final steps for RTL2GDS using tritonRoute and openSTA (29/03/2024 - 30/03/2024)

### Theory

### Implementation

* Section 5 tasks:-
1. Perform generation of Power Distribution Network (PDN) and explore the PDN layout.
2. Perfrom detailed routing using TritonRoute.
3. Post-Route parasitic extraction using SPEF extractor.
4. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.

* All section 5 logs, reports and results can be found in following run folder:

[Section 5 Run - 26-03_08-45](https://github.com/fayizferosh/advanced-pd-using-openlane-sky130/tree/main/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45)

#### 1. Perform generation of Power Distribution Network (PDN) and explore the PDN layout.

Commands to perform all necessary stages up until now

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Following commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts

# Now that CTS is done we can do power distribution network
gen_pdn 
```

Screenshots of power distribution network run

![Screenshot from 2024-03-26 14-22-34](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/dd916806-6688-4c96-b1af-156b2d4acfe6)
![Screenshot from 2024-03-26 14-22-46](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1f6ade75-93c2-4b76-bc46-77d1d532a84c)

Commands to load PDN def in magic in another terminal

```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

Screenshots of PDN def

![Screenshot from 2024-03-26 14-30-52](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b13997fd-296c-4213-b4f9-8f66a7375e47)
![Screenshot from 2024-03-26 14-32-24](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/79b5f158-acf4-4065-a0ec-61007ab465d0)
![Screenshot from 2024-03-26 14-34-03](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/bee921ce-03d5-49fb-a9fc-bcc6e3402f8c)

#### 2. Perfrom detailed routing using TritonRoute and explore the routed layout.

Command to perform routing

```tcl
# Check value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Check value of 'ROUTING_STRATEGY'
echo $::env(ROUTING_STRATEGY)

# Command for detailed route using TritonRoute
run_routing
```

Screenshots of routing run

![Screenshot from 2024-03-28 20-00-15](https://github.com/iamavi07/PHYSICAL-DESIGN-USING-OPENLANE-SKY130/assets/122794054/b68a16d6-0b57-4fd5-81bd-6a7745bb8377)

![Screenshot from 2024-03-26 15-38-39](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/c0c8f372-0293-4fdd-a0a3-691f164e7bed)
![Screenshot from 2024-03-26 15-29-38](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/70a99289-06ea-4eb8-b3b0-4147395c6f9c)

Commands to load routed def in magic in another terminal

```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def

![Screenshot from 2024-03-26 15-33-12](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/6eade230-eb96-4d7b-b7a9-7a7db9c2c8b7)
![Screenshot from 2024-03-26 15-30-36](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/b1900c55-7470-41b2-8b4f-3af871494d99)
![Screenshot from 2024-03-26 15-31-29](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/5faaca5b-fb6e-4abd-946d-6531b35489b8)
![Screenshot from 2024-03-26 15-32-20](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/594ef79b-4755-4934-a087-33fb24996526)

Screenshot of fast route guide present in `openlane/designs/picorv32a/runs/26-03_08-45/tmp/routing` directory

![Screenshot from 2024-03-26 15-41-18](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/1dc38a57-03c9-45c3-acdb-063731a86433)

#### 3. Post-Route parasitic extraction using SPEF extractor.

Commands for SPEF extraction using external tool

```bash
# Change directory
cd Desktop/work/tools/SPEF_EXTRACTOR

# Command extract spef
python3 main.py /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def
```

#### 4. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.

Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

```tcl
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def

# Creating an OpenROAD database to work with
write_db pico_route.db

# Loading the created database in OpenROAD
read_db pico_route.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/synthesis/picorv32a.synthesis_preroute.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Read SPEF
read_spef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.spef

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```

Screenshots of commands run and timing report generated

![Screenshot from 2024-03-26 23-16-16](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/72ef3e8d-7ca2-4b60-89ea-de053f9c2902)
![Screenshot from 2024-03-26 23-17-09](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/5cac9ce5-420a-4eaa-b5f4-09286701e550)
![Screenshot from 2024-03-26 23-17-32](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/7d809f14-66b6-4dd6-8161-2ad8371cfaf9)
![Screenshot from 2024-03-26 23-17-56](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd/assets/63997454/64ccb1d8-74aa-42b0-88d4-a0f9588d2ca2)
Section 3 - Task 6 (drc_tests)
