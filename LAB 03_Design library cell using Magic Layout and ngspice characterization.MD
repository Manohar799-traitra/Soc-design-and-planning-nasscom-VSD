# Lab 3 – Design Library Cell using Magic Layout and ngspice Characterization
 
**Technology:** Sky130  
**Tools Used:** Magic VLSI, ngspice, OpenLANE  
**Design:** Custom CMOS Inverter Standard Cell  

**Task 1–5 Directory:** Section 3 – Tasks 1 to 5 (vsdstdcelldesign)  
**Task 6 Directory:** Section 3 – Task 6 (drc_tests)  

---

## Theory

### Standard Cell Design in VLSI

Standard cells form the fundamental building blocks of digital integrated circuits.
Each standard cell consists of a well-defined layout, electrical behavior, and
characterization parameters such as delay, transition time, and power.

A custom-designed standard cell must satisfy:
- Design Rule Check (DRC) constraints
- Electrical correctness (connectivity and device sizing)
- Compatibility with place-and-route tools
- Accurate timing and power characterization

In this lab, a CMOS inverter is designed, verified, and characterized post-layout
using Magic and ngspice, following Sky130 design rules.

---

### Post-Layout Characterization

Post-layout characterization includes parasitic extraction from the layout and
simulation using a circuit simulator. This step captures real-world effects such as:
- Parasitic capacitance and resistance
- Accurate rise/fall transition times
- Cell propagation delays

These parameters are essential for generating timing libraries used during static
timing analysis (STA).

---

## Implementation

## Section 3 Tasks Overview

1. Clone custom inverter standard cell repository  
2. Explore inverter layout in Magic  
3. Perform SPICE extraction  
4. Edit SPICE model for simulation  
5. Post-layout ngspice simulations  
6. Identify and fix DRC rule issues in Sky130 Magic tech file  

---

## 1. Cloning Custom Inverter Standard Cell Repository

### Commands Used

```bash
cd Desktop/work/tools/openlane_working_dir/openlane

git clone https://github.com/nickson-jose/vsdstdcelldesign

cd vsdstdcelldesign

cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

ls
Loading Inverter Layout in Magic
bash
複製程式碼
magic -T sky130A.tech sky130_inv.mag &
2. Exploring the Custom Inverter Layout
Layout Verification Observations
NMOS and PMOS devices correctly identified

PMOS placed in n-well region

NMOS placed in p-substrate

Output node (Y) connected to drains of PMOS and NMOS

PMOS source connected to VDD (VPWR)

NMOS source connected to VSS (VGND)

Manual deletion of layout regions confirms DRC error detection capability.

3. SPICE Extraction using Magic
SPICE extraction converts physical layout information into an electrical netlist
including parasitic components.

Commands in Magic Tkcon Window
tcl
複製程式碼
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
Output Generated
sky130_inv.ext

sky130_inv.spice

These files contain device models, connectivity, and extracted parasitics.

4. Editing SPICE Model for Simulation
Layout Grid Measurement
Unit distance measurements are performed in Magic to correctly parameterize
device dimensions in the SPICE file.

SPICE File Modifications
Inclusion of Sky130 transistor models

Definition of supply voltage (VDD = 3.3 V)

Input pulse source definition

Load capacitance definition

Transient analysis command

The edited SPICE file is now ready for ngspice simulation.

5. Post-Layout ngspice Simulations
Commands Used
bash
複製程式碼
ngspice sky130_inv.spice
tcl
複製程式碼
plot y vs time a
Rise Transition Time Calculation
Definition:
Rise transition time = Time(output reaches 80%) − Time(output reaches 20%)

20% of output = 0.66 V

80% of output = 2.64 V

text
複製程式碼
Rise transition time = 2.24638 − 2.18242
                     = 0.06396 ns
                     = 63.96 ps
Fall Transition Time Calculation
Definition:
Fall transition time = Time(output reaches 20%) − Time(output reaches 80%)

text
複製程式碼
Fall transition time = 4.0955 − 4.0536
                     = 0.0419 ns
                     = 41.9 ps
Rise Cell Delay Calculation
Definition:
Rise cell delay = Time(output rises to 50%) − Time(input falls to 50%)

50% of 3.3 V = 1.65 V

text
複製程式碼
Rise cell delay = 2.21144 − 2.15008
                = 0.06136 ns
                = 61.36 ps
Fall Cell Delay Calculation
Definition:
Fall cell delay = Time(output falls to 50%) − Time(input rises to 50%)

text
複製程式碼
Fall cell delay = 4.07 − 4.05
                = 0.02 ns
                = 20 ps
6. DRC Rule Debugging and Correction in Magic Tech File
Objective
Identify and correct incorrectly implemented DRC rules in the Sky130 Magic
technology file by referencing official SkyWater rules.

Reference:
https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html

Downloading DRC Test Files
bash
複製程式碼
cd
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests
ls -al
Opening Magic with Enhanced Graphics
bash
複製程式碼
gvim .magicrc
magic -d XR &
Identified and Fixed DRC Rule Issues
poly.9 Rule (Simple Rule)
Incorrect spacing check implementation

No violation flagged for spacing < 0.48 µm

Rule corrected by updating sky130A.tech

Verified using drc check and drc why

difftap.2 Rule
Missing violation for spacing < 0.42 µm

Corrected rule logic implemented

Verified through Magic DRC re-run

nwell.4 Rule (Complex Rule)
No error flagged when nwell lacks tap cell

Updated rule and enabled full DRC style

tcl
複製程式碼
tech load sky130A.tech
drc style drc(full)
drc check
drc why
Conclusion
This lab demonstrates the complete flow of custom standard cell development,
including layout verification, parasitic extraction, post-layout characterization,
and DRC rule debugging. The corrected Magic tech file ensures accurate rule checking,
while ngspice simulations provide reliable timing metrics required for standard cell
library generation and integration into automated VLSI flows.
