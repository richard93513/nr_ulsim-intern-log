# 🛰️ `nr_ulsim` Uplink Simulation CLI Reference

`nr_ulsim` is the uplink simulation tool provided by the OpenAirInterface (OAI) 5G NR stack. It simulates the PUSCH transmission chain, allowing you to evaluate BLER, throughput, channel models, and LDPC decoding performance under various configurations.

---

## 🔧 Common Command-Line Parameters

| Option | Description | Example |
|--------|-------------|---------|
| `-s`   | Starting SNR value in dB | `-s 0` |
| `-S`   | Ending SNR value in dB (defaults to SNR0 + 10 if not specified) | `-S 10` |
| `-n`   | Number of trials per SNR point | `-n 100` |
| `-m`   | MCS index (defines modulation and coding rate) | `-m 9` (QPSK), `-m 10` (16QAM) |
| `-r`   | Number of Resource Blocks allocated for PUSCH | `-r 25` |
| `-u`   | Numerology (subcarrier spacing): μ = 0 → 15kHz, μ = 1 → 30kHz | `-u 1` |
| `-v`   | Maximum number of HARQ retransmission rounds | `-v 4` |
| `-I`   | Maximum number of LDPC decoder iterations | `-I 20` |
| `-P`   | Print simulation performance (BLER, throughput, etc.) | `-P` |
| `-g`   | Channel model: format = `Model,Correlation,Doppler` | `-g A,l,10` = TDLA, low correlation, 10 Hz |
| `-y`   | Number of UE TX antennas | `-y 1` |
| `-z`   | Number of gNB RX antennas | `-z 1` |
| `-X`   | Output CSV file for simulation statistics | `-X output.csv` |
| `-L`   | Log level: `0` = errors, `1` = warnings, ..., `4` = full trace | `-L 2` |
| `-T`   | Enable PTRS: format `L,K` (PTRS config) | `-T 0,2` |
| `-U`   | Configure DMRS: format `Mapping,AddPos,Type,CDMGroup` | `-U 0,2,1,2` |

---

## 🧪 Simulation Examples

###  Example 1: Basic SNR Sweep with QPSK

```bash
./nr_ulsim -s 0 -S 10 -n 100 -m 9 -r 25 -u 1 -I 20 -P

###  Example 2: Log to CSV with 16QAM

```bash
./nr_ulsim -s 0 -S 6 -n 200 -m 10 -r 50 -P -X uplink_qam16.csv

###  Example 3: Realistic Channel Model

```bash
./nr_ulsim -s 0 -S 8 -n 150 -m 10 -r 51 -u 1 -g B,m,70 -y 1 -z 2 -P
