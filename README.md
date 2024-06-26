# Title
Design and Implementation of 16-Byte SRAM in 0.18μm CMOS Technology for low power IoT application.

# What is SRAM ?
- SRAM, which stands for Static Random-Access Memory, is a type of computer memory that is used for storing data that needs to be accessed quickly and frequently by a computer's central processing unit (CPU).  SRAM is one of the two primary types of volatile memory used in computers, with the other being DRAM (Dynamic Random-Access Memory).
- One of the important characteristics of SRAM is its stability. Once data is written into an SRAM cell, it remains there until it's explicitly overwritten. This is in contrast to DRAM, which requires periodic refreshing to maintain data integrity.
- SRAM is very fast because it doesn't require the charge/discharge cycle that DRAM needs. Data can be read or written to SRAM cells with minimal delay, making it suitable for use as cache memory in CPUs.

## Table of Contents
- [Introduction](#introduction)
- [Specification](#specification)
- [Tools used](#tools-used)
- [Block Diagram](#block-diagram)
- [Design Description](#design-description)
   - [6-Transistor (6T) CMOS SRAM Cell](#6-transistor-6t-cmos-sram-cell)
     - [Sizing of 6T](#sizing-of-6t)
   - [Precharge Circuit](#precharge-circuit)
   - [Row Decoder](#row-decoder)
   - [Sense Amplifier](#sense-amplifier)
   - [Data driver](#data-driver)
- [Operation of SRAM](#operation-of-sram)
- [Static Noise Margin](#static-noise-margin)
- [Top level Schematic and Testbench](#top-level-schematic-and-testbench)
- [Layout](#layout)
- [Conclusion](#conclusion)

## Introduction
The primary objective of this project is to design a 16-byte SRAM with optimal performance and minimal power consumption in a 0.18μm CMOS process.
- Cell Design: Design of SRAM memory cells that offer fast access times and ensuring high-speed data retrieval while minimizing power consumption.
- Wordline and Bitline Design: Design of wordline and bitline circuit that facilitate fast data read and write operations, minimizing access time and maximizing data throughput.
- Sense Amplifier: Implementation of differential based sense amplifiers for accurate data sensing and amplification.

## Specification
- Memory Size --> 16x8-bit (16-byte)
- Technology used --> 0.18μm CMOS Technology
- Operating voltage --> 1.8V
- Operating frequency --> 50 MHz

## Tools used
The design process will involve extensive use of Electronic Design Automation (EDA) tools for schematic, simulation, and layout design.

- Schematic - [Cadence Virtuoso Schematic Editor](https://cadence.com/en_US/home/tools/custom-ic-analog-rf-design/circuit-design/virtuoso-schematic-editor.html)
- Simulation - [Cadence Spectre Simulator](https://en.wikipedia.org/wiki/Spectre_Circuit_Simulator)
- Layout - [Cadence Virtuoso Layout Suite](https://www.cadence.com/en_US/home/tools/custom-ic-analog-rf-design/layout-design/virtuoso-layout-suite.html)

## Block Diagram

Here's a simplified block diagram of a SRAM, as shown in Figure 1.
| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/c699e559-ece2-4876-b35a-cf8f3016cf9f) |
| :---: |
| Fig.1: Block diagram of a typical SRAM |

## Design Description
The complete SRAM design contains 6-transistor (6T) SRAM array, a pre-charge circuit, a row decoder, a sense amplifier and a data driver. This section will discuss the design of the blocks and their simulation results.

### 1. 6-Transistor (6T) CMOS SRAM Cell
- Each SRAM cell consists of two cross-coupled inverters (also known as a bistable circuit) and additional access transistors for read and write operations. These inverters form a latch, which can hold data in a stable state without the need for periodic refreshing (unlike DRAM).
- It's a back-to-back inverter (M1, M2, M5, M6) to store data indefinitely if power is provided to the cell. The access transistors (M3, M4) are used to read and write data into the SRAM cell. 

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/e4f8ac7f-0d99-41d1-be68-f3839f62bd8d) |
| :---: |
| Fig.2: 6T SRAM Cell |

### Design of 6T 
The core of the SRAM is a memory cell that stores one bit of information. Each cell’s area and power are critical since it decides the area of the entire chip.
The sizing of the devices is decided by three main factors: the area of the cell, stored data in the memory cells is not corrupted while reading it, and able to overwrite the stored data during write operation. 


| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/48cde1ca-b8b2-4220-a576-8181e203b4c1) |
| :---: |
| Fig.3: Read opeartion |

Assuming the SRAM is storing 0 at Q node. Before the read operation, both the bitlines are precharged to vdd. After the access transistors are turned on, the node voltage of V1 will start increasing as the C is discharging through M3 and M1. It should not be more than the Vth of M2 otherwise the stored data will get corrupted.  
Therefore,&emsp; $V1 \leq Vth(M2)------(1)$  
By taking $V_1 = 0.3$, We can find that M3 operates in saturation while M1 operates in the linear region. So the current equation for both the transistor is as follows-

$$Id(M1) = \frac{k_{n,1}}{2}\left(2(V_{GS} - V_{T,n})V_{DS} - V_{DS}^2\right)$$

$$Id(M3) = \frac{k_{n,3}}{2}(V_{GS} - V_{T,n})^2$$

As same I is flowing through M3 and M1.<br>
So,&emsp; $Id(M3) = Id(M1)   ------(2)$

By substituating the corresponding $V_{GS}$ and $V_{DS}$ values for transistorr M3 and M1, we obtain - 
$$\frac{k_{n,3}}{2}(V_{DD} - V_1 - V_{T,n})^2 = \frac{k_{n,1}}{2}\left(2(V_{DD} - V_{T,n})V_1 - V_1^2\right)$$

$$\frac{k_{n,3}}{k_{n,1}} = \frac{(W/L)3}{(W/L)1} \leq \frac{2(V_{DD} - V_{T,n})V_{1}-V_{1}^2}{(V_{DD} - V_1- V_{T,n})^2}------(3)$$

By substituating the values as $V_{T,n}= 0.67V$ and $V_1 = 0.3V$, we get 
$$\frac{(W/L)3}{(W/L)1} \leq \frac{2(1.8 - 0.67)0.3 - (0.3)^2}{(1.8 - 0.3- 0.67)^2}$$
$$\frac{(W/L)3}{(W/L)1} \leq 0.85 ------(4)$$

It can be shown that M1 needs to be stronger than M3 for correct read operation. To summarize, the transistor M2 will remain in cut-off during the read "0" operation if condition (4) is satisfied. Since the structure is symmetric, the same constraints apply for M4 and M2.

Similarly we can calculate the size for M5 transistor during write operation.
Now, consider the write "0" operation, assuming that a logic "1" is stored in the SRAM cell initially. The voltage levels in the CMOS SRAM cell at the beginning of the data-write operation is shown in the below figure. The transistors MI and M6 are turned off, while the transistors M2 and M5 operate in the linear mode. Thus, the internal node voltages are V1 = vdd and V2 = 0 before the cell access (or pass) transistors M3 and M4 are turned on. 

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/bb7c4e52-8f90-4798-b414-b3986ec5964a) |
| :---: |
| Fig.4: Write Operation |


The column voltage $V_{c}$ is forced to logic "0" level by the write driver. Once the pass transistors M3 and M4 are turned on by the row decoder circuit, we expect that the node voltage V2 remains below the threshold voltage of M1, since M2 and M4 are designed according to condition (4). Consequently, the voltage level at node (2) would not be sufficient enough to turn on MI. To change the stored information, i.e., to force $V_{1}$ to 0 V and  $V_{2}$ to vdd, the node voltage $V_{1}$, must be reduced below the threshold voltage of M2, so that M2 turns off first. 
When $V_{1} \leq Vth(M2)$  the transistor M3 operates in the linear region while M5 operates in saturation.

$$Id(M3) = \frac{k_{n,1}}{2}\left(2(V_{GS} - V_{T,n})V_{DS} - V_{DS}^2\right)$$

$$Id(M5) = \frac{k_{n,3}}{2}(V_{GS} - V_{T,n})^2$$

As same I is flowing through M3 and M5.<br>
So, $Id(M3) = Id(M5)$   ------(5)

By putting the corresponding values for $V_{GS}$, $V_{DS}$ for M3 and M5, we get  
$$\frac{k_p,5}{2}  (0 - V_{DD} - V_{T,p})^2 = \frac{k_n,3}{2} \left(2(V_{DD} - V_{T,n})V_{1} - V_{1}^2\right)$$

Rearranging this condition results in:  
$$\frac{k_{p,5}}{k_{n,3}} \leq \frac{2(V_{DD} - V_{T,n})V_{1}-V_{1}^2}{(V_{DD} - V_{T,p})^2}------(6)$$

$$\frac{(W/L)5}{(W/L)3} < \frac{\mu_n}{\mu_p} \cdot \frac{2(V_{DD} - V_{T,n})V_{1}-V_{1}^2}{(V_{DD} - V_{T,p})^2}$$

By putting the values for $\frac{\mu_n}{\mu_p} = 5$ , $V_{T_p} = V_{T,n}= 0.67$ , $V_1 = 0.3$, we get  
$$\frac{(W/L)5}{(W/L)3} \leq 5\cdot \frac{2(1.8 - 0.67)0.3 -0.3^2}{(1.8 - 0.67)^2}$$

$$\frac{(W/L)5}{(W/L)3} \leq 2.3 --------(7)$$

To summarize, the transistor M2 will be forced into cut-off mode during the write "0" operation if condition (5) is satisfied. This will guarantee that MI turns on, and changing the stored information. Since the structure is symmetric, the same constraints apply for M4 and M6.  
It can be shown that M3 needs to be stronger than M5 and, M1 needs to be stronger than M3. In this design, the 6T transistors were sized for minimum area.


### 2. Precharge Circuit
Since the bitlines (BL and BLB) of each 6T cell are shared by all the cells in a particular column, the parasitic capacitance on those nodes is very large making it impractical for the 6T cells to drive the bitlines to full CMOS voltage levels. The precharge circuit helps to initialize the bitline and bitline bar (BL and BLB) to known logic levels usually high(In Pre-charge) and low(In Pre-discharge)) before a read or write operation.
Here, We have connected the bitlines to power supply voltage (VDD) through a PMOS transistor. **PMOS** is preferred with respect to **NMOS** as it passes strong 1. Both the bitlines are pre-charged to the same voltage (vdd in this case), and a differential amplifier is used to sense the difference between the bitlines to read it. The nodes are also pre-charged before a write operation to reset a previous operation. Since the sensing mechanism is a differential operation, it is critical for both the bitlines to be equal in voltage for which one PMOS transistor can be used as equalizer which will maintain the same voltage level at both the sides.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/6384bac5-6423-4f24-a00f-68e389da0333) |
| :---: |
| Fig.5: Circuit diagram of precharge circuit |

> #### To view the schematic for Precharge Circuit, click [here](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Schematic_precharge.png)

### Simulation results are given below -

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/06c6e61d-08c2-4066-81f6-04fc440b02e2) |
| :---: |
| Fig.6: Timing diagram of precharge circuit |

In Figure 6, we can see that when PC is low, BL and BLB are charged up to vdd. After precharge BL and BLB voltage will drop as per the stored value in SRAM.

### 3. Row Decoder
Here we are using a nand based 4:16 decoder (Figure 7) to select a row from the sixteen rows in the SRAM array based on the input address bit configuration. The 4-bit address signals A[3:0] are used to activate the transistors in such a way that, any one of the outputs will be high. For example, if all the address bits are low (0000), then WL[0] output will be high and this will select the 0th row in the SRAM array. Similarly, if all the address bits are high (1111), WL[15] output line will be high and that will select the 15th row of the SRAM array.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/17a8aee3-2461-472b-b8b4-30f05ff51cab) |
| :---: |
| Fig.7: Circuit diagram of Row Decoder |


> #### To view the schematic for row decoder, click [here](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Schematic_row_decoder.png)

### 4. Sense Amplifier
- The sense amplifier is used to sense the voltage difference between the bitlines and amplify it to drive the digital circuits. There are different types of sense amplifiers which is used in the SRAM design depending upon the application. In this project, a differential based sense amplifier is implemented as shown in Figure 8.
- Initially, during the precharge, the node voltage for BL and BLB is charged upto vdd, which is a input to the M2 and M3 transistor. Constant current (I) is provided to the M0, which is in saturation region and it behaves like a current mirror circuit. So, the same current will flow from M1. Similarly, we can see that M4 also acts as current mirror circuit. So, the same current will flow from M5. Therefore, we can say that the M1 current is equally divided into two paths because of the circuit symmetricity.
- Now, during the read operation if there is a small voltage difference between the bitlines ‘BL’ and ‘BLB’. Then, the Vgs for M2 or M3 will be affected which will leads to change in current flowing through that transistor (M2/M3). As M4 and M5 are in series with M2/M3 So the overall current will get affected on that path. Now to maintain the KCL, the sum of current from both the paths i.e., M2,M4 and M3,M5 should be equal to the current of M1. So, this extra I will either charging the node C of I0, or discharging the C depending upon the read value.
- Now, this small voltage difference is amplified by the sense amplifier and the buffer converts the output to rail-to-rail voltage levels (vdd to 0). The gain of the amplifier and the threshold of the buffer is designed very carefully to achieve this function.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/e20217e6-78f0-47a7-899e-e3c207d07ad1) |
| :---: |
| Fig.8: Circuit diagram of Sense Amplifier |

> #### To view the schematic for Sense Amplifier, click [here](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Schematic_Sense_Amplifier.png)

The gain of a sense amplifier in an SRAM (Static Random-Access Memory) is typically characterized by the ratio of the output voltage swing to the input voltage difference.
The sense amplifier is a crucial component in the read operation of an SRAM cell. Therefore it's gain directly impacts the read stability and speed of the SRAM cell. The higher the gain, the more quickly it can detect and amplify the small voltage difference between the bitlines (BL and BLB) when reading data from the SRAM cell. This results in a faster read operation because it reaches the decision point (logic high or low) more rapidly, reducing the read access time. In this case, the gain is around 30dB, as shown in Figure 9.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/48124a12-fcf5-4877-8eb3-031dfa9b412a) |
| :---: |
| Fig.9: Gain of Sense Amplifier |

**Simulation results are given below -**

Initially both BL and BLB starts at 1.8V which shows the Precharge state. Now, BLB begins to discharge from 1.8V to 1.6V which indicates that logic 1 is stored in SRAM cell. This small voltage drop detects by sense amplifier and the output voltage is shown as 1.
Again both the bitlines are precharged for some duration and Now, the voltage at BL starts decreasing which indicates that logic 0 is stored in the cell. The sense amplifier will amplify this difference and gives a output as strong logic 0, as can be seen in figure 10.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/6be025d7-537e-4827-bca1-50286bf77a9d) |
| :---: |
| Fig.10: Timing diagram of Sense Amplifier |

### 5. Data Driver
The circuit that is responsible for correct writing of data into the memory cell is called the write/data driver. A possible implementation of the write driver is shown in the Figure 11. The write operation starts with the precharge circuit that precharges both bit-lines (i.e., BL and BLB). Then after the precharge, access transistor should be ON for writing the given data. data is connected to BL for writing at Q Node. Similarly, datab is connected to BLB, for writing at QB node.
> Note that the data should be stable before the writing process. So, we should give the data before the WL signal became HIGH, which enables the access transistor to overwrite the stored data.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/a7fb7dce-c3c5-40e8-b63a-27939f529942) |
| :---: |
|  Fig.11: Circuit diagram of Write driver |

> #### To view the schematic for Write Driver, click [here](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Schematic_write_driver.png)

## Operation of SRAM
### Read Operation
- Let's assume initially the 6T cell is containing 0. Then the effective circuit is shown in Figure 12. (considering the bit lines are precharged to Vdd). Here, M2 and M5 are turned off. The internal node voltages are V1 = 0 and V2 = vdd before the access transistors M3 and M4 are turned on.
  
| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/48cde1ca-b8b2-4220-a576-8181e203b4c1) |
| :---: |
| Fig.12: Read operation in SRAM |

- When M3 and M4 turned on by the row selection circuitry, the voltage level of column C will not show any significant variation since no current will flow through M4. On the other half of the cell, however, M3 and M1 will conduct a nonzero current and the voltage level of column C will begin to drop slightly. While M1 and M3 are slowly discharging the column capacitance, the node voltage $V_1$ will increase from its initial value of 0 V.
- When M3 and M4 turned on then the column capacitance will discharge thus varying the voltage at BL and this small change in voltage of BL will be sensed by sense amplifier and will be interpreted as 0.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/ff62df94-abf9-45f1-bf0b-dc40b6bb5b45) |
| :---: |
| Fig.13: Timing diagram of read operation |


### Write Operation
Now let's consider initially the circuit was containing 1 and we want to modify the content to 0. Now to write 0 into it we forced the bit line to 0 by writing circuitory.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/09656957-0bd1-4426-af3b-221d6a6f44f5) |
| :---: |
| Fig.14: Circuit diagram of write operation |

But to modify the content V1 should be 0. As we designed circuit such in a way V2 can't go above Vtn so we have to force V1 > Vtn so that M2 will turn off.

Figure 14 shows the voltage levels in the CMOS SRAM cell at the beginning of the data-write operation. The transistors MI and M6 are turnedoff, while the transistors M2 and M5 operate in the linear mode. Thus, the internalnode voltages are V 1 =V DD and V_{2} = 0V before the cell access (or pass) transistorsM3 and M4 are turned on..

The column voltage Ve is forced to logic "0" level by the data-write circuitry:thus, we may assume that V_{c} is approximately equal to 0 V. Once the pass transistorsM3 and M4 are turned on by the row selection circuitry, we expect that the node voltage V_{2} remains below the threshold voltage of M1, since M2 and M4 are designed ac-cording to condition (10.5). Consequently, the voltage level at node (2) would

Here, we can see during the precharge both the bitlines are charged upto vdd, and then write enable signal is turned ON this gives the path for data to BL and data bar(datab) to BLB. data is stable before the access transistor gets on. after the access transisor gets on. the node voltage of sram Q or QB. 

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/5a641fd2-87a4-4867-88bc-7ef6946d4173) |
| :---: |
| Fig.15: Timing diagram of write operation |

## Static Noise Margin
**Static Noise Margin (SNM)** is a measure of tolerance of a static random-access memory (SRAM) cell. It's important for determining the stability and reliability of data storage in SRAM cells. An SNM of 6T SRAM is typically evaluated in terms of the read and write stability margins.  
**Read Margin (Read SNM):** This measures how much the SRAM cell can tolerate noise or variability in the data when you're trying to read it without making an incorrect decision.  
**Write Margin (Write SNM):** This measures how much noise or variability the SRAM cell can tolerate when you're trying to write data into it without causing unintended switching of the stored values.  
In order to find the static noise margin of SRAM cell, we used the butterfly curve which is a graphical representation of the SRAM cell's behavior. In this, we ﬁnd VTC of inverter 1 and inverter 2 of memory cell. After getting the VTC of both inverters, we tried to plot both the VTC in one single plot which which ultimately resulting in a butterfly curve. The minimum side of largest square that can be ﬁxed within the lobes of the butterﬂy curve represent SNM of the SRAM bitcell. In this case, the noise margin is around 0.704V

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/aae0afa4-d3cc-492f-8e93-a8d446a000c4) |
| :---: |
| Fig.16: Butterfly curve</p> |

## Top level Schematic and Testbench
Creating a top-level schematic and testbench for SRAM in a 0.18μm CMOS technology node involves several steps. The key components are shown in Figure 17 which is a top-level schematic of SRAM.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/f2611dfd-4a85-430e-8156-324ddb7496f1) |
| :---: |
|  Fig.17: Top level Schematic of SRAM |

The signal descriptions are as follows -
1. PC = Precharge signal to precharge the Bitlines
2. WL = Worldline signal to enable the access transistor
3. ctrl = Indicate the control signal for read, write, hold operation
4. rw = read-write signal. high for read, low for write operation
5. sa = To enable the sense amplifier
6. idc = supply current to sense amplifier from BGR

The final tesbench for SRAM is shown in Figure 18 -
| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/71b53346-ca56-40bf-ac05-769cc14b8fd7) |
| :---: |
|  Fig.18: Testbench for SRAM-top |


### 1. Transient Analysis : 
To simulate SRAM for read and write operations. For example:
- Write: Apply signals to write data (0 and 1) into the SRAM cell and observe the cell's response.
- Read: Apply signals to read data from the SRAM cell and observe the cell's response.
     
Here, we are writing data to two different addresses and then reading the data from the corresponding address lines.   
![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/c8170075-c4d0-4d15-a488-f817827e47ab)

Output waveforms are given below - 
| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/af7ded51-f674-469b-9020-3805b19b75bf) |
| :---: |
| Fig.19: Read Write operation |

### Delay calculation :
**Read 0 delay** - Delay between 50% of the rising edge of ctrl signal and 50% of the falling edge of the output.  
**Read 1 delay** - Delay between 50% of the rising edge of ctrl signal and 50% of the rising edge of the output.  

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/f21c4683-aa77-4432-a382-94397ce1d043) |
| :---: |
| Fig.20: Read 0-1 delay |

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/ffc8470b-cf80-46dd-a9ce-b2ce5baa81a3) |
| :---: |
| Fig.20: Read 1-0 delay |

