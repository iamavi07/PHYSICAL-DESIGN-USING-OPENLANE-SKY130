# PHYSICAL-DESIGN-USING-OPENLANE-SKY130
AUTHOR: AVINASH TANTI

This repository documents my progress through the 2 Week Advanced Physical Design using OpenLANE/Sky130 workshop with complete RTL2GDSII flow organized by VSD as part of Level-3 of Chip Design for High School Program in collaboration with Intel India

Section 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK (18/03/2024 - 19/03/2024)
Theory
Expand or Collapse
Implementation
Section 1 tasks:-

Run 'picorv32a' design synthesis using OpenLANE flow and generate necessary outputs.
Calculate the flop ratio.
 
All section 1 logs, reports and results can be found in following run folder:
Section 1 Run - 15-03_15-51

1. Run 'picorv32a' design synthesis using OpenLANE flow and generate necessary outputs.
Commands to invoke the OpenLANE flow and perform synthesis

# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Exit from OpenLANE flow
exit

# Exit from OpenLANE flow docker sub-system
exit
Screenshots of running each commands

1 2 3

2. Calculate the flop ratio.
Screenshots of synthesis statistics report file with required values highlighted

Screenshot from 2024-03-15 22-02-42 Screenshot from 2024-03-15 22-03-39

Calculation of Flop Ratio and DFF % from synthesis statistics report file

 
Section 2 - Good floorplan vs bad floorplan and introduction to library cells (20/03/2024 - 21/03/2024)
Theory
Implementation
Section 2 tasks:-

Run 'picorv32a' design floorplan using OpenLANE flow and generate necessary outputs.
Calculate the die area in microns from the values in floorplan def.
Load generated floorplan def in magic tool and explore the floorplan.
Run 'picorv32a' design congestion aware placement using OpenLANE flow and generate necessary outputs.
Load generated placement def in magic tool and explore the placement.
All section 2 logs, reports and results can be found in following run folder:
Section 2 Run - 17-03_12-06

1. Run 'picorv32a' design floorplan using OpenLANE flow and generate necessary outputs.
Commands to invoke the OpenLANE flow and perform floorplan

# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Now we can run floorplan
run_floorplan
Screenshot of floorplan run

Screenshot from 2024-03-17 18-06-19 Screenshot from 2024-03-17 18-06-36

2. Calculate the die area in microns from the values in floorplan def.
Screenshot of contents of floorplan def

Screenshot from 2024-03-17 18-34-53

According to floorplan def

 
 
 
3. Load generated floorplan def in magic tool and explore the floorplan.
Commands to load floorplan def in magic in another terminal

# Change directory to path containing generated floorplan def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/floorplan/

# Command to load the floorplan def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
Screenshots of floorplan def in magic

Screenshot from 2024-03-17 18-05-19

Equidistant placement of ports

Screenshot from 2024-03-17 18-14-28

Port layer as set through config.tcl

Screenshot from 2024-03-17 18-17-46 Screenshot from 2024-03-17 18-19-50

Decap Cells and Tap Cells

Screenshot from 2024-03-17 18-22-57

Diogonally equidistant Tap cells

Screenshot from 2024-03-17 18-25-28

Unplaced standard cells at the origin

Screenshot from 2024-03-17 18-31-41

4. Run 'picorv32a' design congestion aware placement using OpenLANE flow and generate necessary outputs.
Command to run placement

# Congestion aware placement by default
run_placement
Screenshots of placement run

Screenshot from 2024-03-17 22-44-17 Screenshot from 2024-03-17 22-46-27

5. Load generated placement def in magic tool and explore the placement.
Commands to load placement def in magic in another terminal

# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
Screenshots of floorplan def in magic

Screenshot from 2024-03-17 22-58-44

Standard cells legally placed

Screenshot from 2024-03-17 23-04-20

Commands to exit from current run

# Exit from OpenLANE flow
exit

# Exit from OpenLANE flow docker sub-system
exit
Section 3 - Design library cell using Magic Layout and ngspice characterization (22/03/2024 - 25/03/2024)
Theory
Implementation
Section 3 tasks:-
Clone custom inverter standard cell design from github repository: Standard cell design and characterization using OpenLANE flow.
Load the custom inverter layout in magic and explore.
Spice extraction of inverter in magic.
Editing the spice model file for analysis through simulation.
Post-layout ngspice simulations.
Find problem in the DRC section of the old magic tech file for the skywater process and fix them.
Section 3 - Tasks 1 to 5 files, reports and logs can be found in the following folder:
Section 3 - Tasks 1 to 5 (vsdstdcelldesign)

Section 3 - Task 6 files, reports and logs can be found in the following folder:
Section 3 - Task 6 (drc_tests)
