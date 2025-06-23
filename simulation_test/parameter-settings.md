# Uplink Simulation Parameter Settings

Below are commonly used CLI flags for `nr_ulsim` and their meanings.

| Parameter | Meaning |
|----------|---------|
| `-s`     | Random seed |
| `-n`     | Number of SNR points (sweep count) |
| `-S`     | Starting SNR (in dB) |
| `-m`     | Modulation (e.g., 9 = QPSK, 10 = 16QAM) |
| `-t`     | Trials per SNR point |
| `-f`     | Enable frequency-domain equalizer |
| `-d`     | Enable debug logs |
| `-R`     | Redundant/override MCS (if needed) |

### Example

```bash
./nr_ulsim -s 0 -n 10 -S 0 -m 9 -t 100 -f
