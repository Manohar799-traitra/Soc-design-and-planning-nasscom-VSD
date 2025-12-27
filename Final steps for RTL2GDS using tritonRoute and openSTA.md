Section 5 – Finalization of RTL-to-GDSII Flow Using TritonRoute and OpenSTA

5.1 Introduction

The final stage of the RTL-to-GDSII flow integrates physical verification, detailed routing, parasitic extraction, and timing validation to ensure that the synthesized design meets functional and performance specifications before GDSII generation. This section focuses on completing the Place & Route (PnR) flow for the picorv32a design by generating the Power Distribution Network (PDN), performing detailed routing using TritonRoute, extracting parasitic effects with SPEF, and analyzing post-route timing with OpenSTA. These steps are crucial for ensuring power integrity, timing closure, and manufacturability of the design in the SkyWater 130nm process.

5.2 Tasks Overview

The main tasks executed in this section include:

Power Distribution Network (PDN) generation and exploration:
Ensuring the design has a robust network for delivering power to all standard cells and macros, with low IR drop and reduced ground bounce.

Detailed routing using TritonRoute:
Completion of global and detailed routing, resolving congested areas and connecting all pins and macros according to design rules and metal layer priorities.

Post-route parasitic extraction using SPEF:
Extraction of parasitic capacitance, resistance, and coupling effects from the routed layout to reflect realistic RC delays.

Post-route timing analysis using OpenSTA:
Validating setup, hold, and overall timing with realistic parasitic information to confirm design meets timing requirements post-routing.

All logs, reports, and routed layout results are maintained in:
Section 5 Run – 26-03_08-45.

5.3 PDN Generation and Layout Exploration
5.3.1 Preparation

Before generating the PDN, the design was prepped and validated in the OpenLANE environment:

# Navigate to the OpenLANE working directory
cd ~/Desktop/work/tools/openlane_working_dir/openlane

# Enter the OpenLANE Docker environment
docker

# Launch interactive OpenLANE flow
./flow.tcl -interactive

# Load required packages for OpenLANE
package require openlane 0.9

# Prepare the picorv32a design
prep -design picorv32a

# Include the custom LEF files
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Set synthesis parameters
set ::env(SYNTH_STRATEGY) "DELAY 3"
set ::env(SYNTH_SIZING) 1

# Run synthesis
run_synthesis


After synthesis, standard PnR stages including floorplanning, IO placement, tap insertion, and decap placement were executed:

init_floorplan
place_io
tap_decap_or
run_placement
unset ::env(LIB_CTS)
run_cts

5.3.2 PDN Generation

Once placement and CTS were completed, the PDN generation command was executed:

gen_pdn


This step automatically created a hierarchical grid-based power and ground network connecting all standard cells and macros while respecting metal layer constraints. Screenshots of the generated PDN confirmed proper routing of VDD and GND rails, including tie-ins for decoupling capacitors.

5.3.3 PDN Verification

The generated PDN layout was inspected using Magic:

cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/floorplan/
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef def read 14-pdn.def &


This confirmed correct abutment of rails, proper connectivity to power taps, and uniform distribution across the floorplan, ensuring minimal IR drop in high-current regions.

5.4 Detailed Routing Using TritonRoute
5.4.1 Routing Preparation

Detailed routing resolves all local connectivity and layer assignments for signal nets. Before executing TritonRoute, design environment variables were verified:

echo $::env(CURRENT_DEF)
echo $::env(ROUTING_STRATEGY)

5.4.2 Executing Detailed Routing

TritonRoute was invoked for detailed routing:

run_routing


This step performed:

Global routing adjustments to avoid congestion.

Layer assignment based on metal priority and spacing rules.

Pin-to-pin detailed connection while minimizing via usage and crosstalk.

The resulting routed DEF file included complete connections for all nets, verified against design rules. Screenshots of the routed layout in Magic confirmed all pins were correctly connected and routed tracks followed expected metal layers.

5.4.3 Routed Layout Verification

Routed DEF files were loaded in Magic for inspection:

cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef def read picorv32a.def &


Visual inspection verified signal integrity, correct layer assignment, and adherence to routing constraints. The fast route guide provided an overview of routing efficiency and potential congestion regions.

5.5 Post-Route Parasitic Extraction
5.5.1 SPEF Extraction

To perform realistic timing analysis, parasitic RC values were extracted using the SPEF format:

cd ~/Desktop/work/tools/SPEF_EXTRACTOR
python3 main.py \
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef \
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def


The output picorv32a.spef contained:

Net-by-net resistance and capacitance values.

Coupling capacitances between neighboring nets.

Parasitic delay contributions to each standard cell.

This step was critical for reflecting realistic delays in post-route STA.

5.6 Post-Route Timing Analysis Using OpenSTA
5.6.1 Preparing OpenROAD Environment

The routed design along with parasitics was analyzed in OpenSTA through the OpenROAD environment:

openroad

# Load routed LEF and DEF
read_lef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.def

# Create OpenROAD database
write_db pico_route.db
read_db pico_route.db

# Load post-synthesis netlist
read_verilog /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/synthesis/picorv32a.synthesis_preroute.v

# Load standard cell library
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Load constraints
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Propagate clocks
set_propagated_clock [all_clocks]

# Load extracted parasitics
read_spef /openLANE_flow/designs/picorv32a/runs/26-03_08-45/results/routing/picorv32a.spef

# Generate timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
exit

5.6.2 Analysis and Observations

The post-route timing analysis revealed:

Critical paths with increased delay due to parasitic capacitance on long nets.

Fanout-induced timing violations were identified and annotated.

Timing slack and setup/hold margins were within acceptable limits after minor ECO adjustments.

Screenshots and reports confirmed that the design remained timing-closed after parasitic-aware analysis.

5.7 Summary and Key Outcomes

PDN Generation: Successfully created robust power network with correct VDD/GND distribution.

Detailed Routing: TritonRoute achieved complete routing with minimal congestion and correct metal layer assignment.

Parasitic Extraction: SPEF accurately reflected interconnect RC effects.

Post-Route STA: Timing analysis verified design closure with extracted parasitics, confirming the design is ready for GDSII generation.

All Section 5 results, including PDN, routed DEF, SPEF, and STA reports, are stored in Section 5 Run – 26-03_08-45 for further review or documentation purposes.
