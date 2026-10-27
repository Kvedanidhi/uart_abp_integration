# APB-to-UART Integration Subsystem

A synthesizable APB-to-UART bridge implemented in Verilog/SystemVerilog. The design connects a 32-bit APB interface to an 8-bit UART interface and includes Yosys-based structural synthesis plus functional loopback verification.

## Overview

This repository contains the RTL, synthesis flow, and verification environment for a compact APB-to-UART subsystem. The design is organized into clearly defined modules to preserve hierarchy during synthesis and simplify integration into a larger SoC or ASIC/FPGA flow.

### Highlights

- 32-bit APB slave interface with UART data-path integration
- 8-bit UART transmitter and receiver
- Configurable baud-generation block
- Clean top-level integration through `apb_uart_system`
- Yosys synthesis script with explicit hierarchy checking
- Functional UART loopback verification at 9600 baud
- Synthesizable RTL with no intentional latches

## Architecture

The design uses the following hierarchy:

```text
apb_uart_system
└── uart_apb_slave
    └── uart_top
        ├── baud_generator
        ├── uart_tx
        └── uart_rx
```

### Module Responsibilities

| Module | Responsibility |
| --- | --- |
| `apb_uart_system` | Top-level APB-to-UART integration wrapper |
| `uart_apb_slave` | APB transaction handling, control, and status logic |
| `uart_top` | UART subsystem integration |
| `baud_generator` | UART timing and baud-rate generation |
| `uart_tx` | Parallel-to-serial UART transmission |
| `uart_rx` | Serial-to-parallel UART reception |

## Verification

The testbench `tb_apb_uart_system.sv` validates the complete data path using a physical loopback configuration:

1. The top-level `tx_pin` is connected directly to `rx_pin`.
2. The APB stimulus drives a write transaction containing `0xA5`.
3. The UART transmitter serializes the payload at 9600 baud.
4. The receiver captures and reconstructs the looped-back data.
5. The APB master reads the received value through the bridge.
6. The testbench reports `SOC SUCCESS` when the loopback transaction passes.

This verifies the APB control path, UART serialization and deserialization, baud timing, and end-to-end data integrity.

## Synthesis

The repository includes `run.ys`, a Yosys synthesis script that checks and preserves the intended top-level hierarchy:

```yosys
read_verilog design.sv
hierarchy -check -top apb_uart_system

proc; opt; memory; opt; fsm; opt

select apb_uart_system
```

The synthesis flow is intended to provide a clean structural representation of the design for downstream analysis and physical-design workflows, including floorplanning, placement, and routing.

## Repository Contents

| File | Description |
| --- | --- |
| `design.sv` | Synthesizable APB-to-UART RTL design |
| `tb_apb_uart_system.sv` | Functional loopback testbench |
| `run.ys` | Yosys synthesis and hierarchy-check script |

## Design Notes

- The APB interface is 32 bits wide.
- UART data is 8 bits wide.
- The functional verification scenario uses a 9600-baud UART configuration.
- The top-level module is `apb_uart_system`.
- The RTL hierarchy is intentionally maintained to support structural inspection and integration.

## Status

The subsystem is functionally verified using APB-driven UART loopback and is structured for synthesis-oriented development.
