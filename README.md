# Modular-FP-Processing-Element
RTL Design and Synthesis of a novel modular PE for systolic array and accelerator architectures

## Processing Element (PE) 
## FLOATING POINT

 <p align="center">
  <img width="1400" height="500" src="/Images/MAC.png">
</p>

**I/O Overview**

| Signal         | Width  | Description                           | Signal      | Width | Description                  |
|---------------|--------|---------------------------------------|------------|-------|------------------------------|
| `in`         | 96     | Operand input (A, B, bias)           | `ch_out`   | 32    | Chained output to another PE |
| `clk`        | 1      | System clock                         | `out`      | 32    | Final computed result        |
| `rst`        | 1      | Active-high reset                    | `op_sel`   | 1     | Operation select (MUL, ADD)  |
| `chain_in`   | 1      | Enables input chaining               | `m2`       | 2     | Multiplexer control          |
| `ch_in_ax_fwd` | 1    | Forwarding control for AX            | `chain_out`| 2     | Chaining output selection    |
| `ax_ay_fwd`  | 1      | Forwarding control for AY            | `ch_in`    | 32    | Chained input from PE        |
| `acc`        | 1      | Accumulation enable                  |            |       |                              |

**Operation Overview**

 Operation            | OPSEL | OP_SEL | ACC | AX_AY_FWD | CH_IN_AX_FWD | M2    | CHAIN_IN |
|----------------------|------ |------- |---- |---------- |------------- |------ |--------- |
| Multiply (MULT)      | 0     | 1      | 0   | 1         | 1            | 2'b10 | 0       |
| Addition (ADD)       | 1     | 1      | 0   | 1         | 1            | 2'b10 | 0       |
| Subtraction (SUB)    | 1     | 1      | 0   | 1         | 1            | 2'b10 | 0       |
| Multiply-Accumulate (MAC) | 0 | 1      | 1   | 1         | 1            | 2'b10 | 0       |
| Multiply-Add (MADD)  | 0     | 1      | 0   | 1         | 1            | 2'b01 | 0       |
| Multiply-Subtract (MSUB) | 1 | 1      | 0   | 1         | 1            | 2'b01 | 0       |

| CHAIN_IN | CH_IN_AX_FWD | CHAIN_OUT | Description                           |
|--------- |------------ |---------- |-------------------------------------- |
| 1        | 0          | 00        | Chained Multiply (MULT)               |
| 1        | 0          | 01        | Chained Addition/Subtraction (ADD/SUB) |
| 1        | 0          | 10        | Chained Multiply-Accumulate (MAC)     |

### Overview

- **Comprehensive Operations** – The Processing Element (PE) supports MAC, MULT, ADD/SUB, MADD/MSUB and PE chaining, enabling efficient chianed computations.

- **Dedicated FP32 Fixed Unit** – Unlike virtual logic and hardware sharing, the PE contains a fixed FP32 unit with dedicated adders and multipliers for all supported formats.

- **Unified Arithmetic Design** – The FP32 multiplier (24-bit mantissa MUL + 8-bit CLA for exponent) and corresponding adders are reused for BF16, INT16, and FP16 by appending zeros to match fixed-width processing.

- **Precision-Adaptive Processing** – Input data is zero-extended to FP32 width for computation, but the final result maintains the same precision format as the input (BF16, INT16, or FP16).

- **No Logical PE Mapping** – Unlike architectures with logical PE scaling (e.g., 16-bit → 1 PE, 8-bit → 4 PEs), each physical PE operates independently without virtual partitioning.

- **Efficient Multi-Format Computation** – This design ensures seamless format adaptation while maintaining high compute efficiency for mixed-precision deep learning workloads.

## FIXED POINT

| Signal       | Width             | Description                    | Signal    | Width             | Description                 |
|--------------|-------------------|--------------------------------|-----------|-------------------|-----------------------------|
| `clk`        | 1 bit              | Primary clock signal           | `r1`      | 256 bits [255:0]  | Output register 1           |
| `clk2`       | 1 bit              | Secondary clock signal         | `r2`      | 256 bits [255:0]  | Output register 2           |
| `rst`        | 1 bit              | Asynchronous reset             | `r3`      | 256 bits [255:0]  | Output register 3           |
| `a_data`     | 128 bits [127:0]    | Input data bus A                | `r4`      | 256 bits [255:0]  | Output register 4           |
| `b_data`     | 128 bits [127:0]    | Input data bus B                |           |                   |                             |

## Processing Element (PE) - Floating Point
### RTL Design and Functional Verification

- **SCHEMATIC**
 <p align="center">
  <img width="1200" height="500" src="/Images/SCHEMATIC.png">
</p>

- **MULT AND ADD/SUB**
 <p align="center">
  <img width="1200" height="500" src="/Images/MULT , ADD ( OPSEL = 0 AND OP_SEL = 1 , ACC = 0 , AX_AY_FWD = 1 , CH_IN_AX_FWD = 1 , M2 = 2'b01 , CHAIN_IN = 0 ).png">
</p>

- **MULT-ADD(MADD)/MULT-SUB(MSUB)**
 <p align="center">
  <img width="1200" height="500" src="/Images/MULT - ADD ( OPSEL = 0 AND OP_SEL = 1 , ACC = 0, AX_AY_FWD = 1 , CH_IN_AX_FWD = 1 , M2 = 2'b10 , CHAIN_IN = 0 ).png">
</p>

- **MULT AND ACC (MAC)**
 <p align="center">
  <img width="1200" height="500" src="/Images/MULT AND ACC ( OPSEL = 0 AND OP_SEL = 1 , ACC = 1, AX_AY_FWD = 1 , CH_IN_AX_FWD = 1 , M2 = 2'b10 , CHAIN_IN = 0 ).png">
</p>

### Synthesis and Gate Level Simulation (GLS)

- **BLOCK DIAGRAM**
 <p align="center">
  <img width="1200" height="500" src="/Images/PU BLOCK.png">
</p>

- **PROCESSING ELEMENT NETLIST**
 <p align="center">
  <img width="1200" height="500" src="/Images/PU.png">
</p>

- **CONTROL UNIT NETLIST**
 <p align="center">
  <img width="1200" height="500" src="/Images/CU.png">
</p>


- **GLS (TENTATIVELY DONE IN XCELIUM)**
 <p align="center">
  <img width="1200" height="500" src="/Images/GLS.png">
</p>

- **AREA REPORT**
<p align="center">
  <img width="1200" height="500" src="/Images/AREA.png">
</p>

- **TIMING REPORT**
- The maximum delay of the Processing Element is just 2.45 ns 
 <p align="center">
  <img width="1200" height="500" src="/Images/TIMING.png">
</p>

- **POWER REPORT**
 <p align="center">
  <img width="1200" height="500" src="/Images/POWER.png">
</p>
