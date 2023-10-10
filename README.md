# Title
Design and Implementation of a 16-Byte SRAM in 0.18μm CMOS Technology for low power IoT application.

# What is SRAM ?
- SRAM, which stands for Static Random-Access Memory, is a type of computer memory that is used for storing data that needs to be accessed quickly and frequently by a computer's central processing unit (CPU).  SRAM is one of the two primary types of volatile memory used in computers, with the other being DRAM (Dynamic Random-Access Memory).
- One of the important characteristics of SRAM is its stability. Once data is written into an SRAM cell, it remains there until it's explicitly overwritten. This is in contrast to DRAM, which requires periodic refreshing to maintain data integrity.
- SRAM is very fast because it doesn't require the charge/discharge cycle that DRAM needs. Data can be read or written to SRAM cells with minimal delay, making it suitable for use as cache memory in CPUs.

## Table of Contents
- [Introduction](#introduction)
- [Tools used](#tools-used)
- [Block Diagram](#block-diagram)
- [Design Description](#design-description)
   - [6-Transistor (6T) CMOS SRAM Cell](#6-transistor-6t-cmos-sram-cell)
   - [Precharge Circuit](#precharge-circuit)
   - [Row Decoder](#row-decoder)
   - [Sense Amplifier](#sense-amplifier)
   - [Data driver](#data-driver)
- [Working of SRAM](#working-of-sram)
- [Static Noise Margin](#static-noise-margin)

## Introduction
The primary objective of this project is to design a 16-byte SRAM with optimal performance and minimal power consumption in a 0.18μm CMOS process.
- Cell Design: Design of SRAM memory cells that offer fast access times and ensuring high-speed data retrieval while minimizing power consumption.
- Wordline and Bitline Design: Design of wordline and bitline circuit that facilitate fast data read and write operations, minimizing access time and maximizing data throughput.
- Sense Amplifier: Implementation of differential based sense amplifiers for accurate data sensing and amplification.

## Tools used
The design process will involve extensive use of Electronic Design Automation (EDA) tools for schematic, simulation, and layout design.

- Schematic - [Cadence Virtuoso Schematic Editor](https://cadence.com/en_US/home/tools/custom-ic-analog-rf-design/circuit-design/virtuoso-schematic-editor.html)
- Simulation - [Cadence Spectre Simulator](https://en.wikipedia.org/wiki/Spectre_Circuit_Simulator)
- Layout - [Cadence Virtuoso Layout Suite](https://www.cadence.com/en_US/home/tools/custom-ic-analog-rf-design/layout-design/virtuoso-layout-suite.html)


## Block Diagram

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/40204812-c10f-4fd2-a6c0-59761492b5f0)

## Design Description
the complete SRAM design contains a 6-transistor (6T) SRAM cell, a pre-charge circuit, a row decoder, control logic, a sense amplifier and a data driver. This section will discuss the design of the blocks and their simulation results.

### 1. 6-Transistor (6T) CMOS SRAM Cell
- Each SRAM cell consists of two cross-coupled inverters (also known as a bistable circuit) and additional access transistors for read and write operations. These inverters form a latch, which can hold data in a stable state without the need for periodic refreshing (unlike DRAM).
- It's a back-to-back inverter (M1, M2, M5, M6) to store data indefinitely if power is provided to the cell. The access transistors (M3, M4) are used to read and write data into the SRAM cell. 
  
![6t-Page-1 drawio (1)](https://github.com/Shruti-Mahato/SRAM/assets/119694274/83556903-db58-4dc7-b617-117f56b3a1db)

### Sizing of 6T 
The core of the SRAM is a memory cell that stores one bit of information. Each cell’s area and power are critical since it decides the area of the entire chip.
The sizing of the devices is decided by three main factors: the area of the cell, stored data in the memory cells is not corrupted while reading it, and able to overwrite the stored data during write operation.  It can be shown that M3 needs to be stronger than M5 and, M1 needs to be stronger than M3. Since the structure is symmetric, the same constraints apply for M6, M4, M2. In this design, the 6T transistors were sized for minimum area.

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/3f3ae30e-bef8-4b5d-b6c0-89d42ace8c56)

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/43ff07f8-0666-4d60-9831-883eddacf785)


We can assume that after the access transistors are turned on, the column voltage Vc remains approximately equal to VDD. Hence, M3 operates in saturation while M1 operates in the linear region.

By Calculating we can get the the (W/L) ratio of the access transistor M3 and the (W/L) ratio of M1.


### 2. Precharge Circuit
Since the output bitlines (BL and BLB) of each 6T cell are shared by all the cells in a particular column, the parasitic capacitance on those nodes is very large making it impractical for the 6T cells to drive the bitlines to full CMOS voltage levels. Instead, both the bitlines are pre-charged to the same voltage, and a differential amplifier is used to sense the difference between the bitlines to read it. The nodes are also pre-charged before a write operation to reset a previous operation. Since the sensing mechanism is a differential operation, it is critical for both the bitlines to be equal in voltage for which one PMOS transistor can be used as equalizer which will maintain the same voltage level at both the sides.

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/6384bac5-6423-4f24-a00f-68e389da0333)



### Simulation results are given below -
![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/b0737330-9c05-4a0f-a91b-e0ccba316665)


### 3. Row Decoder
Here we are using a nand based 4:16 decoder to select a row from the sixteen rows in the SRAM array based on the input address bit configuration. The 4-bit address signals A[3:0] are used to activate the transistors in such a way that, any one of the outputs will be high. For example, if all the address bits are low (0000), then WL[0] output will be high and this will select the 0th row in the SRAM array. Similarly, if all the address bits are high (1111), WL[15] output line will be high and that will select the 15th row of the SRAM array.

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/17a8aee3-2461-472b-b8b4-30f05ff51cab)

To view the schematic of row decoder, click [here](Images/ROW_DECODER.png)

### 4. Sense Amplifier
- The sense amplifier is used to sense the voltage difference between the bitlines and amplify it to drive the digital circuits. There are different types of sense amplifiers which is used in the SRAM design depending upon the application. In this project, a differential based sense amplifier is implemented as shown in the figure below.
- Initially, during the precharge, the node voltage for BL and BLB is charged upto vdd, which is a input to the M2 and M3 transistor. Constant I is provided to the M0, which is in Saturation region and it behaves like current mirror circuit. So, equal I will flow from M1. Similary we can see that M4 also acts as current mirror circuit. So, equal I will flow from M5. Therefore, we can say that the M1 I is equally divided into two paths beacause of the circuit symmetricity.
- Now, During the read operation if there is a small voltage difference between the bitlines ‘BL’ and ‘BLB’. Then, the Vgs for M2 or M3 will be effected which will leads to change in I flowing through that transistor (M2/M3). As M4 and M5 are in series with M2/M3 So the overall I will get affected on that path. Now to maintain the KCL, the sum of I from both the paths i.e., M2,M4 and M3,M5 should be equal to I of M1. So, this extra I will either charging the node C of I0, or discharging the C depending upon the read value.
- Now, this small voltage difference is amplified by the sense amplifier and the buffer converts the output to rail-to-rail voltage levels (vdd to 0). The gain of the amplifier and the threshold of the buffer is designed very carefully to achieve this function.

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/e20217e6-78f0-47a7-899e-e3c207d07ad1)


### Simulation results are given below -
![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/6be025d7-537e-4827-bca1-50286bf77a9d)

### 5. Data Driver
The circuit that is responsible for correct writing of data into the memory cell is called the write/data driver. A possible implementation of the write driver is shown in the below figure. The write operation starts with the precharge circuit that precharges both bit-lines (i.e., Bl and BLB). Then after the precharge, access transistor should be ON for writing the given data. data is connected to BL for writing at Q Node. Similarly, datab is connected to BLB, for writing at QB node.
> Note that the data should be stable before the writing process. So, we should give the data before the WL signal became HIGH for which enables the access transistor to overwrite the stored data.

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/a7fb7dce-c3c5-40e8-b63a-27939f529942)

## Working of SRAM
### Read Operation
- Let's assume initially the 6T cell is containing 0. Then the effective circuit will be like as the image shown below (considering the bit lines are precharged to Vdd). Here, M2 and M5 are turned off. The internal node voltages are V1 = 0 and V2 = vdd before the access transistors M3 and M4 are turned on.
  
![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/48cde1ca-b8b2-4220-a576-8181e203b4c1)

- When M3 and M4 turned on by the row selection circuitry, the voltage level of column C will not show any significant variation since no current will flow through M4. On the other half of the cell, however, M3 and M1 will con-duct a nonzero current and the voltage level of column C will begin to drop slightly. While M1 and M3 are slowly discharging the column capacitance, the node voltage V₁ will increase from its initial value of 0 V.
-  When M3 and M4 turned on then Vc will discharge thus varying V1 and now the change in voltage of BL will be sensed by sense amplifier and will be interpreted as 0.

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/6a55f4d8-7231-4385-8b88-f0f622d4fbd9)

### Write Operation
Now let's consider initially the circuit was containing 1 and we want to modify the content to 0.Now for the cell containing 1 effective circuit will be like :Now to write 0 into it we forced the bit line to 0 by writing circuitory.But to modify the content V1 should be =0 As we designed circuit such in a way V2 can't go above Vtn so we have to force V1 > Vtn so that M2 will turn off.
mnb 
![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/bb7c4e52-8f90-4798-b414-b3986ec5964a)

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/6b8614f6-0f81-402c-89f4-88fb1ee535ea)


## Static Noise Margin

![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/aae0afa4-d3cc-492f-8e93-a8d446a000c4)
