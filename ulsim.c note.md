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
**openair1/SIMULATION/NR_PHY/ulsim.c**  
OAI uplink physical layer simulation entry point. Simulates uplink transmission and decoding without over-the-air RF hardware.

---

## Initialization and Flow Overview

### General
`nr_ulsim` is a command-line simulation tool for testing 5G NR uplink PHY procedures. It bypasses RF front-end transmission and directly tests encoding, modulation, channel, and decoding within one process.

---

# nr_ulsim Simulation Flow (OpenAirInterface)

## Overview

This document describes the overall process and internal modules involved in the `nr_ulsim` uplink physical layer simulator of the OAI project.

---

## 📈 Flowchart
![Editor _ Mermaid Chart-2025-06-23-132355](https://github.com/user-attachments/assets/d9d2f8dc-40c0-4cb4-9500-28662782fd69)


## 🔍 Module Descriptions

### 1. Program Startup and Parameter Parsing

- Execution starts from `main()`.
- Parses CLI options using `getopt` (e.g., SNR range, MCS, number of trials).
- Loads and sets global parameters for simulation.

---

### 2. Simulation Environment Initialization

- Initializes random seeds and sin-cos LUTs.
- Configures the channel model (AWGN, TDL-A/B/C).
- Sets up modulation, resource block settings, HARQ parameters.

---

### 3. Simulation Loop (`n_trials`)

- Generates a new Transport Block (TB).
- Appends CRC for error detection.
- Performs LDPC encoding to produce the codeword.
- Applies rate matching and interleaving.
- Modulates symbols (QPSK/16QAM/64QAM).
- Adds DMRS (reference signals for channel estimation).
- Passes data through the simulated channel (AWGN or fading).
  
---

### 4. Receiver Processing

- Demodulates the received signal and computes Log-Likelihood Ratios (LLRs).
- Performs LDPC decoding to retrieve the original data.
- Verifies the decoded result using CRC.
- Uses HARQ logic to determine if retransmission is required.

---

### 5. Performance Statistics

- Collects simulation metrics: Block Error Rate (BLER), throughput, delay, etc.
- Determines if the configured number of trials has been completed.
- If not, continues the simulation loop.

---

### 6. Finalization

- Prints or saves final results (e.g., to CSV file).
- Frees all allocated memory and gracefully exits the simulation.

---
# 🧠 NR Uplink Simulator: Main Flow Structure Notes

This document serves as a structural note for the `nr_ulsim.c` simulation main program.  
It divides the program into **three major sections** and provides a detailed breakdown  
of the **Startup & Initialization** phase.  

This corresponds to the physical layer (PHY) simulation logic in **OpenAirInterface (OAI)**.

![Editor _ Mermaid Chart-2025-06-25-163830](https://github.com/user-attachments/assets/ebd60712-c057-498c-a0e1-4d59de972d83)

The program is divided into the following three main parts:

1. **Program Startup & CLI Parsing**
   - Entry point: `main()`
   - Parses command-line arguments (e.g., SNR, MCS, number of trials).
   - Sets global simulation parameters.

2. **Simulation Initialization**
   - Initializes system configuration, memory buffers, and channel models.
   - Sets up modulation, frame structure, and UE/gNB parameters.

3. **Main Simulation Loop (`n_trials`)**
   - Runs the full uplink PHY transmission and reception chain.
   - Includes transport block generation, encoding, modulation, channel transmission, decoding, and error checking.

## 1. Program Startup & CLI Parsing

This section handles program startup and command-line interface (CLI) option parsing. It sets up initial simulation parameters and prepares the program to run the uplink simulation.

### 1.1 Command-Line Argument Parsing

- Uses `getopt` to parse CLI options like SNR range, number of trials, MCS, etc.
- Validates input parameters and stores them in global or local variables.

### 1.2 Global Parameter Setup

- Loads or initializes global parameters related to the NR frame structure.
- Sets values for bandwidth, subcarrier spacing, number of resource blocks, etc.

### 1.3 Logging and Output Setup

- Configures logging levels and output file handles.
- Prepares CSV files or other output formats if requested.

### 1.4 Memory Allocation

- Allocates buffers for transmitted and received data.
- Prepares data structures needed throughout the simulation.

### 1.5 Initial Checks and Messages

- Prints summary of simulation configuration.
- Verifies correctness of parameters before entering main loop.

## 🔧 1. Program Startup & CLI Parsing (Part 1)

```c
int main(int argc, char **argv)
```
- Entry point of the simulation.
- Receives command-line arguments for configuration.

```c
init_openair0();
```
- Initializes the RF frontend abstraction (though unused in pure simulation).

```c
randominit(0);
set_taus_seed(0);
```
- Seeds random number generators for reproducibility.

## 🔧 1. Program Startup & CLI Parsing (Part 2)

```c
load_configmodule(argc, argv);
```
- Loads and parses configuration modules (usually for global OAI params).

```c
logInit();
```
- Initializes the logging system.

```c
set_glog(LOG_DEBUG, LOG_HIGH, 1);
```
- Sets global log verbosity level.

```c
int ret = 1;
```
- Default return value, will be set to 0 if simulation completes successfully.

```c
cpu_set_t cpuset;
```
- Declares CPU affinity set for potential multi-core execution binding.

## 🔧 1. Program Startup & CLI Parsing (Part 3)

```c
init_openair0();
```
- Initializes RF hardware interface abstraction (even if unused in simulation mode).

```c
randominit(0);
```
- Initializes the random number generator with seed 0.

```c
crcTableInit();
```
- Initializes the CRC table used in encoding/decoding.

```c
generate_ul_reference_signal_sequences();
```
- Generates reference signal sequences (e.g., DMRS) for uplink.

```c
init_context(gNB, UE, &gNB_config, &UE_config);
```
- Sets up the gNB and UE context structures with simulation parameters.

## 🔧 1. Program Startup & CLI Parsing (Part 4)

```c
if (input_fd) {
  // When input file is provided, read simulation parameters and data from file.
  // Useful for replay or predefined scenarios.
  read_input_file(input_fd, &sim_params);
}
```
- Handles optional input from a file to override or provide simulation parameters.

```c
if (output_fd) {
  // Opens file for writing simulation output results.
  open_output_file(output_fd);
}
```
- Prepares output destination for simulation logs or data.

```c
if (scg_fd) {
  // Reads secondary cell group configuration if provided.
  read_scg_config(scg_fd);
}
```
- Loads additional configuration for simulation (optional).
## 🔧 1. Program Startup & CLI Parsing (Part 5)

```c
srand(time(NULL));
```
- Initialize random seed for reproducibility

```c
init_sin_cos_LUT();
```
- Initialize sine and cosine look-up tables used for OFDM modulation/demodulation

```c
init_simulation_globals(&sim_params);
```
- Initialize global simulation parameters like frame parameters, modulation schemes, HARQ config

## 2. Simulation Environment Initialization

This section sets up the simulation environment after parsing the CLI parameters. It initializes the random number generator, configures the channel model, modulation scheme, resource blocks, and HARQ parameters.

### 2.1 Random Number Generator and LUT Initialization

- Seeds the random number generator using a high-resolution timer or fixed seed.
- Initializes sine-cosine lookup tables for OFDM modulation and demodulation.
- Ensures repeatability of simulations with controlled seeds.

### 2.2 Channel Model Configuration

- Selects channel model type based on user input (e.g., AWGN, TDL-A, TDL-B, TDL-C).
- Sets fading parameters such as delay profiles, Doppler spread.
- Configures multipath channel simulation buffers.

### 2.3 Modulation and Resource Block Setup

- Sets modulation scheme according to MCS (QPSK, 16QAM, 64QAM, etc.).
- Determines number of resource blocks (RBs) and symbols per slot.
- Sets OFDM symbol size and cyclic prefix length (normal or extended).

### 2.4 HARQ and Link Adaptation Parameters

- Initializes HARQ parameters such as max LDPC decoding iterations.
- Configures number of HARQ rounds and related counters.
- Prepares data structures for retransmissions and error tracking.

### 2.5 Other Simulation Settings

- Sets SNR range and increments for simulation loop.
- Configures logging verbosity and output files.
- Initializes performance counters and statistics storage.

## 🔧 2. Simulation Environment Initialization (Part 1)

```c
srand(time(NULL));
Initializes the random seed based on the current system time.

Ensures stochastic processes (e.g., noise generation, fading) are randomized.

Supports reproducibility if a fixed seed is later used.

🔧 2. Simulation Environment Initialization (Part 2)
c
複製程式碼
init_sin_cos_lut();
Precomputes sine and cosine values for OFDM symbol modulation and demodulation.

Improves computational efficiency during FFT/IFFT operations.

Essential for waveform generation and channel simulation.

🔧 2. Simulation Environment Initialization (Part 3)
c
複製程式碼
if (channel_model == AWGN) {
  init_awgn_channel();
} else if (channel_model == TDL_A) {
  init_tdl_channel(TDL_A);
} else if (channel_model == TDL_B) {
  init_tdl_channel(TDL_B);
}
// Additional models possible
Determines which channel model is selected: AWGN, TDL-A, TDL-B, etc.

Initializes channel parameters accordingly (e.g., delay spread, Doppler).

Prepares channel state for subsequent signal propagation simulation.
