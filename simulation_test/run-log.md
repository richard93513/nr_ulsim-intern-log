richard93513@richard93513-Lenovo-ideapad-700-15ISK:~/openairinterface5g/cmake_targets/ran_build/build$ ./nr_ulsim -s 0 -S 3 -n 100 -m 9 -r 25 -u 1 -I 20 -P
CMDLINE: "./nr_ulsim" "-s" "0" "-S" "3" "-n" "100" "-m" "9" "-r" "25" "-u" "1" "-I" "20" "-P" 
Initializing random number generator, seed 9833574585794245309
handling optarg s
Setting SNR0 to 0.000000
handling optarg S
Setting SNR1 to 3.000000
handling optarg n
handling optarg m
handling optarg r
handling optarg u
handling optarg I
handling optarg P
DL frequency 3619080000: band 48, UL frequency 3619080000
AWGN: ricean_factor 0.000000
[ULSIM]: length_dmrs: 1, l_prime_mask: 1	number_dmrs_symbols: 1, mapping_type: 1 add_pos: 0 
[ULSIM]: CDM groups: 1, dmrs_config_type: 0, num_rbs: 25, nb_symb_sch: 12, start_symbol 0
[ULSIM]: MCS: 9, mod order: 2, code_rate: 6790

[ULSIM]: VALUE OF G: 6900, TBS: 4608
*****************************************
SNR 0.000000: n_errors (100/100,95/100,0/95,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312358/690000,310854/690000,300331/655500,0/0)

