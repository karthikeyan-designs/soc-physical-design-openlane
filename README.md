# Digital VLSI SoC Design and Planning  
### Hands-on RTL to GDSII Implementation (VSD Workshop)


## Introduction
This repository documents the complete **RTL to GDSII physical design flow** implemented using **open-source EDA tools**. The design flow is demonstrated on the **picorv32a RISC-V core** using **OpenLANE**, an automated RTL-to-GDSII framework built around industry-grade open-source tools.

The work covers the full physical design lifecycle, including **custom standard cell design, characterization, and integration** into the SoC flow.

---

## Overview
The physical design flow transforms a Register Transfer Level (RTL) description into a manufacturable layout (GDSII) through the following stages:

- **Synthesis**: RTL to gate-level netlist generation  
- **Floorplanning**: Chip dimensioning, IO placement, and macro planning  
- **Placement**: Optimal standard cell placement  
- **Clock Tree Synthesis (CTS)**: Balanced clock distribution  
- **Routing**: Global and detailed routing  
- **Sign-off**: DRC, LVS, and Static Timing Analysis (STA)

---

## Tools and Technologies Used
- **OpenLANE** – Automated RTL to GDSII flow  
- **Sky130 PDK** – Google–SkyWater 130nm open-source PDK  
- **Magic** – Layout viewing and editing  
- **ngspice** – SPICE-level circuit simulation  
- **OpenSTA** – Static Timing Analysis  
- **TritonRoute** – Detailed routing engine  

---

## Design Details
- **Design Name**: picorv32a  
- **Core**: RISC-V based processor  
- **Technology Node**: 130nm (Sky130 PDK)

---

## Key Highlights
- **Custom Standard Cell Integration**  
  Integrated an existing custom inverter cell into the picorv32a design flow, including library inclusion, characterization data usage, and verification through the complete RTL-to-GDSII PnR flow.

- **Cell Characterization**  
  Performed SPICE extraction and timing characterization using ngspice.

- **Custom Cell Integration**  
  Successfully integrated the custom inverter cell into the picorv32a OpenLANE flow.

- **Complete PnR Flow Execution**  
  Executed full Place and Route (PnR) with the custom cell included in the standard cell library.

- **Timing Analysis**  
  Performed pre-layout and post-layout timing analysis using OpenSTA.

- **DRC Debugging**  
  Identified and fixed Design Rule Check (DRC) issues using Sky130 technology rules.

---

## Repository Structure
The repository is organized into **5 logical stages**, each corresponding to a phase of the physical design flow:

- **Day 1** – Introduction to OpenLANE and synthesis  
- **Day 2** – Floorplanning and placement  
- **Day 3** – Custom standard cell design and characterization  
- **Day 4** – Pre-layout timing analysis and Clock Tree Synthesis (with custom inverter integration)  
- **Day 5** – Routing and final sign-off (DRC, LVS, STA)

Each section contains step-by-step commands, configuration files, and outputs required to reproduce the RTL-to-GDSII flow.

---

## Day 1 – Inception of Open-Source EDA, OpenLANE, and Sky130 PDK

### Implementation Tasks
- Run synthesis for the **picorv32a** design using the OpenLANE flow.
- Generate synthesis reports and netlists.
- Calculate the **flop ratio** from synthesis results.
- Understand the overall OpenLANE design flow and Sky130 PDK structure.

Day  1 Labs :- 
1. Run 'picorv32a' design synthesis using OpenLANE flow and generate necessary outputs.
2. Calculate the flop ratio.

```math
Flop\ Ratio = \frac{Number\ of\ D\ Flip\ Flops}{Total\ Number\ of\ Cells}
```
```math
Percentage\ of\ DFF's = Flop\ Ratio * 100
```

#### 1. Run 'picorv32a' design synthesis using OpenLANE flow and generate necessary outputs.

Commands to invoke the OpenLANE flow and perform synthesis

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

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Exit from OpenLANE flow
exit

# Exit from OpenLANE flow docker sub-system
exit
```
## Synthesis Results

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/24fe25c7-f03d-49d2-8a54-139929e3afad" />


<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/73b31fd9-9544-4d53-9056-8db17b22972f" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/d7e2d429-f60e-4e9e-b39a-d1cffb04fc5c" />

#### 2. Calculate the flop ratio.

Calculate the flop ratio.

```math
Flop\ Ratio = \frac{Number\ of\ D\ Flip\ Flops}{Total\ Number\ of\ Cells}
```
```math
Percentage\ of\ DFF's = Flop\ Ratio * 100
```

Calculation of Flop Ratio and DFF % from synthesis statistics report file
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/2c01fbdf-1244-4cf5-9e8e-69507c2b6b51" />


```math
Flop\ Ratio = \frac{1613}{14876} = 0.108429685
```
```math
Percentage\ of\ DFF's = 0.108429685 * 100 = 10.84296854\ \%
```
---
## Day 2 - Good floorplan vs bad floorplan and introduction to library cells 

 IMPLEMENTATION 
  </summary>

1. Run 'picorv32a' design floorplan using OpenLANE flow and generate necessary outputs.
2. Calculate the die area in microns from the values in floorplan def.
3. Load generated floorplan def in magic tool and explore the floorplan.
4. Run 'picorv32a' design congestion aware placement using OpenLANE flow and generate necessary outputs.
5. Load generated placement def in magic tool and explore the placement.

```math
Area\ of\ die\ in\ microns = Die\ width\ in\ microns * Die\ height\ in\ microns
```

#### 1. Run 'picorv32a' design floorplan using OpenLANE flow and generate necessary outputs.

Commands to invoke the OpenLANE flow and perform floorplan

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

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Now we can run floorplan
run_floorplan
```

