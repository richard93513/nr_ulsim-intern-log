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

### Initialization Flow

- `load_configmodule()`  
  Load runtime configuration from command-line or config file.
- `set_default_frame_parms()`  
  Initialize frame structure (FFT size, subcarrier spacing, slots, etc.).
- `nr_phy_config_request_sim()`  
  Apply transport and physical layer simulation parameters.
- `init_nr_transport()`  
  Initialize LDPC encoding/decoding, rate matching, modulation, etc.

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

## Command Line Options

`nr_ulsim` can be launched with various CLI flags to configure the simulation.

| Option | Description |
|--------|-------------|
| `-s`   | Random seed |
| `-n`   | Number of SNR points |
| `-S`   | SNR start value |
| `-m`   | Modulation (9 = QPSK) |
| `-t`   | Number of trials per SNR |
| `-d`   | Enable debug logs |
| `-f`   | Enable frequency-domain equalizer |

Example:

```bash
./nr_ulsim -s 0 -n 10 -S 0 -m 9 -t 100
