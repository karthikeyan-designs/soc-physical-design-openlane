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
<img width="1280" height="768" alt="image" src="https://github.com/user-attachments/assets/c64cc81d-3797-456d-9249-bb6c3fd53071" />

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
### 5. Load generated placement def in magic tool and explore the placement.

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
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/e61cbf1d-ab3f-4ac4-8a1d-bddf702c05ac" />
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

Screenshot of tracks.info of sky130_fd_sc_hd

<img width="1920" height="1080" alt="Screenshot (342)" src="https://github.com/user-attachments/assets/b3d0a89f-2b4c-4325-8d9e-f6cd3d69e033" />

ommands for tkcon window to set grid as tracks of locali layer

```tcl
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```

Screenshot of commands run

<img width="1920" height="1080" alt="Screenshot (343)" src="https://github.com/user-attachments/assets/434f0700-4bde-4615-9910-ffc2467673fc" />
<img width="1920" height="1080" alt="Screenshot (344)" src="https://github.com/user-attachments/assets/332a78fd-dc56-4fdc-be98-9db73410cdec" />

Condition 1 verified

<img width="1920" height="1080" alt="Screenshot (345)" src="https://github.com/user-attachments/assets/7e558a2a-0044-4525-af6f-aaa2ca4ea4e7" />

Condition 2 verified

```math
Horizontal\ track\ pitch = 0.46\ um
```
<img width="772" height="417" alt="image" src="https://github.com/user-attachments/assets/89b1c680-d1b0-4ddf-b52b-9b52328b6f74" />

```math
Width\ of\ standard\ cell = 1.38\ um = 0.46 * 3
```

Condition 3 verified

```math
Vertical\ track\ pitch = 0.34\ um
```

<img width="778" height="418" alt="image" src="https://github.com/user-attachments/assets/8450193d-ea3e-4487-b295-4e9d6952e913" />

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
```bash
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_vsdinv.mag &
```

Screenshot of newly saved layout

<img width="1920" height="1080" alt="Screenshot (347)" src="https://github.com/user-attachments/assets/86043802-f695-42ce-9fe4-5671705a63e5" />
<img width="1920" height="1080" alt="Screenshot (348)" src="https://github.com/user-attachments/assets/86e5238d-5b5c-4b7a-87f0-947b166abdf2" />


#### 3. Generate lef from the layout.

Command for tkcon window to write lef

```tcl
# lef command
lef write
```

Screenshot of command run

<img width="773" height="412" alt="image" src="https://github.com/user-attachments/assets/ecb5edf4-8240-4ecb-b46d-d3ff4062ef8b" />

Screenshot of newly created lef file
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/fafb98e7-8145-4c65-b692-425190ed7230" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/336375a7-4b0d-4f0a-a39d-f600aa6dd292" />



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

<img width="769" height="427" alt="image" src="https://github.com/user-attachments/assets/ae7b4ac9-c7a1-46df-b085-9adeb8802e1f" />

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
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/1ae89e46-4992-44a7-9a35-290fcb058e53" />

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/c641cbd6-5657-4a6a-a985-df38120b725b" />

#### 7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.

Noting down current design values generated before modifying parameters to improve timing\
<img width="998" height="540" alt="image" src="https://github.com/user-attachments/assets/05be3c49-ba03-4ea2-bdce-132a8d2af705" />

<img width="991" height="535" alt="image" src="https://github.com/user-attachments/assets/92f02878-1d0c-4ca7-9da4-9a6390fc83d3" />

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
Comparing to previously noted run values area has increased and worst negative slack has become 0
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/c9c57744-faa1-416c-900e-bc13755cff0c" />

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/870ff08f-d868-419c-bb1d-1286927a041a" />

#### 8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.

Now that our custom inverter is properly accepted in synthesis we can now run floorplan using following command

```tcl
# Now we can run floorplan
run_floorplan
```

Screenshots of command run

<img width="997" height="543" alt="image" src="https://github.com/user-attachments/assets/1fe492dc-d53a-43e0-be55-1daba4d3849c" />
<img width="994" height="527" alt="image" src="https://github.com/user-attachments/assets/72940558-5f6a-4808-8445-fa9ff13847be" />


Since we are facing unexpected un-explainable error while using `run_floorplan` command, we can instead use the following set of commands available based on information from `Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl` and also based on `Floorplan Commands` section in `Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md`

```tcl
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```
Now that floorplan is done we can do placement using following command

```tcl
# Now we are ready to run placement
run_placement
```

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshot of placement def in magic
<img width="999" height="550" alt="image" src="https://github.com/user-attachments/assets/f3013fc5-86df-402b-98b0-3499b8aa0c8e" />
Screenshot of custom inverter inserted in placement def with proper abutment
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/98c671c9-1851-45e2-ada7-b871b885bbb4" />
Command for tkcon window to view internal layers of cells

```tcl
# Command to view internal connectivity layers
expand
```