Screenshot of floorplan run

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/86db112c-f059-4f52-a4a4-7a69bdc79e2f" />

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/be78cee3-c3e2-4338-997b-088055d083f4" />



<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/9f3c1a6e-a843-4b63-899e-0f156b88e994" />

#### 2. Calculate the die area in microns from the values in floorplan def.

Screenshot of contents of floorplan def
file:///home/vsduser/Pictures/die_area.png<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/c64cc81d-3797-456d-9249-bb6c3fd53071" />

According to floorplan def
```math
1000\ Unit\ Distance = 1\ Micron
```
```math
Die\ width\ in\ unit\ distance = 660685 - 0 = 660685
```
```math
Die\ height\ in\ unit\ distance = 671405 - 0 = 671405
```
```math
Distance\ in\ microns = \frac{Value\ in\ Unit\ Distance}{1000}
```
```math
Die\ width\ in\ microns = \frac{660685}{1000} = 660.685\ Microns
```
```math
Die\ height\ in\ microns = \frac{671405}{1000} = 671.405\ Microns
```
```math
Area\ of\ die\ in\ microns = 660.685 * 671.405 = 443587.212425\ Square\ Microns
```
#### 3. Load generated floorplan def in magic tool and explore the floorplan.

Commands to load floorplan def in magic in another terminal

```bash
# Change directory to path containing generated floorplan def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/21-12_12-13/results/floorplan/

# Command to load the floorplan def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

Screenshots of floorplan def in magic

<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/7a106d22-2e10-43d8-95f9-f6b2ea72db04" />

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/0c8f36ae-0c0f-41b4-9c22-34ca2843f3e2" />

#### 4. Run 'picorv32a' design congestion aware placement using OpenLANE flow and generate necessary outputs.

Command to run placement

```tcl
# Congestion aware placement by default
run_placement
```

Screenshots of placement run

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/2c56204a-f7a9-44e5-8a10-de112833986b" />

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/6d8b13cc-37d8-498d-9eb7-0b27a3beb431" />
#### 5. Load generated placement def in magic tool and explore the placement.

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/21-12_12-13/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshots of Placement in magic

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/85e646cf-b1dd-4c49-8c65-fb1d2d6f6eec" />
Standard Cells Legally Placed
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/08a587e9-be54-4463-ada9-1c88ddb8642b" />

## Day3 - Design library cell using Magic Layout and ngspice characterization 
 IMPLEMENTATION
  </summary>

1. Clone custom inverter standard cell design from github repository: [Standard cell design and characterization using OpenLANE flow](https://github.com/nickson-jose/vsdstdcelldesign).
2. Load the custom inverter layout in magic and explore.
3. Spice extraction of inverter in magic.
4. Editing the spice model file for analysis through simulation.
5. Post-layout ngspice simulations.
6. Find problem in the DRC section of the old magic tech file for the skywater process and fix them.


#### 1. Clone custom inverter standard cell design from github repository

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the repository with custom inverter design
git clone https://github.com/nickson-jose/vsdstdcelldesign

# Change into repository directory
cd vsdstdcelldesign

#View custom inverter cell in Magic
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def sky130_inv.mag &


# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```
#### 2. Load the custom inverter layout in magic and explore.

Screenshot of custom inverter layout in magic
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/9d93afb7-0c8a-42bc-b977-1b42710a178c" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/ebb3c538-9917-4dee-89a2-5dc1cb4299cc" />

#### 3. Spice extraction of inverter in magic.

Commands for spice extraction of the custom inverter layout to be used in tkcon window of magic

```tcl
# Check current directory
pwd

# Extraction command to extract to .ext format
extract all

# Before converting ext to spice this command enable the parasitic extraction also
ext2spice cthresh 0 rthresh 0

# Converting to ext to spice
ext2spice
```
Screenshot of tkcon window after running above commands
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/c48cb7ee-9567-408c-a6d8-33c14bfb88e5" />

Screenshot of created spice file
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/4b05cdb9-1cd3-4ec1-8cf3-f9d4c184f248" />


### 4. Editing the spice model file for analysis through simulation.
Final edited spice file ready for ngspice simulation
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/d23f8674-abf4-41a7-b0f2-96f35282a810" />

#### 5. Post-layout ngspice simulations.

Commands for ngspice simulation