SNR 0.000000: Channel BLER (1.000000e+00,9.500000e-01,0.000000e+00,-nan Channel BER (4.526928e-01,4.505130e-01,4.581709e-01,-nan) Avg round 2.95, Eff Rate 1574.4000 bits/slot, Eff Throughput 34.17, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           398.61 us (295 trials)
 Statistics std=182.13, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           22.70 us (295 trials)		(  6.70 total [ms])
    |__ ULSCH channel estimation time       11.27 us (295 trials)		(  3.33 total [ms])
        |__ Antenna Processing time          9.19 us (295 trials)
    |__ RX PUSCH Initialization time         1.31 us (295 trials)		(  0.39 total [ms])
    |__ RX PUSCH Symbol Processing time      9.95 us (295 trials)		(  2.93 total [ms])
|__ ULSCH total decoding time              375.65 us (295 trials)		(110.82 total [ms])

UE TX
|__ PHY_PROC_TX                             88.04 us (295 trials)		( 25.97 total [ms])
|__ PUSCH_PROC_STATS                        37.40 us (295 trials)		( 11.03 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.17 us (295 trials)		(  0.05 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               18.03 us (295 trials)		(  5.32 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                1.55 us (295 trials)		(  0.46 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.47 us (295 trials)		(  0.73 total [ms])
|__ ULSCH_ENCODING_STATS                    26.30 us (295 trials)		(  7.76 total [ms])
|__ OFDM_MOD_STATS                          50.45 us (295 trials)		( 14.88 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 0.200000: n_errors (100/100,54/100,0/54,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312160/690000,310917/690000,170681/372600,0/0)

SNR 0.200000: Channel BLER (1.000000e+00,5.400000e-01,0.000000e+00,-nan Channel BER (4.524058e-01,4.506043e-01,4.580811e-01,-nan) Avg round 2.54, Eff Rate 1889.2800 bits/slot, Eff Throughput 41.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           404.69 us (254 trials)
 Statistics std=157.68, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           22.74 us (254 trials)		(  5.77 total [ms])
    |__ ULSCH channel estimation time       11.27 us (254 trials)		(  2.86 total [ms])
        |__ Antenna Processing time          9.23 us (254 trials)
    |__ RX PUSCH Initialization time         1.25 us (254 trials)		(  0.32 total [ms])
    |__ RX PUSCH Symbol Processing time     10.03 us (254 trials)		(  2.55 total [ms])
|__ ULSCH total decoding time              381.78 us (254 trials)		( 96.97 total [ms])

UE TX
|__ PHY_PROC_TX                             85.36 us (254 trials)		( 21.68 total [ms])
|__ PUSCH_PROC_STATS                        35.14 us (254 trials)		(  8.92 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.15 us (254 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               17.48 us (254 trials)		(  4.44 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                1.12 us (254 trials)		(  0.28 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.32 us (254 trials)		(  0.59 total [ms])
|__ ULSCH_ENCODING_STATS                    24.56 us (254 trials)		(  6.24 total [ms])
|__ OFDM_MOD_STATS                          50.06 us (254 trials)		( 12.72 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 0.400000: n_errors (100/100,9/100,0/9,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312220/690000,310965/690000,28588/62100,0/0)

SNR 0.400000: Channel BLER (1.000000e+00,9.000000e-02,0.000000e+00,-nan Channel BER (4.524928e-01,4.506739e-01,4.603543e-01,-nan) Avg round 2.09, Eff Rate 2234.8800 bits/slot, Eff Throughput 48.50, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           400.74 us (209 trials)
 Statistics std=97.52, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           23.67 us (209 trials)		(  4.95 total [ms])
    |__ ULSCH channel estimation time       11.75 us (209 trials)		(  2.46 total [ms])
        |__ Antenna Processing time          9.39 us (209 trials)
    |__ RX PUSCH Initialization time         1.46 us (209 trials)		(  0.31 total [ms])
    |__ RX PUSCH Symbol Processing time     10.30 us (209 trials)		(  2.15 total [ms])
|__ ULSCH total decoding time              376.89 us (209 trials)		( 78.77 total [ms])

UE TX
|__ PHY_PROC_TX                             87.02 us (209 trials)		( 18.19 total [ms])
|__ PUSCH_PROC_STATS                        35.25 us (209 trials)		(  7.37 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.15 us (209 trials)		(  0.03 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               17.55 us (209 trials)		(  3.67 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.38 us (209 trials)		(  0.08 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.43 us (209 trials)		(  0.51 total [ms])
|__ ULSCH_ENCODING_STATS                    24.02 us (209 trials)		(  5.02 total [ms])
|__ OFDM_MOD_STATS                          51.59 us (209 trials)		( 10.78 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 0.600000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312498/690000,311191/690000,0/0,0/0)

SNR 0.600000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.528957e-01,4.510014e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           409.33 us (200 trials)
 Statistics std=61.61, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           27.79 us (200 trials)		(  5.56 total [ms])
    |__ ULSCH channel estimation time       13.73 us (200 trials)		(  2.75 total [ms])
        |__ Antenna Processing time         10.88 us (200 trials)
    |__ RX PUSCH Initialization time         1.94 us (200 trials)		(  0.39 total [ms])
    |__ RX PUSCH Symbol Processing time     11.84 us (200 trials)		(  2.37 total [ms])
|__ ULSCH total decoding time              381.26 us (200 trials)		( 76.25 total [ms])

UE TX
|__ PHY_PROC_TX                             97.19 us (200 trials)		( 19.44 total [ms])
|__ PUSCH_PROC_STATS                        39.77 us (200 trials)		(  7.95 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.21 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               19.29 us (200 trials)		(  3.86 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.28 us (200 trials)		(  0.06 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.69 us (200 trials)		(  0.54 total [ms])
|__ ULSCH_ENCODING_STATS                    27.00 us (200 trials)		(  5.40 total [ms])
|__ OFDM_MOD_STATS                          57.12 us (200 trials)		( 11.42 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 0.800000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312645/690000,310842/690000,0/0,0/0)

SNR 0.800000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.531087e-01,4.504957e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           368.71 us (200 trials)
 Statistics std=48.53, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           26.68 us (200 trials)		(  5.34 total [ms])
    |__ ULSCH channel estimation time       13.26 us (200 trials)		(  2.65 total [ms])
        |__ Antenna Processing time         10.63 us (200 trials)
    |__ RX PUSCH Initialization time         1.83 us (200 trials)		(  0.37 total [ms])
    |__ RX PUSCH Symbol Processing time     11.36 us (200 trials)		(  2.27 total [ms])
|__ ULSCH total decoding time              341.74 us (200 trials)		( 68.35 total [ms])

UE TX
|__ PHY_PROC_TX                             96.07 us (200 trials)		( 19.21 total [ms])
|__ PUSCH_PROC_STATS                        39.02 us (200 trials)		(  7.80 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.19 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               19.20 us (200 trials)		(  3.84 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.25 us (200 trials)		(  0.05 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.63 us (200 trials)		(  0.53 total [ms])
|__ ULSCH_ENCODING_STATS                    26.67 us (200 trials)		(  5.33 total [ms])
|__ OFDM_MOD_STATS                          56.76 us (200 trials)		( 11.35 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 1.000000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312222/690000,311054/690000,0/0,0/0)

SNR 1.000000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.524957e-01,4.508029e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           412.09 us (200 trials)
 Statistics std=122.62, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           34.78 us (200 trials)		(  6.96 total [ms])
    |__ ULSCH channel estimation time       17.78 us (200 trials)		(  3.56 total [ms])
        |__ Antenna Processing time         13.90 us (200 trials)
    |__ RX PUSCH Initialization time         2.71 us (200 trials)		(  0.54 total [ms])
    |__ RX PUSCH Symbol Processing time     13.81 us (200 trials)		(  2.76 total [ms])
|__ ULSCH total decoding time              376.97 us (200 trials)		( 75.39 total [ms])

UE TX
|__ PHY_PROC_TX                            113.19 us (200 trials)		( 22.64 total [ms])
|__ PUSCH_PROC_STATS                        47.63 us (200 trials)		(  9.53 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.34 us (200 trials)		(  0.07 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               22.99 us (200 trials)		(  4.60 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.40 us (200 trials)		(  0.08 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.93 us (200 trials)		(  0.59 total [ms])
|__ ULSCH_ENCODING_STATS                    32.55 us (200 trials)		(  6.51 total [ms])
|__ OFDM_MOD_STATS                          65.04 us (200 trials)		( 13.01 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 1.200000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312354/690000,311866/690000,0/0,0/0)

SNR 1.200000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.526870e-01,4.519797e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           384.78 us (200 trials)
 Statistics std=77.36, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           32.28 us (200 trials)		(  6.46 total [ms])
    |__ ULSCH channel estimation time       16.10 us (200 trials)		(  3.22 total [ms])
        |__ Antenna Processing time         12.52 us (200 trials)
    |__ RX PUSCH Initialization time         2.65 us (200 trials)		(  0.53 total [ms])
    |__ RX PUSCH Symbol Processing time     13.11 us (200 trials)		(  2.62 total [ms])
|__ ULSCH total decoding time              352.20 us (200 trials)		( 70.44 total [ms])

UE TX
|__ PHY_PROC_TX                            108.72 us (200 trials)		( 21.74 total [ms])
|__ PUSCH_PROC_STATS                        45.19 us (200 trials)		(  9.04 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.29 us (200 trials)		(  0.06 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               21.74 us (200 trials)		(  4.35 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.36 us (200 trials)		(  0.07 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.91 us (200 trials)		(  0.58 total [ms])
|__ ULSCH_ENCODING_STATS                    30.64 us (200 trials)		(  6.13 total [ms])
|__ OFDM_MOD_STATS                          63.17 us (200 trials)		( 12.63 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 1.400000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312097/690000,311136/690000,0/0,0/0)

SNR 1.400000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.523145e-01,4.509217e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           323.40 us (200 trials)
 Statistics std=75.64, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           26.52 us (200 trials)		(  5.30 total [ms])
    |__ ULSCH channel estimation time       13.12 us (200 trials)		(  2.62 total [ms])
        |__ Antenna Processing time         10.47 us (200 trials)
    |__ RX PUSCH Initialization time         1.79 us (200 trials)		(  0.36 total [ms])
    |__ RX PUSCH Symbol Processing time     11.36 us (200 trials)		(  2.27 total [ms])
|__ ULSCH total decoding time              296.65 us (200 trials)		( 59.33 total [ms])

UE TX
|__ PHY_PROC_TX                             93.12 us (200 trials)		( 18.62 total [ms])
|__ PUSCH_PROC_STATS                        37.83 us (200 trials)		(  7.57 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.20 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               18.58 us (200 trials)		(  3.72 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.25 us (200 trials)		(  0.05 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.55 us (200 trials)		(  0.51 total [ms])
|__ ULSCH_ENCODING_STATS                    25.73 us (200 trials)		(  5.15 total [ms])
|__ OFDM_MOD_STATS                          55.04 us (200 trials)		( 11.01 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 1.600000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312396/690000,311205/690000,0/0,0/0)

SNR 1.600000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.527478e-01,4.510217e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           323.32 us (200 trials)
 Statistics std=109.51, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           26.57 us (200 trials)		(  5.31 total [ms])
    |__ ULSCH channel estimation time       13.38 us (200 trials)		(  2.68 total [ms])
        |__ Antenna Processing time         10.62 us (200 trials)
    |__ RX PUSCH Initialization time         1.71 us (200 trials)		(  0.34 total [ms])
    |__ RX PUSCH Symbol Processing time     11.21 us (200 trials)		(  2.24 total [ms])
|__ ULSCH total decoding time              296.51 us (200 trials)		( 59.30 total [ms])

UE TX
|__ PHY_PROC_TX                             95.41 us (200 trials)		( 19.08 total [ms])
|__ PUSCH_PROC_STATS                        39.43 us (200 trials)		(  7.89 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.22 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               19.18 us (200 trials)		(  3.84 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.32 us (200 trials)		(  0.06 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.62 us (200 trials)		(  0.52 total [ms])
|__ ULSCH_ENCODING_STATS                    26.76 us (200 trials)		(  5.35 total [ms])
|__ OFDM_MOD_STATS                          55.62 us (200 trials)		( 11.12 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 1.800000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312321/690000,311186/690000,0/0,0/0)

SNR 1.800000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.526391e-01,4.509942e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           289.17 us (200 trials)
 Statistics std=85.48, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           23.64 us (200 trials)		(  4.73 total [ms])
    |__ ULSCH channel estimation time       11.71 us (200 trials)		(  2.34 total [ms])
        |__ Antenna Processing time          9.31 us (200 trials)
    |__ RX PUSCH Initialization time         1.48 us (200 trials)		(  0.30 total [ms])
    |__ RX PUSCH Symbol Processing time     10.24 us (200 trials)		(  2.05 total [ms])
|__ ULSCH total decoding time              265.35 us (200 trials)		( 53.07 total [ms])

UE TX
|__ PHY_PROC_TX                             87.46 us (200 trials)		( 17.49 total [ms])
|__ PUSCH_PROC_STATS                        36.16 us (200 trials)		(  7.23 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.17 us (200 trials)		(  0.03 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               17.88 us (200 trials)		(  3.58 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.23 us (200 trials)		(  0.05 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.53 us (200 trials)		(  0.51 total [ms])
|__ ULSCH_ENCODING_STATS                    24.65 us (200 trials)		(  4.93 total [ms])
|__ OFDM_MOD_STATS                          51.01 us (200 trials)		( 10.20 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 2.000000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312100/690000,310987/690000,0/0,0/0)

SNR 2.000000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.523188e-01,4.507058e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           315.44 us (200 trials)
 Statistics std=102.30, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           27.67 us (200 trials)		(  5.53 total [ms])
    |__ ULSCH channel estimation time       13.72 us (200 trials)		(  2.74 total [ms])
        |__ Antenna Processing time         10.86 us (200 trials)
    |__ RX PUSCH Initialization time         2.01 us (200 trials)		(  0.40 total [ms])
    |__ RX PUSCH Symbol Processing time     11.68 us (200 trials)		(  2.34 total [ms])
|__ ULSCH total decoding time              287.54 us (200 trials)		( 57.51 total [ms])

UE TX
|__ PHY_PROC_TX                             95.68 us (200 trials)		( 19.14 total [ms])
|__ PUSCH_PROC_STATS                        39.19 us (200 trials)		(  7.84 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.25 us (200 trials)		(  0.05 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               19.04 us (200 trials)		(  3.81 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.26 us (200 trials)		(  0.05 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.64 us (200 trials)		(  0.53 total [ms])
|__ ULSCH_ENCODING_STATS                    26.45 us (200 trials)		(  5.29 total [ms])
|__ OFDM_MOD_STATS                          56.22 us (200 trials)		( 11.24 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 2.200000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (311960/690000,311144/690000,0/0,0/0)

SNR 2.200000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.521159e-01,4.509333e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min -1, max 0, average 92233720368547760.0
*****************************************

gNB RX
Total PHY proc rx                           284.99 us (200 trials)
 Statistics std=100.70, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           24.32 us (200 trials)		(  4.86 total [ms])
    |__ ULSCH channel estimation time       12.22 us (200 trials)		(  2.44 total [ms])
        |__ Antenna Processing time          9.88 us (200 trials)
    |__ RX PUSCH Initialization time         1.44 us (200 trials)		(  0.29 total [ms])
    |__ RX PUSCH Symbol Processing time     10.48 us (200 trials)		(  2.10 total [ms])
|__ ULSCH total decoding time              260.49 us (200 trials)		( 52.10 total [ms])

UE TX
|__ PHY_PROC_TX                             87.88 us (200 trials)		( 17.58 total [ms])
|__ PUSCH_PROC_STATS                        35.39 us (200 trials)		(  7.08 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.15 us (200 trials)		(  0.03 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               17.47 us (200 trials)		(  3.49 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.21 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.50 us (200 trials)		(  0.50 total [ms])
|__ ULSCH_ENCODING_STATS                    24.00 us (200 trials)		(  4.80 total [ms])
|__ OFDM_MOD_STATS                          52.30 us (200 trials)		( 10.46 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 2.400000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312482/690000,310871/690000,0/0,0/0)

SNR 2.400000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.528725e-01,4.505377e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           292.62 us (200 trials)
 Statistics std=102.08, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           25.46 us (200 trials)		(  5.09 total [ms])
    |__ ULSCH channel estimation time       12.53 us (200 trials)		(  2.51 total [ms])
        |__ Antenna Processing time          9.95 us (200 trials)
    |__ RX PUSCH Initialization time         1.66 us (200 trials)		(  0.33 total [ms])
    |__ RX PUSCH Symbol Processing time     11.06 us (200 trials)		(  2.21 total [ms])
|__ ULSCH total decoding time              266.95 us (200 trials)		( 53.39 total [ms])

UE TX
|__ PHY_PROC_TX                             91.32 us (200 trials)		( 18.26 total [ms])
|__ PUSCH_PROC_STATS                        36.59 us (200 trials)		(  7.32 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.18 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               17.81 us (200 trials)		(  3.56 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.22 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.67 us (200 trials)		(  0.53 total [ms])
|__ ULSCH_ENCODING_STATS                    24.89 us (200 trials)		(  4.98 total [ms])
|__ OFDM_MOD_STATS                          54.52 us (200 trials)		( 10.90 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 2.600000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312297/690000,310376/690000,0/0,0/0)

SNR 2.600000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.526043e-01,4.498203e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           284.93 us (200 trials)
 Statistics std=115.54, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           25.68 us (200 trials)		(  5.14 total [ms])
    |__ ULSCH channel estimation time       12.73 us (200 trials)		(  2.55 total [ms])
        |__ Antenna Processing time         10.17 us (200 trials)
    |__ RX PUSCH Initialization time         1.54 us (200 trials)		(  0.31 total [ms])
    |__ RX PUSCH Symbol Processing time     11.18 us (200 trials)		(  2.24 total [ms])
|__ ULSCH total decoding time              259.03 us (200 trials)		( 51.81 total [ms])

UE TX
|__ PHY_PROC_TX                             89.69 us (200 trials)		( 17.94 total [ms])
|__ PUSCH_PROC_STATS                        36.48 us (200 trials)		(  7.30 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.19 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               17.82 us (200 trials)		(  3.56 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.25 us (200 trials)		(  0.05 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.60 us (200 trials)		(  0.52 total [ms])
|__ ULSCH_ENCODING_STATS                    24.97 us (200 trials)		(  4.99 total [ms])
|__ OFDM_MOD_STATS                          52.97 us (200 trials)		( 10.59 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)

*****************************************
SNR 2.800000: n_errors (100/100,0/100,0/0,0/0) (negative CRC), false_positive 0/100, errors_scrambling (312223/690000,310915/690000,0/0,0/0)

SNR 2.800000: Channel BLER (1.000000e+00,0.000000e+00,-nan,-nan Channel BER (4.524971e-01,4.506014e-01,-nan,-nan) Avg round 2.00, Eff Rate 2304.0000 bits/slot, Eff Throughput 50.00, TBS 4608 bits/slot
DMRS-PUSCH delay estimation: min 0, max 0, average 0.0
*****************************************

gNB RX
Total PHY proc rx                           271.85 us (200 trials)
 Statistics std=101.63, median=0.00, q1=0.00, q3=0.00 µs (on 0 trials)
|__ RX PUSCH time                           24.22 us (200 trials)		(  4.84 total [ms])
    |__ ULSCH channel estimation time       11.97 us (200 trials)		(  2.39 total [ms])
        |__ Antenna Processing time          9.47 us (200 trials)
    |__ RX PUSCH Initialization time         1.32 us (200 trials)		(  0.26 total [ms])
    |__ RX PUSCH Symbol Processing time     10.76 us (200 trials)		(  2.15 total [ms])
|__ ULSCH total decoding time              247.43 us (200 trials)		( 49.49 total [ms])

UE TX
|__ PHY_PROC_TX                             89.82 us (200 trials)		( 17.97 total [ms])
|__ PUSCH_PROC_STATS                        36.59 us (200 trials)		(  7.32 total [ms])
|__ ULSCH_SEGMENTATION_STATS                 0.19 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_LDPC_ENCODING_STATS               18.46 us (200 trials)		(  3.69 total [ms])
|__ ULSCH_RATE_MATCHING_STATS                0.22 us (200 trials)		(  0.04 total [ms])
|__ ULSCH_INTERLEAVING_STATS                 2.58 us (200 trials)		(  0.52 total [ms])
|__ ULSCH_ENCODING_STATS                    25.19 us (200 trials)		(  5.04 total [ms])
|__ OFDM_MOD_STATS                          53.01 us (200 trials)		( 10.60 total [ms])
|__ RX SRS time                              0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Generate SRS sequence time           0.00 us (  0 trials)		(  0.00 total [ms])
    |__ Get SRS signal time                  0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS channel estimation time          0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS timing advance estimation time   0.00 us (  0 trials)		(  0.00 total [ms])
    |__ SRS report TLV build time            0.00 us (  0 trials)		(  0.00 total [ms])
        |__ SRS beam report build time       0.00 us (  0 trials)
        |__ SRS IQ matrix build time         0.00 us (  0 trials)


Num RB:	25
Num symbols:	12
MCS:	9
DMRS config type:	0
DMRS add pos:	0
PUSCH mapping type:	1
DMRS length:	1
DMRS CDM gr w/o data:	1
