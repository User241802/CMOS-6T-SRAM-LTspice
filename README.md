# CMOS 6T SRAM Cell Design in LTSpice

This repository contains a comprehensive implementation of a 6-transistor CMOS Static Random Access Memory (SRAM) cell designed using 180nm TSMC technology in LTSpice. The design includes all essential peripheral circuits including precharge circuitry, sense amplifier, write drivers, and proper control signal timing for reliable memory operation.

## Overview

Static Random Access Memory (SRAM) uses latching circuitry to store each bit of data without requiring constant refresh operations, making it faster and more reliable than Dynamic RAM (DRAM). The 6T SRAM cell consists of two cross-coupled CMOS inverters forming a bistable latch, plus two access transistors that provide controlled access to the stored data.

## Circuit Architecture

### 6T SRAM Cell Core (M1-M6)

The heart of the design consists of six transistors implementing the standard 6T SRAM topology:

**Cross-Coupled Inverters (Storage Elements):**
- **M1, M3**: Pull-down NMOS transistors (W=3μm, L=180nm)
- **M2, M4**: Pull-up PMOS transistors (W=1.5μm, L=180nm)

**Access Transistors:**
- **M5, M6**: NMOS pass gates (W=1.5μm, L=180nm)

### Beta Ratio Design Considerations

The transistor sizing follows critical beta ratio principles to ensure stable operation[12]. The beta ratio (β) is defined as the ratio of pull-down transistor conductance to access transistor conductance[12]:

**β = (W_pulldown / L) / (W_access / L) = 3μm / 1.5μm = 2.0**

This 2:1 ratio ensures adequate read stability while maintaining reasonable write capability[12]. Industry standards typically require beta ratios between 1.8 to 3.0 for reliable operation.

### Precharge Circuitry (M9, M10)

The precharge circuit equalizes bit lines to VDD before each read operation:

**Design Parameters:**
- **M9, M10**: PMOS transistors (W=2μm, L=180nm)
- **Function**: Precharge both BL and BLB to 1.8V (VDD)
- **Control Signal**: PC (Precharge) - Active low

**Critical Design Requirement:** The precharge circuit must maintain bit line voltage differences below 80mV for correct read operation. The 2μm width provides adequate current drive for fast precharge while minimizing area overhead.

### Sense Amplifier (M11-M16)

The differential sense amplifier detects small voltage differences on bit lines and amplifies them to full logic levels:

**Architecture Components:**
- **M11, M13**: NMOS cross-coupled pair (W=3μm, L=180nm)
- **M12, M14**: PMOS cross-coupled pair (W=6μm, L=180nm)  
- **M15**: PMOS enable transistor (W=6μm, L=180nm)
- **M16**: NMOS enable transistor (W=3μm, L=180nm)

**Sizing Rationale:** The 2:1 PMOS to NMOS width ratio compensates for the mobility difference between holes and electrons, ensuring balanced operation. The larger PMOS devices (6μm) provide stronger pull-up capability for fast sensing.

### Write Driver Circuit (M7, M8)

Write drivers force the desired data onto bit lines during write operations:

**Specifications:**
- **M7, M8**: NMOS transistors (W=6μm, L=180nm)
- **Drive Strength**: 4× stronger than access transistors
- **Function**: Overpower cell pull-up transistors during writes

The 6μm width ensures write drivers can reliably overcome the internal cell resistance and establish new data states.

## Bit Line Capacitance Design

### Load Capacitors (C1, C2)

**Value**: 10fF each on BL and BLB
**Justification**: Represents realistic parasitic capacitance from:
- Access transistor drain capacitance
- Metal interconnect capacitance  
- Sense amplifier input capacitance

The 10fF value is typical for single-cell loading in 180nm technology[9]. Larger capacitances would slow operation, while smaller values wouldn't represent realistic conditions.

## Control Signal Timing and Values

### Word Line (WORD)
```
PULSE(0 1.8 1n 100p 100p 2n 5n)
```
- **Amplitude**: 0V to 1.8V (full rail swing)
- **Delay**: 1ns (allows precharge completion)
- **Width**: 2ns (sufficient for read/write operations)
- **Period**: 5ns (200MHz operation frequency)

### Write Enable (WRITE)
```  
PULSE(0 1.8 1n 100p 100p 2n 10n)
```
- **Timing**: Coincides with word line activation
- **Width**: 2ns (matches word line width)
- **Period**: 10ns (allows read cycles between writes)

### Precharge Control (PC)
```
PULSE(1.8 0 3n 100p 100p 1n 10n)
```
- **Active Low**: 0V enables precharge
- **Delay**: 3ns (after write completion)
- **Width**: 1ns (adequate precharge time)

### Sense Amplifier Enable (SE/SE_bar)
```
SE: PULSE(0 1.8 6n 100p 100p 2n 10n)
SE_bar: PULSE(1.8 0 6n 100p 100p 2n 10n)
```
- **Delay**: 6ns (after bit line development)
- **Differential**: Complementary signals ensure proper operation

## Critical Design Trade-offs

### Consequences of Incorrect Sizing

**Undersized Pull-down Transistors (M1, M3):**
- Reduced Static Noise Margin below 300mV
- Read disturb failures during access operations
- Inability to maintain stored data reliably

**Oversized Access Transistors (M5, M6):**
- Write margin degradation below 200mV
- Difficulty changing cell state during writes
- Increased leakage current in standby mode

**Inadequate Write Driver Strength:**
- Write failures due to insufficient drive capability
- Longer write times increasing power consumption
- Reliability issues across process variations

### Timing Violations Impact

**Insufficient Precharge Time:**
- Bit line voltage imbalance exceeding 80mV
- False read data due to improper initial conditions
- Sense amplifier metastability issues

**Premature Sense Amplifier Activation:**
- Incorrect data sensing due to insufficient bit line swing
- Reduced noise margins in read operations
- Potential data corruption in memory arrays

## Technology Specifications

**Process**: 180nm TSMC CMOS technology
**Supply Voltage**: 1.8V nominal
**Minimum Feature Size**: 180nm channel length
**Operating Temperature**: Standard commercial range

## Simulation Results Expected

With proper sizing and timing, the design should demonstrate:
- **Static Noise Margin**: >350mV for excellent stability
- **Write Margin**: >250mV for reliable write operations 
- **Access Time**: <300ps for high-speed operation
- **Power Consumption**: <50μW per cell including peripherals

## Design Verification Checklist

✅ Beta ratio = 2.0 (industry standard)
✅ Precharge transistors sized for <1ns equalization
✅ Sense amplifier balanced for differential operation  
✅ Write drivers 4× stronger than access transistors
✅ Control signals properly sequenced and timed
✅ Load capacitances represent realistic conditions

This comprehensive SRAM design serves as an excellent educational platform for understanding memory circuit design principles while demonstrating industry-standard practices for reliable operation.

