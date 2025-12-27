## Theory – Open-Source EDA and OpenLANE

Open-source Electronic Design Automation (EDA) tools have gained prominence due to
their accessibility, transparency, and adaptability for academic and industrial research.

### OpenLANE
OpenLANE is an automated RTL-to-GDSII flow built on top of several open-source tools
such as Yosys, OpenROAD, Magic, and KLayout. It is optimized for the Sky130 PDK and
supports interactive as well as non-interactive design flows.

### Sky130 PDK
Sky130 is a 130 nm open-source process design kit released by SkyWater Technology
in collaboration with Google, enabling fully open silicon design.


## Commands Used for Synthesis

```bash
# Navigate to OpenLANE directory
cd Desktop/work/tools/openlane_working_dir/openlane

# Launch OpenLANE Docker
docker

# Start interactive OpenLANE flow
./flow.tcl -interactive

# Load OpenLANE package
package require openlane 0.9

# Prepare picorv32a design
prep -design picorv32a

# Run synthesis
run_synthesis

# Exit OpenLANE
exit

# Exit Docker container
exit


## Synthesis Results

- Design: picorv32a
- Total number of cells: 14876
- Number of D Flip-Flops: 1613

All synthesis logs and reports are available in:
Logs_and_Reports/Section1_Run_15-03_15-51/


## Flop Ratio Calculation

### Formula

Flop Ratio = (Number of D Flip-Flops) / (Total Number of Cells)

Percentage of DFFs = Flop Ratio × 100

---

### Values from Synthesis Report

- Number of D Flip-Flops = 1613
- Total Number of Cells = 14876

---

### Calculation

Flop Ratio = 1613 / 14876  
Flop Ratio = 0.108429685  

Percentage of DFFs = 0.108429685 × 100  
Percentage of DFFs = **10.84296854 %**

---

### Observation

Approximately **10.84%** of the synthesized design consists of sequential elements,
indicating a moderately balanced datapath and control logic structure.