**Write Delay** - Delay between the rising edge of ctrl signal and the internal node of SRAM(Q/QB) when data gets overwritten to 90-95%

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/b00ed0fb-7351-42ae-bb9e-f22d38aa4c59) |
| :---: |
| Fig.21: Write delay |

**Pre-Charge Delay** - Delay between the falling edge of PC and the 95% of rising edge of BL & BLB.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/dc832d40-46a5-4250-a56b-a1556ada4801) |
| :---: |
| Fig.22: Precharge delay |

### 2. Corner Simulation : 
To test the SRAM cell under different corner cases (e.g., process, voltage, temperature) to ensure the robustness of SRAM.

| ![image](https://github.com/Shruti-Mahato/SRAM/assets/119694274/943f60f9-b191-4147-a27c-c5ac72cf101b) |
| :---: |
| Fig.23: Delay calculation in differnet PVT cornrers |

In figure 23,The highlighted values in yellow indicate the best case for their respective delays while the ones in red indicate the worst case.
## Layout
In this section, layout for verious SRAM blocks is attached below. The SRAM layout refers to the physical arrangement of components within the memory cell, including transistors, capacitors, and interconnects. Each block is designed to meet the performance, power, and area requirements of the SRAM design. By examining these layouts, one can gain insights into the structure of the SRAM design, it's analysis, optimization, and verification processes.

| ![image](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Layout_6t.png) |
| :---: |
| Fig.24: Layout for 6T transistor |

| ![image](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Layout_precharge.png) |
| :---: |
| Fig.25: Layout for Precharge Circuit |


| ![image](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Layout_row_enable.png) |
| :---: |
| Fig.26: Layout for Row enable Circuit |

| ![image](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Layout_sense_amplifier.png) |
| :---: |
| Fig.27: Layout for Sense Amplifier |

| ![image](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Layout_sram_6t_16x8.png) |
| :---: |
| Fig.28: Layout for 16x8 6T SRAM Cell |

| ![image](https://github.com/Shruti-Mahato/SRAM/blob/main/Images/Layout_sram_top.png) |
| :---: |
| Fig.29: Layout for Top level SRAM Circuit | 


## Conclusion

In this project, a 16-byte SRAM was designed using 0.18μm CMOS technology. The maximum operating frequency for this case is 50MHz, and the maximum power consumption is within 700uW (699.86uW). Since the SRAM size is 16x8, no column decoder is required, which is simplifying the design. Instead, only a row decoder is needed to select from the 16 rows of address lines.

We implemented a differential-based sense amplifier, which efficiently senses the difference between the bitlines, resulting in faster read operations compared to other sense amplifiers. In the best-case scenario, the write delay is 351.7p, and in the worst-case scenario, it is 649.5p. For precharge, the best-case delay is 397.1p, and the worst-case delay is 653.3p. During read operations, the best-case delay is 1.88ns, and the worst-case delay is 9.99ns.

Overall, this SRAM project demonstrates an efficient and robust design that meets the specified performance requirements and design goals.
