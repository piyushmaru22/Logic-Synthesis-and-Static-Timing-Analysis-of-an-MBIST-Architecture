PROJECT IMPLEMENTATION THEORY: SYNTHESIS, TIMING, AND POWER CHARACTERIZATION

1. Architectural Configurations
The project evaluates the physical overhead of Design-for-Testability (DFT) by comparing two distinct architectural configurations:
- Configuration A (WITHOUT_MBIST - Baseline): A pure SRAM memory array modeled in Verilog. It contains standard control signals (clock, address, data-in, write-enable) but lacks internal testing logic. It requires external Automatic Test Equipment (ATE) to stimulate read/write cycles, making it vulnerable to post-manufacturing defects without on-chip testability hooks.
- Configuration B (WITH_MBIST - Self-Testing): Integrates a hardware MBIST controller block with the memory. A built-in Finite State Machine (FSM) autonomously generates read/write cycles to execute the Checkerboard algorithm. Local comparator logic reads cells, compares them against expected patterns, and instantly raises a 'fail' flag on mismatch, enabling full at-speed internal verification.

2. Gate-Level Synthesis Theory (Yosys)
To move beyond behavioral simulation, the RTL is processed using the Yosys Synthesis Suite to map the logic onto physical hardware gates. The synthesis pipeline follows four phases:
- Front-End Parsing: Translates behavioral Verilog into an Abstract Syntax Tree (AST).
- Logic Optimization: Flattens the design, eliminates dead-code, and extracts the FSM logic.
- Technology Mapping: Maps the technology-independent generic gates onto concrete standard cells defined in the SkyWater 130nm high-density PDK (sky130_hd.lib).
- Netlist Export: Outputs a flattened gate-level Verilog netlist containing the exact interconnections of standard library cells, ready for physical analysis.

3. Static Timing Analysis Theory (OpenSTA)
Timing validation is performed using OpenSTA to ensure signals propagate through the synthesized logic fast enough to meet clock requirements.
- The tool traces the physical critical path—the longest combinational delay between any two sequential registers (e.g., from the MBIST FSM state registers through the memory address decoders to the comparator output).
- Data Arrival Time is calculated based on the cumulative delay of standard cells and interconnects.
- Data Required Time is determined by the target clock period and library setup/hold limits.
- Timing Slack is evaluated (Slack = Required Time - Arrival Time). Positive slack indicates timing closure, while negative slack indicates a timing violation.

4. Static Power Characterization Theory
Power consumption is extracted under static conditions using the Liberty (.lib) state-dependent lookup tables provided by the SkyWater 130nm PDK. Power is categorized into three components:
- Internal Power: Dynamic power consumed within standard cells due to charging/discharging internal capacitances during logical switching.
- Switching Power: Power consumed by charging and discharging external net capacitances.
- Leakage Power: Static power consumed while cells are in steady state, primarily driven by subthreshold leakage currents defined in the Liberty tables.

5. Theoretical Trade-offs of DFT Integration
The integration of the MBIST controller introduces an inherent trade-off in the physical design. The addition of address counters, state transitions, and expected data comparators increases the overall sequential register density. This results in an increased physical critical path (a timing penalty) and an increase in active power consumption (power overhead) compared to the raw baseline memory. Quantifying this exact overhead is critical to prove that the DFT penalty is minimal and highly justified for the benefit of autonomous, on-chip testing.
