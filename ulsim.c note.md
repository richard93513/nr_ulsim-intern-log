# OAI Project Directory Structure

| Directory Path     | Description                                                                                 |
|--------------------|---------------------------------------------------------------------------------------------|
| openair1/          | Implementation of Layer 1 (PHY). Includes LDPC encoder/decoder source files such as nrLDPC_encoder.c and ldpc_decoder.c. |
| openair2/          | Implementation of Layer 2 modules (MAC / RLC / PDCP / RRC). Can be skipped for LDPC-focused study. |
| openair3/          | Layer 3 modules (e.g., NGAP, GTP for core network). Not directly related to LDPC.           |
| radio/             | Implementation of simulated RF channel for radio front-end. Handles transmission simulation between gNB and UE. |
| cmake_targets/     | Build scripts and configuration for compiling and launching softmodem. Main execution happens from this directory. |
| executables/       | Contains main entry points for gNB, UE, and other top-level executables.                     |
| CMakeLists.txt     | Top-level CMake configuration file for building the entire project.                         |
| doc/               | Documentation and architecture descriptions (e.g., system diagrams, flow explanations).     |
| tools/             | Developer tools for code formatting, analysis, and maintenance.                             |
| docker/            | Docker build files and environment setup for container-based deployment.                     |
| ci-scripts/        | CI/CD scripts and configurations for automated testing and integration.                     |
---
# Uplink Simulation
## ulsim.c

---

## File Path
**openair1/TEST/ULSIM/nr_ulsim.c**  
OAI uplink physical layer simulation entry point. Simulates uplink transmission and decoding without over-the-air RF hardware.

---

## Initialization and Flow Overview

### General
`nr_ulsim` is a command-line simulation tool for testing 5G NR uplink PHY procedures. It bypasses RF front-end transmission and directly tests encoding, modulation, channel, and decoding within one process.

---



### Transmission Chain (TX)

1. Generate transport block (TB)
2. Apply CRC
3. Perform LDPC encoding
4. Rate matching and interleaving
5. Modulation (QPSK/16QAM/64QAM)
6. Add DMRS (if applicable)
7. Add AWGN or fading channel (simulated)

---

### Reception Chain (RX)

1. Demodulation
2. LLR calculation
3. LDPC decoding
4. CRC check

---
