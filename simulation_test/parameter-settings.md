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
```
Simulation Result 

```bash
*****************************************
SNR 1.000000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (305821/690000,311167/690000,0/0,0/0)

SNR 1.000000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.432188e-01,4.509667e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           316.78 us (200 trials)
 Statistics std=54.87, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           21.63 us (200 trials)		(  4.33 total [ms])
    |__ ULSCH channel estimation time       10.96 us (200 trials)		(  2.19 total [ms])
        |__ Antenna Processing time          8.84 us (200 trials)
    |__ RX PUSCH Initialization time         1.08 us (200 trials)		(  0.22 total [ms])
    |__ RX PUSCH Symbol Processing time      9.46 us (200 trials)		(  1.89 total [ms])
|__ ULSCH total decoding time              295.00 us (200 trials)		( 59.00 total [ms])

UE TX
|__ PHY_PROC_TX                             83.42 us (200 trials)		( 16.69 total [ms])
|__ PUSCH_PROC_STATS                        32.73 us (200 trials)		(  6.55 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.12 us (200 trials)		(  0.02 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               16.68 us (200 trials)		(  3.34 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.19 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.24 us (200 trials)		(  0.45 total [ms])
|__ ULSCH_ENCODING_STATS                    22.73 us (200 trials)		(  4.55 total [ms])
|__ OFDM_MOD_STATS                          50.60 us (200 trials)		( 10.12 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)


*****************************************
```
###  Example 2: Log to CSV with 16QAM

```bash
./nr_ulsim -s 0 -S 6 -n 200 -m 10 -r 50 -P -X uplink_qam16.csv
```
###  Example 3: Realistic Channel Model

```bash
./nr_ulsim -s 0 -S 8 -n 150 -m 10 -r 51 -u 1 -g B,m,70 -y 1 -z 2 -P
```
