# LDPC Decoder Core
## nrLDPC_decoder.c

---

## File Path
** openair1/PHY/CODING/nrLDPC_decoder/nrLDPC_decoder.c**
Implements the core 5G LDPC decoder logic. Performs iterative decoding of LDPC codewords using belief propagation with optional AVX acceleration.

---
## Overview

The LDPC decoder in OpenAirInterface (OAI) executes soft-input soft-output (SISO) decoding for NR LDPC codes. It uses a layered belief-propagation approach across check nodes (CN) and bit nodes (BN), with options for optimized AVX vector implementations for performance.
Function of interest: nrLDPC_decoder_core(...)
This function is responsible for:

- Loading decoding LUTs (lookup tables)

- Iterative message passing between CNs and BNs

- Handling decoding termination via CRC or parity-check

- Outputting LLR values or hard bits

## 📈 LDPC Decoder Core Flow

graph TD
    A[Start Decoder] --> B[Load Parameters / Buffers]
    B --> C[Initial BN Processing (BN->CN)]
    C --> D[First CN Processing (CN->BN)]
    D --> E[First Parity Check or skip CRC]
    E --> F{Passed?}
    F -- No --> G[Iterative Decoding Loop]
    G --> H[CN Processing]
    H --> I[CN->BN Message]
    I --> J[BN Processing]
    J --> E
    F -- Yes --> K[Output LLR or Bits]
    K --> L[End]

## 🔍 Module Descriptions

1. Decoder Setup

- Entry via nrLDPC_decoder_core(...)

- Loads lookup tables (LUTs) for decoding

- Initializes required buffers for CN, BN, and LLRs

- Handles alignment and SIMD preparation

2. First Pass Processing

- BN Pre-processing with nrLDPC_bnProcPc_* (rate & BG-specific)

- CN Pre-processing via nrLDPC_bn2cnProcBuf_*

- First CN processing: nrLDPC_cnProc_*

- Back-propagate to BN via nrLDPC_cn2bnProcBuf_*

At this point, first pass is complete, parity check not applied yet because early iterations may contain unreliable values.

3. Iterative Decoding Loop

Executed while:

- numIter < numMaxIter

- Parity check (or CRC) failed

Steps per iteration:

- CN processing (rate & BG specific)

- CN to BN message transfer

- BN processing (soft LLR update)

- BN to CN message preparation

- Parity Check (or CRC check if enabled)

Each module can be AVX2/AVX512 optimized or fall back to 128-bit baseline implementations.

4. Early Termination Conditions

- If check_crc is enabled, decoder verifies CRC on hard-decoded bits

- If CRC passes before numMaxIter, exits early

- If no CRC, parity-check is evaluated at every step

5. Output Formatting

At end of decoding:

- Outputs raw LLR or hard bits

- Output mode: LLRINT8, BIT, or BITINT8

- Uses:

  - nrLDPC_llrRes2llrOut()

  - nrLDPC_llr2bit() / nrLDPC_llr2bitPacked()

## 🛠️ SIMD Optimizations

Many functions have conditional compilation:

- __AVX2__, __AVX512BW__

- Separate routines per BaseGraph (BG1/BG2) and code rate (R13/R23/...)

- Examples: nrLDPC_bnProc_BG1_R13_AVX2, nrLDPC_cnProc_BG2_R15_128

Fallback versions: *_128 always exist

## 📊 Performance Profiling (Optional)

If compiled with NR_LDPC_PROFILER_DETAIL, profiling is enabled:

- Measures timing for:

  - bnProcPc, bnProc, cnProc, cn2bnProcBuf, bn2cnProcBuf, llr2bit, etc.

- Results used to benchmark and optimize decoding time

## 🧪 Debugging Support (Optional)

Enabled via NR_LDPC_DEBUG_MODE:

- Dumps buffer content to files at each stage:

  - LLR results

  - CN, BN messages

  - Intermediate decoded bits

- Useful for visualization or unit testing

## ✅ Output: Final Decoder Result

The decoder returns:

- numIter: number of iterations performed

- p_out: decoded bits or LLRs, depending on outMode

## Related Files

- nrLDPC_decoder.c – Core decoder

- nrLDPC_cnProc.c – Check node processing

- nrLDPC_bnProc.c – Bit node processing

- nrLDPC_decoder.h – Function declarations

## 💡 Notes

- The decoder is modularized with heavy optimization for high-throughput systems

- Tightly integrated with LDPC test harness: ldpctest.c

- Can be tested via nr_ulsim or unit test
