# README

## Problem
The mismatch between memory access speed and processor computation speed.

## Task
Implementation of a TLB that stores the translation of a virtual address to a physical address.

## TLB Architectures

| Type | Description |
| ---- | ----------- |
| 1    | Single shared TLB: A single TLB for both instruction and data caches. |
| 2    | Independent instruction and data TLBs: Separate TLBs for instructions and data. |
| 3    | Two-level TLB architecture: A small micro-TLB is supported by a larger TLB. |

## Influence of Cache Size on TLB Performance

| Case           | Description |
| -------------- | ----------- |
| Small Caches   | More TLB misses due to limited page storage, which increases error rates. |
| Large Caches   | Fewer TLB misses thanks to larger page storage, but longer access times due to higher retrieval costs. |

## Methodology and Implementation

### Abstraction Level

- No CPU modules and cache level.
- The TLB is directly connected to the memory.
- Little Endian: Used by most modern processors.
- Byte-addressable memory.
- Hit latency and miss latency: Simulating the interaction between memory and TLB through `wait(latency)` + `wait(SC_ZERO_TIME)` for synchronization.
- The TLB is simulated as a map, providing constant time complexity of O(1) for searches, insertions, and deletions. The TLB stores only non-empty sets.
- `set_index = virt_block % TLB size` with `virtual_address = virt_block | Offset_block` and `virt_block = Tag | Set_Index`.
- Virtual address = physical address + offset with `Offset = Blocksize * v2b-block-offset`.
- Optimization: Offset = a * x with x = 2^optim.

### Theoretical Part

- **primitiveGates = 5747 + TLB size * Block size * 32**
  - Bit: 4 gates
  - Adder/Subtractor: 150 + 32 XOR gates
  - Multiplier: 31 full adders: 4650 gates
  - Divider: Restoring Division Algorithm: 915 gates
  - Shifts: About 3 gates per bit
- This analysis aims to determine the necessary hardware components and gate requirements for effectively implementing the TLB in a hardware architecture.


### Default Values

- Cycles = 1000
- TLB size = 16
- TLB latency = 1
- Block size = 32
- v2b_block_offset = 1
- Memory latency = 50

## Objective of Our Tests

Evaluate the impact of TLB size on performance. Simulate a realistic usage scenario.

## Test Procedure

- **Access sequence:** Read accesses to addresses (in the CSV file).
- **Measurement strategy:** Various memory accesses where some addresses are reused through previous write operations. Simulation with different TLB sizes; recording of cycles, hits, and misses.

## Analysis of the Results

| Size | Misses | Hits | Cycles | PrimitiveGates |
| ---- | ------ | ---- | ------ | -------------- |
| 4    | 24     | 0    | 2380   | 7888           |
| 8    | 20     | 4    | 1028   | 9936           |
| 16   | 12     | 12   | 636    | 14032          |

## Interpretation of the Results

The TLB has a positive impact on runtime: the more hits, the better. This confirms the theoretical part:
- Small TLB size: More cycles + fewer gates
- Large TLB size: Fewer cycles + more gates
