# Lab 2 – Floorplanning and Placement using OpenLANE
  
**Design:** picorv32a  
**Flow:** OpenLANE Interactive Flow  
**Run Directory:** Section 2 Run – 27-12_17-28  

---

## Theory

### Floorplanning in VLSI Physical Design

Floorplanning is a critical phase of the VLSI physical design flow that defines the
overall physical dimensions of the chip and establishes the foundation for placement
and routing. During this stage, the die area and core area are determined, I/O ports
are positioned, and essential physical cells such as tap cells and decoupling
capacitor (decap) cells are inserted.

A well-optimized floorplan minimizes routing congestion, improves timing closure,
enhances power integrity, and reduces overall silicon area. In OpenLANE, the
floorplanning stage is tightly integrated with the Sky130 PDK and OpenROAD toolchain.

The Sky130 technology uses a unit-distance-based coordinate system, where
1000 database units correspond to 1 micron. This abstraction allows consistent
physical interpretation across DEF, LEF, OpenROAD, and Magic tools.

---

### Placement in VLSI Physical Design

Placement is the process of assigning exact legal locations to all standard cells
within the predefined floorplan. Modern placement engines are congestion-aware and
timing-driven to ensure optimal wirelength, routability, and performance.

OpenLANE performs congestion-aware placement by default using OpenROAD, ensuring
that standard cells are placed while respecting design rules, power rails, clocking
constraints, and routing resource availability.

---

## Implementation

### Section 2 Tasks

1. Run picorv32a design floorplan using OpenLANE  
2. Calculate die area from floorplan DEF  
3. Load and inspect floorplan DEF in Magic  
4. Perform congestion-aware placement  
5. Load and inspect placement DEF in Magic  

All logs, reports, and results are available in:



---

## 1. Floorplan Execution

After completing synthesis, the floorplanning stage is executed in the OpenLANE
interactive environment.

### Commands Used

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
run_floorplan

Outputs Generated

picorv32a.floorplan.def

Floorplan logs and reports

I/O pin placement and core boundary

Tap cell and decap cell insertion

2. Die Area Calculation from Floorplan DEF
Formula

Area of die in microns = Die width in microns × Die height in microns

Values from Floorplan DEF

1000 Unit Distance = 1 Micron

Die width (unit distance) = 660685

Die height (unit distance) = 671405

Conversion to Microns

Die width in microns = 660685 / 1000 = 660.685 µm

Die height in microns = 671405 / 1000 = 671.405 µm

Die Area Calculation

Area of die = 660.685 × 671.405

Area of die = 443587.212425 µm²

Observation

The calculated die area represents the physical footprint required to accommodate
standard cells, power distribution, clock structures, and routing resources.

3. Floorplan Visualization using Magic

The Magic VLSI tool is used to visualize and verify the generated floorplan DEF.

Commands Used

cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-12_17-28/results/floorplan/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef \
def read picorv32a.floorplan.def &

Key Observations

Equidistant placement of I/O ports around the die

Correct port layer assignment via configuration

Decap cells placed near power rails

Diagonally equidistant tap cells for well biasing

Standard cells unplaced at origin before placement

4. Congestion-Aware Placement Execution

Congestion-aware placement assigns legal positions to all standard cells while
minimizing routing congestion and wirelength.

Command Used
run_placement

5. Placement Visualization using Magic
Commands Used
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-12_17-28/results/placement/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
lef read ../../tmp/merged.lef \
def read picorv32a.placement.def &

Observations

Standard cells are legally placed within defined rows

Uniform distribution across the core area

No visible placement overlaps or major congestion

Design is ready for clock tree synthesis and routing

Exit Commands
exit
exit

Conclusion

This lab successfully demonstrates floorplanning and congestion-aware placement of
the picorv32a design using the OpenLANE flow and Sky130 PDK. Die area estimation,
physical verification using Magic, and legal standard cell placement establish a
robust foundation for subsequent CTS and routing stages.
