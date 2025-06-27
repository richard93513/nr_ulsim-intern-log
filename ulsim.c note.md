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
This section iteratively simulates the full uplink PHY transmission and reception chain. Each trial includes the following sequential steps:

### 3.1 Transport Block Generation
- Determines the transport block (TB) size.

- Randomly generates the input bit sequence.

- Appends a CRC checksum for error detection.

### 3.2 LDPC Encoding & Rate Matching
- Applies LDPC channel coding to the TB.

- Performs rate matching and bit interleaving.

- Prepares the encoded data for modulation.

### 3.3 Modulation & Resource Mapping
- Modulates the encoded bits according to the MCS (QPSK, 16QAM, 64QAM, etc.).

- Maps data and DMRS symbols into the uplink resource grid.

- Performs IFFT to generate time-domain OFDM symbols.

### 3.4 Channel Simulation
- Passes the transmit signal through an AWGN or TDL channel.

- Simulates multipath fading, noise, and Doppler effects.

### 3.5 Receiver Processing
- Applies FFT, channel estimation, and equalization.

- Demodulates symbols and computes LLRs.

- Performs LDPC decoding and CRC checking.

### 3.6 Error Analysis & HARQ Logic
- Updates error statistics based on decoding results.

- Triggers HARQ rounds or retransmissions as needed.

- Collects BLER, throughput, and decoding performance metrics.

## 🔧 3.1 Transport Block Generation
- Calculates the appropriate TB size based on MCS, bandwidth, and other configuration.

- Generates a pseudo-random bit sequence to simulate real uplink data.

- Appends CRC for error detection before encoding.

```c
ulsch->harq_processes[harq_pid]->TBS = nr_compute_tbs(
  pusch_pdu->mcs,
  pusch_pdu->mcs_table,
  pusch_pdu->nb_layers,
  pusch_pdu->rb_size,
  pusch_pdu->nr_of_symbols,
  pusch_pdu->dmrs_symbol_map,
  pusch_pdu->nr_of_dmrs_symbols,
  0,  // tb_scaling
  pusch_pdu->transform_precoding
);
```
- Computes the Transport Block Size (TBS) using 3GPP-defined tables based on modulation scheme, number of layers, and resource allocation.

```c
for (int i = 0; i < TBS_bytes; i++)
  ulsch_input_buffer[i] = (unsigned char)(taus() & 0xff);
```
- Fills the input buffer with random bits using a seeded Tausworthe generator to ensure reproducibility.

```c
ulsch_input_buffer[TBS_bytes] = 0;
ulsch_input_buffer[TBS_bytes+1] = 0;
ulsch_input_buffer[TBS_bytes+2] = 0;
ulsch_input_buffer[TBS_bytes+3] = 0;
```
- Reserves space for the 24-bit CRC at the end of the transport block.

```c
crc = crc24a(ulsch_input_buffer, TBS_bytes);
ulsch_input_buffer[TBS_bytes]     = (uint8_t)((crc>>24)&0xff);
ulsch_input_buffer[TBS_bytes + 1] = (uint8_t)((crc>>16)&0xff);
ulsch_input_buffer[TBS_bytes + 2] = (uint8_t)((crc>>8 )&0xff);
ulsch_input_buffer[TBS_bytes + 3] = (uint8_t)((crc    )&0xff);
```
- Computes and appends the CRC to the transport block to enable error checking at the receiver.

## 🔧 3.2 LDPC Encoding & Rate Matching
- Encodes the Transport Block using LDPC (Low-Density Parity-Check) coding.

- Applies segmentation and filler bit insertion if the TB is too large.

- Performs rate matching to match codeword length with the physical layer budget.

```c
nr_ulsch_encoding(gNB->ulsch[UE_id],
                  &gNB->frame_parms,
                  NULL,
                  &gNB->UL_req,
                  pusch_pdu,
                  ulsch_input_buffer,
                  0, // no SRS
                  0);
```
- Entry point for LDPC encoding process. Takes the prepared TB and frame parameters, and returns a fully encoded and rate-matched codeword.

- The internal flow of nr_ulsch_encoding() includes:

  - CRC segmentation if TB > 8424 bits.

  - LDPC base graph selection based on TB size and code rate.

  - LDPC encoding using parity-check matrices.

  - Filler bit insertion if necessary.

  - Rate matching (puncturing or repetition) to adjust output length.

```c
ulsch->harq_processes[harq_pid]->F = number_of_filler_bits;
```
- Filler bits (F) are set when TBS is not a multiple of the LDPC lifting size. These bits are ignored during decoding.

## 🔧 3.3 Transport Block CRC & LDPC Encoding

- Adds CRC to the Transport Block (TB) for error detection.
- Selects LDPC base graph and encoding parameters based on TB size and MCS.
- Encodes the TB using LDPC encoder and performs rate matching.

