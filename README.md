# Title
Design and Implementation of a 16-Byte SRAM in 0.18μm CMOS Technology for low power IoT application.

# What is SRAM ?
- SRAM, which stands for Static Random-Access Memory, is a type of computer memory that is used for storing data that needs to be accessed quickly and frequently by a computer's central processing unit (CPU).  SRAM is one of the two primary types of volatile memory used in computers, with the other being DRAM (Dynamic Random-Access Memory).
- One of the key characteristics of SRAM is its stability. Once data is written into an SRAM cell, it remains there until it's explicitly overwritten. This is in contrast to DRAM, which requires periodic refreshing to maintain data integrity.
- SRAM is very fast because it doesn't require the charge/discharge cycle that DRAM needs. Data can be read or written to SRAM cells with minimal delay, making it suitable for use as cache memory in CPUs.

## Table of Contents
- [Introduction](#introduction)
- [Flowchart](#flowchart)
- [Design Description](#design-description)
   - [6-Transistor (6T) CMOS SRAM Cell](#6-transistor-6t-cmos-sram-cell)
   - [Precharge Circuit](#precharge-circuit)
   - [Row Decoder](##row-decoder)
   - [Sense Amplifier](#sense-amplifier)
   - [Data driver](#data-driver)
- [Working of SRAM](#working-of-sram)
- [Static Noise Margin](#static-noise-margin)

## Introduction
The primary objective of this project is to design a 16-byte SRAM with optimal performance and minimal power consumption in a 0.18μm CMOS process.
- Cell Design: Design of SRAM memory cells that offer fast access times and ensuring high-speed data retrieval while minimizing power consumption.
- Wordline and Bitline Design: Design of wordline and bitline circuit that facilitate fast data read and write operations, minimizing access time and maximizing data throughput.
- Sense Amplifier: Implementation of differential based sense amplifiers for accurate data sensing and amplification.

### Tools used :
The design process will involve extensive use of Electronic Design Automation (EDA) tools for schematic, simulation, and layout design.
- Schematic - Cadence Virtuoso Schematic Editor
- Simulation - Cadence Spectre Simulator[https://en.wikipedia.org/wiki/Spectre_Circuit_Simulator]
- Layout - Cadence Virtuoso Layout Suite

- Each SRAM cell consists of two cross-coupled inverters (also known as a bistable circuit) and additional access transistors for read and write operations. These inverters form a latch, which can hold data in a stable state without the need for periodic refreshing (unlike DRAM).
- SRAM cells are organized into rows and columns. Each row has a wordline that controls access to the cells in that row, and each column has a pair of bitlines that connect to the SRAM cells
## Flowchart
## Design Description
the complete SRAM design contains a 6-transistor (6T) SRAM cell, a pre-charge circuit, a row decoder, control logic, a sense amplifier and a data driver. This section will discuss the design of the blocks and their simulation results.

### 1. 6-Transistor (6T) CMOS SRAM Cell
The core of the SRAM is a memory cell that stores one bit of information. Each cell’s area and power are critical since it decides the area and power of the entire chip. This design uses a standard 6T SRAM cell [1][2]. In principle, it is a back-to-back inverter (M1, M2, M5, M6) to store data indefinitely if power is provided to the cell. The access transistors (M3, M4) are used to read and write data into the cell. The sizing of the devices is decided by three main factors: the area of the cell, stored data in the memory cells is not corrupted while reading it, and able to overwrite the stored data during write operation. Analysis with the appropriate large-signal equations (saturation/linear) [1], it can be shown that M3 needs to be stronger than M5 and, M1 needs to be stronger than M3. Since the structure is symmetric, the same constraints apply for M6, M4, M2. Since these constraints are unbounded, additional constraints are needed to choose the size of transistors in the 6T cell which is typically a trade-off between area and speed. In this design, the 6T transistors were sized for minimum area.

### 2. Precharge Circuit
Since the output bitlines (bl and blb) of each 6T cell are shared by all the cells in the rows in that column, the parasitic capacitance on those nodes is very large making it impractical for the 6T cells to drive the bitlines to full CMOS voltage levels. Instead, both the bitlines are pre-charged to the same voltage, and a differential amplifier is used to sense the difference between the bitlines to read it. The nodes are also pre-charged before a write operation to reset a previous operation. As shown in Fig. 6, NMOS M7 and M8 are used to pre-charge the bitlines to VDD-VTN. Since the sensing mechanism is a differential operation, it is critical for both the bitlines to be equal in voltage for which PMOS M_9 is used. It should be noted that the bitlines are pre-charged to VDD-VTN instead of VDD. It is done to keep the differential sense amplifier active during the pre-charge phase.

### 3. Row Decoder
shows a pass-transistor logic based 4:16 row decoder to select any row from the sixteen rows in the SRAM array based on the input address bit configurations. The 4-bit address signals a[3:0] are used to activate the transistors in such a way that, any one of the outputs will be high. For example, if all the address bits are low (0000), then wl[0] output will be high and this will select the 0th row in the SRAM array. Similarly, if all the address bits are high (1111), wl[15] output line will be high and that will select the 15th row of the SRAM array. Since the pass-transistor logic only passes high (VDD), a pull-down is required at the output.

### 4. Sense Amplifier
The sense amplifier is used to sense the voltage difference between the bitlines and amplify it to drive the digital circuits. There are different types of sense amplifiers are present [5,6] and used in the SRAM design depending upon the application which is typically a trade-off between area and power. In this work, an analog differential amplifier-based sense amplifier is implemented. Fig. 9 shows the differential amplifier-based sense amplifier. As shown in the figure, during the read operation a small voltage difference between the bitlines ‘bl’ and ‘blb’ is amplified by the sense amplifier and the buffer converts the output to rail-to-rail CMOS voltage. The gain of the amplifier and the threshold of the buffer is designed in tandem to achieve that function.

## Working of SRAM
## Static Noise Margin
