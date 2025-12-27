# Section 4 – Pre-Layout Timing Evaluation and Clock Tree Optimization  


## Theory

Pre-layout timing analysis is a critical phase in ASIC design that validates timing feasibility before detailed routing. At this stage, synthesis results, cell delays, and estimated interconnect parasitics are analyzed to identify setup and hold violations. A well-structured clock tree is essential to minimize skew, reduce insertion delay, and ensure synchronous behavior across the design. Early timing closure significantly reduces iterations in later physical design stages.

---

## Implementation

### Section 4 Objectives

The following activities were carried out to integrate a custom standard cell into the OpenLANE flow and perform comprehensive timing analysis:

- Correct minor DRC violations and validate layout readiness  
- Save and reopen the finalized custom layout  
- Generate LEF abstraction from layout  
- Integrate LEF and library files into the `picorv32a` design source  
- Update OpenLANE configuration to include custom cells  
- Execute synthesis with the newly added inverter  
- Mitigate timing violations through parameter tuning  
- Validate custom cell acceptance during floorplan and placement  
- Perform post-synthesis timing analysis using OpenSTA  
- Apply timing ECOs to reduce violations  
- Replace synthesis netlist post-ECO and continue PnR  
- Conduct post-CTS timing analysis in OpenROAD  
- Analyze CTS behavior by modifying clock buffer lists  

---

## Directory Structure and Logs

- **Tasks 1–4:** `Section 4 – Tasks 1 to 4 (vsdstdcelldesign)`  
- **Task 4 Source Files:** `Section 4 – Task 4 (src)`  
- **Task 5 Design Files:** `Section 4 – Task 5 (picorv32a)`  
- **Tasks 6–8 & 11–13 Runs:** `24-03_10-03`  
- **Tasks 9–11 Runs:** `25-03_18-52`  

---

## Task 1 – DRC Cleanup and Layout Validation

Before integrating the custom inverter into the design flow, the following physical constraints were verified:

1. Input and output pins align with routing track intersections  
2. Cell width is an odd multiple of horizontal track pitch  
3. Cell height is an even multiple of vertical track pitch  

The custom inverter layout was opened in Magic, and routing grids were aligned to the standard cell tracks to confirm compliance with library specifications.

---

## Task 2 – Final Layout Save and Reload

Once validation was complete, the layout was saved under a new identifier to distinguish it from the original version. The updated layout was reloaded in Magic to confirm integrity and consistency.

---

## Task 3 – LEF Generation

A LEF file was generated from the finalized layout to provide an abstract physical representation required for placement and routing stages in OpenLANE.

---

## Task 4 – Integration into picorv32a Design

The generated LEF and associated Liberty timing files were copied into the `src` directory of the `picorv32a` design. This ensured availability of the custom cell during synthesis and physical implementation.

---

## Task 5 – OpenLANE Configuration Updates

The `config.tcl` file was modified to:
- Point synthesis to the local library files  
- Include the custom LEF using the `EXTRA_LEFS` variable  

This enabled OpenLANE to recognize and utilize the custom inverter throughout the flow.

---

## Task 6 – Synthesis with Custom Cell

The OpenLANE flow was launched in interactive mode, and synthesis was executed with the newly added inverter cell. Additional commands were used to explicitly load the custom LEF into the merged LEF database.

---

## Task 7 – Timing Optimization via Parameter Tuning

Initial synthesis runs showed timing violations. To address this:
- Synthesis strategy was shifted toward delay optimization  
- Cell sizing and buffering options were enabled  

After re-synthesis, worst negative slack was improved to zero at the cost of a moderate area increase.

---

## Task 8 – Floorplan and Placement Verification

With successful synthesis, floorplanning and congestion-aware placement were performed. In cases where automated commands failed, equivalent lower-level Tcl commands were invoked. The placement DEF was inspected in Magic to verify proper insertion and abutment of the custom inverter.

---

## Task 9 – Post-Synthesis Timing Analysis

OpenSTA was used to analyze timing on a synthesis run with known violations. Custom SDC constraints were applied, and timing paths were examined to identify high fanout nets contributing to delay.

---

## Task 10 – Timing ECO Fixes

Engineering Change Orders (ECOs) were applied directly in OpenSTA by:
- Identifying critical nets with excessive fanout  
- Replacing low-drive logic gates with higher drive-strength equivalents  

Each replacement resulted in incremental slack improvement, reducing overall timing violations.

---

## Task 11 – Netlist Replacement and PnR Continuation

The optimized netlist was written back and replaced the original synthesis netlist. Floorplanning, placement, and clock tree synthesis (CTS) were rerun using the updated netlist to ensure consistency.

---

## Task 12 – Post-CTS Timing Analysis

Post-CTS timing was analyzed using OpenROAD’s integrated OpenSTA engine. Propagated clocks were enabled, and detailed timing reports were generated to assess setup and hold performance.

---

## Task 13 – Clock Buffer Exploration

To study clock tree behavior, a specific clock buffer cell was temporarily removed from the CTS buffer list. CTS was rerun, and timing skew reports were analyzed. The buffer was then restored to maintain design stability.

---

## Summary

This section demonstrated the complete flow of integrating a custom standard cell into an ASIC design, achieving timing closure through synthesis tuning, ECO fixes, and clock tree optimization. The exercise highlights the importance of early timing analysis and disciplined clock tree design in achieving robust physical implementation.