```c
nr_generate_dci(...);  // optional for DCI-based setups
crc = crc24a(tinput, tb_size_bits);
nr_segmentation(tinput, ...); // Segments TB if size > 8424 bits
The internal flow of nr_ulsch_encoding() includes:
```

- CRC segmentation if TB > 8424 bits.

- LDPC base graph selection based on TB size and code rate.

- LDPC encoding using parity-check matrices.

- Filler bit insertion if necessary.

- Rate matching (puncturing or repetition) to adjust output length.

```c
nr_ulsch_encoding(...)  // Full LDPC encode + rate matching
```
- Output is a modifiable codeword buffer used for modulation in next step.

## 🔧 3.4 Modulation & DMRS Insertion

- Maps the encoded bits into modulation symbols (QPSK, 16QAM, 64QAM, etc.).
- Inserts Demodulation Reference Signals (DMRS) into the resource grid.
- Prepares full transmission grid combining data and reference symbols.

```c
nr_modulation(codeword, ...);
```
- Converts the codeword into complex modulation symbols based on the modulation order.

```c
nr_generate_dmrs_pusch(...);
```
- Generates DMRS symbols used for channel estimation at the receiver.
```c
nr_fill_ulsch(...)  // Assembles modulated data and DMRS into resource grid
```
- The final output of this stage is a sequence of time-domain samples ready for OFDM processing and transmission.

## 🔧 3.5 Channel Transmission (AWGN/Fading)

- Transmits the modulated signal through a simulated channel.
- Supports different models like AWGN or TDL-A/B/C fading.
- Adds noise or multipath effects to the transmitted signal.

```c
if (channel_model == AWGN) {
  add_awgn_noise(txdata, SNR_dB, ...);
}
```
- Adds Additive White Gaussian Noise (AWGN) based on target SNR.

```c
if (UE2gNB != NULL) {
  multipath_channel(txdata, rxdata, UE2gNB, ...);
}
```
- Applies fading and multipath channel effects using pre-defined models.

- This stage simulates real-world transmission impairments, preparing the signal for receiver processing.

## 🔧 3.6 Receiver Processing and LLR Computation

- The received signal undergoes demodulation and channel estimation.
- Computes Log-Likelihood Ratios (LLRs) for each received bit.
- These LLRs are used in the LDPC decoder.

```c
nr_ulsch_channel_estimation(rxdata, ...);
```
- Estimates the channel from DMRS symbols, needed to equalize received data.

```c
nr_ulsch_extract_rbs(rxdataF, &ulsch_llr, ...);
```
- Extracts resource blocks from the received data for decoding.

```c
nr_ulsch_llr_computation(rxdataF, ulsch_llr, ...);
```
- Performs LLR computation based on modulation type and channel estimate.

- This step is critical for decoding accuracy and ultimately affects BLER performance.

## 🔧 3.7 Decoding & Error Checking

- Applies LDPC decoding to the received LLRs using the base graph and decoding algorithm.
- Checks the integrity of the decoded transport block via CRC verification.
- Determines whether the decoding was successful or needs retransmission.

```c
ret = nr_ulsch_decoding(
  UE,
  SNR,
  gNB_id,
  UE_id,
  harq_pid,
  llr,
  &TBS,
  frame,
  nr_tti_rx,
  0,
  get_Qm_ul(pusch_pdu->mcs),
  N_RB_UL,
  NULL
);
```
- Calls the uplink shared channel (ULSCH) decoding function, which includes:

- LDPC decoder

- CRC checker

- HARQ status update

```c
if (ret < 0) {
  errors++;
}
```
- Increments error count if decoding failed (e.g., CRC not passed).

```c
ul_errors++;
ul_total++;
```
- Updates total error statistics and frame count.

## 🔧 3.8 Error Analysis & HARQ Logic
- Analyzes decoding outcomes and determines retransmission needs.

- Updates counters for errors, false positives, and CRC failures.

```c
if (ret < 0) {
    ul_errors++;
    if (ulsch->max_ldpc_iterations == max_ldpc_iterations)
      crc_errors++;
    else
      decoder_errors++;
}
```
- Checks whether LDPC decoding failed (ret < 0).

- Increments ul_errors.

- Distinguishes between decoding errors (decoder_errors) and final CRC errors (crc_errors).

```c
if (ulsch->last_iteration_cnt == max_ldpc_iterations)
    decoder_max_it++;
```
- Tracks how many decoding attempts reached the maximum number of iterations.

```c
if (tb_crc_failed) {
    false_positive++;
}
```
- Records "false positives" — cases where decoding passed but CRC failed.

```c
if (ul_errors > 0 && ret >= 0)
    errors_scrambling += estimate_scrambling_errors(decoded_bits, original_bits);
```
- Optionally estimates bit errors due to incorrect scrambling.