Abutment of power pins with other cell from library clearly visible
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/431a2686-82f9-4107-911e-c4256d8557c4" />
### 9. Do Post-Synthesis timing analysis with OpenSTA tool.

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
<img width="999" height="543" alt="image" src="https://github.com/user-attachments/assets/5fcf8bc5-a42d-4b5a-8c76-a87a99ac1e85" />

Newly created pre_sta.conf for STA analysis in openlane directory
<img width="999" height="547" alt="image" src="https://github.com/user-attachments/assets/7e57c0f5-2828-4377-a95f-ae9fe0375f1c" />

Newly created my_base.sdc for STA analysis in openlane/designs/picorv32a/src directory based on the file openlane/scripts/base.sdc
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/e27078e6-c0d6-43da-86ba-b48a97c7364a" />

Commands to run STA in another terminal

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```
Screenshots of commands run
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/1a69d727-5126-4ae0-815e-1c9a9ec36849" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/5881dcad-fc83-41f6-8522-3e8da98c56e7" />
<img width="993" height="526" alt="image" src="https://github.com/user-attachments/assets/0594a92f-3d10-4366-939d-0896c9b7184e" />
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

<img width="1000" height="541" alt="image" src="https://github.com/user-attachments/assets/a78a2988-c106-4f5d-ad45-50cda338512a" />

Commands to run STA in another terminal

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Command to invoke OpenSTA tool with script
sta pre_sta.conf
```

Screenshots of commands run

<img width="993" height="552" alt="image" src="https://github.com/user-attachments/assets/7d08aebe-0e0c-4b95-82f8-4ebfff863c18" />
<img width="985" height="530" alt="image" src="https://github.com/user-attachments/assets/720291bb-bdae-4276-959c-5cb0eadbfd09" />
<img width="973" height="541" alt="image" src="https://github.com/user-attachments/assets/673e1f34-52b4-4eea-b0a4-3512c9cb5c6d" />
<img width="982" height="534" alt="image" src="https://github.com/user-attachments/assets/46750cfd-5b7d-498b-8e49-b66bd6866988" />


#### 10. Make timing ECO fixes to remove all violations.

OR gate of drive strength 2 is driving 4 fanouts
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/76eaf727-0641-4474-9489-b33224ee1cb0" />

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
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/36c29673-31e9-460e-938f-f38ca464b2f7" />

OR gate of drive strength 2 is driving 4 fanouts

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11675_

# Replacing cell
replace_cell _14514_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```

Result - slack reducedfile:
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/6482f849-aa18-4d91-acb8-04c08d84ae81" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/39126fb7-496e-4da1-a8ff-aaf23d4d4fb4" />

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
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/7eed6bc3-9f79-46c6-808e-e4903de419d8" />

Commands to verify instance `_14506_`  is replaced with `sky130_fd_sc_hd__or4_4`

```tcl
# Generating custom timing report
report_checks -from _29043_ -to _30440_ -through _14506_
```

## ECO Timing Optimization – Cell Upsizing

### Final Reduced Slack Achieved
![Final Reduced Slack](https://github.com/user-attachments/assets/4051e717-7e34-4bcd-941e-a066888e912e)

### Timing Improvement Summary
- **Initial Worst Negative Slack (WNS)**: −23.9000 ns  
- **Final Worst Negative Slack (WNS)**: −21.9653 ns  
- **Total Slack Improvement**: **~1.93 ns**

### ECO Methodology
- Performed **Engineering Change Order (ECO)** fixes targeting critical timing paths.
- Applied **standard cell upsizing** on selected instances along the critical path.

#### 11. Replace the old netlist with the new netlist generated after timing ECO fix and implement the floorplan, placement and cts.

Now to insert this updated netlist to PnR flow and we can use `write_verilog` and overwrite the synthesis netlist but before that we are going to make a copy of the old old netlist

Commands to make copy of netlist

```bash
# Change from home directory to synthesis results directory
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-12_06-45/results/synthesis/

# List contents of the directory
ls

# Copy and rename the netlist
cp picorv32a.synthesis.v picorv32a.synthesis_old.v

# List contents of the directory
ls
```
Commands to write verilog

```tcl
# Check syntax
help write_verilog

# Overwriting current synthesis netlist
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/picorv32a.synthesis.v

# Exit from OpenSTA since timing analysis is done
exit
```
Verified that the netlist is overwritten by checking that instance `_14506_`  is replaced with `sky130_fd_sc_hd__or4_4`

<img width="990" height="548" alt="image" src="https://github.com/user-attachments/assets/0a9073d9-8e4c-4abf-b100-7f70754b2c2a" />


Since we confirmed that netlist is replaced and will be loaded in PnR but since we want to follow up on the earlier 0 violation design we are continuing with the clean design to further stages

Commands load the design and run necessary stages

```tcl
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 28-12_05-52 -overwrite

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

Screenshots of commands run CTS
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/18af8f15-a08c-4382-a47e-086e471f05ee" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/25986f5b-ca7c-4527-82ad-b25962592fe6" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/c1252f68-59ea-4910-8836-f43aab47730a" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/c006ad17-7034-44da-97fe-875d7f539e02" />

