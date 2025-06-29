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

![Editor _ Mermaid Chart-2025-06-27-035815](https://github.com/user-attachments/assets/dfcf7b23-01eb-492f-906c-f52718623d44)

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

## 🔧 1.1 Command-Line Argument Parsing

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

## 🔧 1.2 Global Parameter Setup

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

## 🔧 1.3 Logging and Output Setup

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

## 🔧 1.4 Memory Allocation

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
## 🔧 1.5 Initial Checks and Messages

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

## 🔧 2.1 Random Number Generator and LUT Initialization
- Seeds the random number generator using a high-resolution timer or fixed seed.

- Initializes sine-cosine lookup tables for OFDM modulation and demodulation.

- Ensures repeatability of simulations with controlled seeds.

```c
randominit(0);
set_taus_seed(0);
```
- Seeds the random number generator with a fixed seed (0) for reproducibility across runs.
```c
init_sin_cos_LUT();
```
- Initializes sine and cosine lookup tables used during OFDM modulation and demodulation.

## 🔧 2.2 Channel Model Configuration
- Selects channel model type based on user input (e.g., AWGN, TDL-A, TDL-B, TDL-C).

- Sets fading parameters such as delay profiles, Doppler spread.

- Configures multipath channel simulation buffers.

```c
if (channel_model == AWGN) {
  UE2gNB = new_channel_desc_scm(UE->frame_parms.nb_antennas_tx,
                                gNB->frame_parms.nb_antennas_rx,
                                channel_model,
                                0,
                                0,
                                0);
}
```
- Initializes an AWGN channel description with no fading or Doppler.

```c
if (UE2gNB) {
  random_channel(UE2gNB, 0);
}
```
- Randomizes the multipath channel characteristics for each simulation trial.

## 🔧 2.3 Modulation and Resource Block Setup
- Sets modulation scheme according to MCS (QPSK, 16QAM, 64QAM, etc.).

- Determines number of resource blocks (RBs) and symbols per slot.

- Sets OFDM symbol size and cyclic prefix length (normal or extended).

```c
set_scs_parameters(pusch_pdu->subcarrier_spacing, &gNB->frame_parms);
```
- Sets subcarrier spacing and corresponding symbol timing for uplink frame.

```c
get_bandwidth_index(pusch_pdu->subcarrier_spacing, bandwidth);
```
- Chooses bandwidth index to determine the number of PRBs and grid size.
```c
init_nr_frame_parms(&gNB->frame_parms);
```
- Initializes the NR frame structure, including FFT size, CP length, symbol count.

## 🔧 2.4 HARQ and Link Adaptation Parameters
- Initializes HARQ parameters such as max LDPC decoding iterations.

- Configures number of HARQ rounds and related counters.
  
- Prepares data structures for retransmissions and error tracking.

```c
gNB->ulsch[UE_id] = new_gNB_ulsch(max_ldpc_iterations, N_RB_UL, 0);
```
- Allocates uplink HARQ structure and sets the max number of decoding attempts.

```c
UE->ul_harq_processes[harq_pid].round = 0;
```
- Initializes HARQ round tracking for retransmission simulation.

## 🔧 2.5 Other Simulation Settings
- Sets SNR range and increments for simulation loop.

- Configures logging verbosity and output files.

- Initializes performance counters and statistics storage.

```c
for (SNR = snr0; SNR <= snr1; SNR += snr_step) {
  ...
}
```
- Main loop of the simulation based on SNR sweep configuration.

```c
init_stats(&gNB->phy_proc_rx);
init_stats(&gNB->ulsch_decoding_stats);
```
- Prepares performance statistics tracking modules for RX and decoding.

```c
if (print_perf == 1)
  printDistribution(&gNB->phy_proc_rx, table_rx, "Total PHY proc rx");
```
- Optionally prints performance timing breakdown after simulation.

## 3. Main Simulation Loop (n_trials)
- This section describes the core PHY simulation steps iterated for each trial in nr_ulsim. Each trial
simulates the full uplink chain: from bit generation to decoding and performance evaluation.

### 3.1 Transport Block Generation
- Determine TB size based on MCS and resource allocation.

- Generate pseudo-random bitstream and append CRC.

### 3.2 LDPC Encoding & Rate Matching
- Segment TB (if needed), select LDPC base graph.

- Apply LDPC encoding and rate matching.

### 3.3 Modulation & Resource Mapping
- Modulate encoded bits (QPSK, 16QAM, etc.).

- Map to resource grid and insert DMRS.

- Generate OFDM symbols via IFFT.

### 3.4 Channel Simulation
- Simulate AWGN or fading (e.g., TDL) effects.

- Apply noise and/or multipath to transmitted signal.

### 3.5 Receiver Front-End Processing
- Perform FFT and extract received symbols.

- Channel estimation and equalization.

### 3.6 LLR Computation
- Compute soft bits (LLRs) from received symbols.

- Prepare for LDPC decoder input.

### 3.7 LDPC Decoding & CRC Checking
- Decode with LDPC and check CRC.

- Determine decoding success/failure.

### 3.8 Error Analysis & HARQ Stats
- Update block error count, CRC and decoder stats.

- Track false positives and decoder iterations.

### 3.9 Performance Logging & Reporting
- Print simulation results (BLER, BER, throughput).

- Optionally write to CSV and display timing stats.

### 4. Finalization
- Free all allocated buffers and contexts.

- Close files and gracefully exit simulation.

## 🔧 3.1 Transport Block Generation

- Calculates the appropriate TB size based on MCS, bandwidth, and other configuration.
- Generates a pseudo-random bit sequence to simulate real uplink data.
- Appends CRC for error detection before encoding.

```c
ulsch->harq_processes[harq_pid]->TBS = nr_compute_tbs(...);
```
- Computes the Transport Block Size (TBS) using 3GPP-defined tables.

```c
for (int i = 0; i < TBS_bytes; i++)
  ulsch_input_buffer[i] = (unsigned char)(taus() & 0xff);
```
- Generates pseudo-random bits using a seeded Tausworthe generator.

```c
crc = crc24a(ulsch_input_buffer, TBS_bytes);
// Appends 24-bit CRC at the end of the buffer
```
## 🔧 3.2 LDPC Encoding & Rate Matching
- Segments the TB if it exceeds 8424 bits.

- Selects LDPC base graph based on TB size and code rate.

- Applies LDPC encoding using parity-check matrices.

- Inserts filler bits (if required) and performs rate matching.

```c
nr_ulsch_encoding(...);
```
- Main entry point for LDPC encoding + rate matching.

```c
ulsch->harq_processes[harq_pid]->F = number_of_filler_bits;
```
- Stores number of filler bits used in rate matching.

## 🔧 3.3 Modulation & Resource Mapping
- Converts encoded bits into complex modulation symbols (e.g., QPSK, 16QAM).

- Generates DMRS and maps both data + DMRS into the uplink resource grid.

- Performs IFFT to prepare time-domain OFDM symbols.

```c
nr_modulation(...);
nr_generate_dmrs_pusch(...);
nr_fill_ulsch(...);
```
## 🔧 3.4 Channel Simulation
- Passes modulated signal through a simulated channel (AWGN or fading).

- Adds noise and/or multipath distortion to simulate realistic transmission.

```c
add_awgn_noise(...);
multipath_channel(...);
```
## 🔧 3.5 Receiver Front-End Processing
- Applies FFT on received signal.

- Estimates the channel based on DMRS.

- Equalizes the received symbols.

```c
nr_ulsch_channel_estimation(...);
nr_ulsch_extract_rbs(...);
```
## 🔧 3.6 LLR Computation
- Computes Log-Likelihood Ratios (LLRs) from the received symbols.

- Prepares soft bits as input to LDPC decoder.

```c
nr_ulsch_llr_computation(...);
```
## 🔧 3.7 LDPC Decoding & CRC Checking
- Performs LDPC decoding on LLRs.

- Verifies decoding success via CRC.

```c
ret = nr_ulsch_decoding(...);
if (ret < 0) errors++;
ul_errors++;
ul_total++;
```
## 🔧 3.8 Error Analysis & HARQ Stats
- Updates block error statistics.

- Tracks CRC failures, decoding failures, and false positives.

- Counts iterations and optionally estimates scrambling errors.

```c
if (ret < 0) {...}
if (ulsch->last_iteration_cnt == max_ldpc_iterations) decoder_max_it++;
if (tb_crc_failed) false_positive++;
```
## 🔧 3.9 Performance Logging & Reporting
- Displays and logs simulation metrics: BLER, BER, effective throughput.

- Prints timing breakdown (if enabled).

- Optionally writes results to CSV file.

```c
printf("SNR %f: n_errors (%d/%d)...\n");
if (print_perf) printDistribution(...);
if (csv_fd) fprintf(csv_fd, ...);
```
## 🔧 4. Finalization
- Closes files and releases allocated memory.

- Cleans up channel and LDPC-related structures.

```c
if (csv_fd) fclose(csv_fd);
free_channel_desc(...);
free_gNB_ulsch(...);
return ret;
```
- Ends the simulation loop and returns exit status.
