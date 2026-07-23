# uart_abp_integration

Overview
This repository contains the complete RTL design, synthesis scripts, and functional verification environment for an APB-to-UART bridge. The system integrates standard UART transmission and reception blocks with an APB (Advanced Peripheral Bus) slave interface, allowing a master processor to seamlessly read and write serial data.

The RTL is strictly modular, structurally verified, and optimized for physical design handoff.

Key Features
Standard Protocol Integration: Bridges 32-bit APB master transactions to an 8-bit UART serial interface.

Modular Architecture: Clean separation of the APB bridge logic and the core UART hardware (uart_tx, uart_rx, baud_generator).

Open-Source Synthesis: Includes a custom run.ys Yosys script configured to establish exact module hierarchy and generate accurate RTL schematics without structural errors or unwanted latches.

Physical Design Ready: The top-level wrapper (apb_uart_system) is cleanly synthesized and ready to be pushed through the back-end flow (floorplanning, placement, and routing).

System Hierarchy
The design is encapsulated within a single top-level wrapper to ensure clean hierarchical boundaries during synthesis:

apb_uart_system (Top-Level Integration)

uart_apb_slave (APB bus interface and control state machine)

uart_top (UART hardware block)

baud_generator

uart_tx (Serializer)

uart_rx (Deserializer)

Verification Environment
The testbench (tb_apb_uart_system.sv) rigorously proves out the functional logic using a physical loopback methodology:

Setup: The tx_pin is directly wired to the rx_pin at the top level.

Stimulus: The testbench drives the psel, penable, and pwrite APB control signals to initiate a write transaction containing a 0xA5 test payload.

Transmission: The UART hardware successfully serializes the data at a 9600 baud rate.

Validation: The APB master successfully reads the looped-back data via the APB bridge, confirming bit-level functional correctness and outputting SOC SUCCESS.

run.ys code :

read_verilog design.sv
hierarchy -check -top apb_uart_system
proc; opt; memory; opt; fsm; opt
select apb_uart_system