```bash
# Command to directly load spice file for simulation to ngspice
ngspice sky130_inv.spice

# Now that we have entered ngspice with the simulation spice file loaded we just have to load the plot
plot y vs time a
```

Screenshots of ngspice run
u<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/e61cbf1d-ab3f-4ac4-8a1d-bddf702c05ac" />
Ngspice Transient Simulation of Inverter
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/cc5c06f1-3415-4676-af3a-da5183f9291a" />

Rise transition time calculation

```math
Rise\ transition\ time = Time\ taken\ for\ output\ to\ rise\ to\ 80\% - Time\ taken\ for\ output\ to\ rise\ to\ 20\%
```
```math
20\%\ of\ output = 660\ mV
```
```math
80\%\ of\ output = 2.64\ V
```
20% Screenshots
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/a8b8858a-e194-4e66-abef-14df96174f2b" />

80% Screenshots
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/d332bfd7-c649-4cc6-acc4-da519b8f335e" />
20% Screenshots
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/ea6c7101-ac77-4aa2-b22f-a1b9b74a4b2a" />
80% Screenshots
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/e2b11a2e-4a52-408f-930d-704eac347690" />

Fall transition time calculation

```math
Fall\ transition\ time = Time\ taken\ for\ output\ to\ fall\ to\ 20\% - Time\ taken\ for\ output\ to\ fall\ to\ 80\%
```
```math
20\%\ of\ output = 660\ mV
```
```math
80\%\ of\ output = 2.64\ V
```
Rise Transition Time
```math
Rise transition time=2.24589e−09−2.18212e−09=63.77 ps
```
Fall Transition Time
```math
Fall transition time=4.09517e−09−4.05276e−09=42.41 ps
```


### Rise Cell Delay Calculation

```math
Rise\ Cell\ Delay = Time\ taken\ for\ output\ to\ rise\ to\ 50\% - Time\ taken\ for\ input\ to\ fall\ to\ 50\%
```
```math
50\%\ of\ 3.3\ V = 1.65\ V
```
```math
Rise cell delay= 2.21145e-09-2.1494e-09= 62.05 ps
```
### Fall Cell Delay Calculation

```math
Fall\ Cell\ Delay = Time\ taken\ for\ output\ to\ fall\ to\ 50\% - Time\ taken\ for\ input\ to\ rise\ to\ 50\%
```
```math
50\%\ of\ 3.3\ V = 1.65\ V
```
```math
fall cell delay = 4.07768e-09 - 4.04996e-09 =27.72ps
```
#### 6. Find problem in the DRC section of the old magic tech file for the skywater process and fix them.

Link to Sky130 Periphery rules: [https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html)

Commands to download and view the corrupted skywater process magic tech file and associated files to perform drc corrections

```bash
# Change to home directory
cd

# Command to download the lab files
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

# Since lab file is compressed command to extract it
tar xfz drc_tests.tgz

# Change directory into the lab folder
cd drc_tests

# List all files and directories present in the current directory
ls -al

# Command to view .magicrc file
gvim .magicrc

# Command to open magic tool in better graphics
magic -d XR &
```

**Incorrectly implemented poly.9 simple rule correction**

Screenshot of poly rules

<img width="854" height="453" alt="image" src="https://github.com/user-attachments/assets/c4cb336d-c029-42b5-adc7-35a2f290ce12" />

Incorrectly implemented poly.9 rule no drc violation even though spacing < 0.48
<img width="1920" height="923" alt="VirtualBox_Physical_Design_31_10_2025_20_54_37" src="https://github.com/user-attachments/assets/4e226b6d-a716-40d6-a325-eb0536a25a79" />
New commands inserted in sky130A.tech file to update drc

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/96efcc2e-1384-456a-a779-3dd8336fa119" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/9fdefb35-2964-4c1c-8373-2bc2055be712" />


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
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/faffb3ee-5903-4d27-b6f2-3bde9e3fc197" />

*Incorrectly implemented nwell.4 complex rule correction**

Screenshot of nwell rules

<img width="990" height="525" alt="image" src="https://github.com/user-attachments/assets/5e07ac20-a2fd-4d47-bf8b-ee38197ab64b" />

Incorrectly implemented nwell.4 rule no drc violation even though no tap present in nwell

<img width="995" height="538" alt="image" src="https://github.com/user-attachments/assets/99859af6-918c-4aaf-9199-7c8b56a8f24f" />

New commands inserted in sky130A.tech file to update drc
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/17a1cee0-0044-48dc-a2ff-74eeaaf4e137" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/b19c2d09-1b08-4422-a79e-aa59a69233c3" />
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
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/210a5ef6-7ad1-4891-ab10-da836c61a20d" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/1bb56fc5-96c3-4d09-97cd-58b095625038" />

## Day 4 - Pre-layout timing analysis and importance of good clock tree 
 <summary>
 IMPLEMENTATION
  </summary>

* Day 4 tasks:-
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

















## Outcome
This project demonstrates hands-on experience with:
- End-to-end **RTL to GDSII physical design**
- **Custom standard cell design and integration**
- Open-source ASIC toolchains used in real-world VLSI workflows

---