#### 12. Post-CTS OpenROAD timing analysis.

Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

```tcl
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/28-12_05-52/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/28-12_05-52/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/28-12_05-52/results/synthesis/picorv32a.synthesis_cts.v

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
Screenshots of commands run and timing report generatedfile:
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/09bee8e8-799e-4f64-b3e5-d541c09d0e73" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/0a37e040-0f56-467f-9b31-2c5e742f5975" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/d2edf097-29ed-4996-be06-495c52b080ea" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/ed8aa3ba-e84d-433d-a90b-47e69c498b26" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/85a2349e-25f8-4142-89bb-93fdcde950f0" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/a76d3b36-3532-442e-b285-9b68684ccdd1" />

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
read_lef /openLANE_flow/designs/picorv32a/runs/28-12_05-52/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/28-12_05-52/results/cts/picorv32a.cts.def

# Creating an OpenROAD database to work with
write_db pico_cts1.db

# Loading the created database in OpenROAD
read_db pico_cts.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/28-12_05-52/results/synthesis/picorv32a.synthesis_cts.v

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

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/94bb93f8-2305-4e66-98cf-1ec076c0d93c" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/a9908e60-5e77-4c67-adf5-6b5e14d8b35a" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/1e412a23-f18e-48cb-8714-4e101745256d" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/07aba6ff-a49f-40d2-8e6e-a8ad6c371910" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/ed6c8d82-ff07-4ccb-9464-a7d99bc5fe10" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/e4a99652-bbe4-4955-94dd-809f36d49644" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/8e37e3c8-bb4e-4218-9b5b-9340e4f29153" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/acdb3a0f-0388-4477-ab94-80e6549289f6" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/252a060a-26e0-4274-bc9c-bd9768351d96" />
## Day 5 - Final steps for RTL2GDS using tritonRoute and openSTA

 Day 5 tasks:
1. Perform generation of Power Distribution Network (PDN) and explore the PDN layout.
2. Perfrom detailed routing using TritonRoute.
3. Post-Route parasitic extraction using SPEF extractor.
4. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.

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

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/97f632e8-d635-464d-a7dc-1d4ea2933ebf" />

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/5bcce35e-d775-44a0-869d-65a60f9e4f53" />


Commands to load PDN def in magic in another terminal

```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-12_05-52/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 17-pdn.def &
```
Screenshots of PDN def
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/ec2d0129-493a-4468-9d42-8169e86348c2" />
<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/ed03e574-9632-4a3d-8d03-ba82a0107889" />

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

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/baa084c6-b030-48a8-84eb-123535087268" />

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/37e05df0-bcbe-4673-8db0-2330f2a027d1" />

<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/b5001aae-d4f8-476e-a1a6-7d23fc235606" />

Commands to load routed def in magic in another terminal

```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-12_05-52/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```
Screenshots of routed def
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/124ddf7b-2026-4d32-9217-f50592a3a217" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/c31244ec-c837-4ccc-81d6-be402f397e40" />
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/e106edee-32e7-4327-ae49-51d7c280d14d" />

### 3.Parasitic Extraction

In the newer versions of **OpenLane**, a separate SPEF extraction step is not required.  
Upon successful completion of the **routing stage**, the **SPEF (Standard Parasitic Exchange Format)** file is **automatically generated** as part of the flow.

The presence of the generated SPEF file can be verified in the corresponding routing output directory before proceeding to the next analysis step.

Commands to Locate and Verify the Generated `.spef` File
<img width="1920" height="923" alt="image" src="https://github.com/user-attachments/assets/9c9b147f-1b67-4d72-9e5a-888fd3979af7" />

4. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.
Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

# Command to run OpenROAD tool
openroad
```
# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/28-12_05-52/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/28-12_05-52/results/routing/picorv32a.def

# Creating an OpenROAD database to work with
write_db pico_route.db

# Loading the created database in OpenROAD
read_db pico_route.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/ 28-12_05-52/results/synthesis/picorv32a.synthesis_preroute.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Read SPEF
read_spef /openLANE_flow/designs/picorv32a/runs/ 28-12_05-52/results/routing/picorv32a.spef

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```
Screenshots of commands run and timing report generated

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/74acd59d-b709-47f7-8573-f7505d49b638" />

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/852f8174-b2be-43ad-9cc1-eee1237d52ad" />

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/7cb35175-9997-4acd-8445-89738725b81c" />

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/c4582631-1d7e-409d-8b85-6ebd9f5e7641" />

---


## Outcome
This project demonstrates hands-on experience with:
- End-to-end **RTL to GDSII physical design**
- **Custom standard cell design and integration**
- Open-source ASIC toolchains used in real-world VLSI workflows

# Acknowledgements

* [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
* [Nickson P Jose](https://github.com/nickson-jose), Physical Design Engineer, Intel Corporation.
* [R. Timothy Edwards](https://github.com/RTimothyEdwards), Senior Vice President of Analog and Design, efabless Corporation.



---


