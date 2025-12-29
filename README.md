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

<img width="955" height="910" alt="image" src="https://github.com/user-attachments/assets/2f109c84-0078-4b71-bfa9-fc913624ff99" />

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



## Outcome
This project demonstrates hands-on experience with:
- End-to-end **RTL to GDSII physical design**
- **Custom standard cell design and integration**
- Open-source ASIC toolchains used in real-world VLSI workflows

---


