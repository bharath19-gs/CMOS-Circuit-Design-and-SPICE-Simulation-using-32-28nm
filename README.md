# CMOS Circuit Design and SPICE Simulation using 28/32nm Technology


# Table of contents

- [Tool used](#Toolused)
- [Overview](#overview)
- [NMOS- Basic element in circuit design](#NMOS)
- [Regions of operation of NMOS](#operation)
  - [Cut off region](#cut)
  - [Resistive or Linear region of operation](#linear)
  - [Saturation region of operation](#sat)
- [Introduction to spice](#spice)
  - [Programs on NMOS transistor characteristics](#prog)
  - [Spice deck for w=5u L=2u and generate ID-VDS graph](#deck1)
  - [Spice deck for w=5u L=2u to generate Id-Vgs curve](#deck3)
  - [Velocity saturation effect a](#vel) 
   - [Spice deck for w=0.39u L=0.15u and generate ID-VDS graph](#deck2)
   - [Spice deck for w=0.39u and l=0.15u to generate Id-Vgs curve](#deck4)
- [CMOS](#c)
  - [Voltage tranfer characteristic](#tra)
  - [Static CMOS behaviour - CMOS robustness](#rob)
- [Acknowlegement](#ack)



# Tool used
The Synopsys Custom Compiler™ design environment is a modern solution for full-custom analog, custom digital, and mixed-signal IC design. As the heart of the Synopsys Custom Design Platform, Custom Compiler provides design entry, simulation management and analysis, and custom layout editing features. It delivers industry-leading productivity, performance, and ease-of-use while remaining easy to adopt for users of legacy tools.

![2](https://github.com/bharath19-gs/6T_SRAM_CELL/blob/main/6T_SRAM/custom_compiler.png)


# Overview

we will be Starting form the concepts of basic element in cicuit design NMOS, its characteristics curve IDVgs, IDVds, its regions of operation - drain current equation, threshold voltage equation, effect of long channel and short channel device (velocity saturation)on the behavioral characteristic of NMOS. Spice scripting and simulation of the learned concept gives a more practical analysis of its characteritics. CMOS inverter circuits its design consideration is learned and simulated including its voltage transfer characteristics, static behaviour evaluation defining its robustness based on the 4 parameters viz switching threshold voltage (Vm), noise magin, power supply variation and device variation. Also the dynamic characteristics – propagation delay of the CMOS is learned and simulated. The importance of the transistor sizes while designing a CMOS inverter for a particular application is seen. All the above design consideration assist in finding the delay table and noise margin for different transistor sizes which can be compared with the actual values provided by the particular pdk. This workshop gives a detail understanding on the basic building block of the VLSI design – CMOS.


![2_1](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/nmos.png)

```
.param vds=1.8 vgs=1.8
.option PARHIER = LOCAL
.option PORT_VOLTAGE_SCALE_TO_2X = 1
.option RUNLVL = 3

.option WDF=1
.temp 25
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09-SP2
*Sun Apr 17 10:53:39 2022

.global gnd!
********************************************************************************
* Library          : mos_testing
* Cell             : nmos4t_hvt_iv
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xm0 net1 net4 gnd! gnd! n105_hvt w=0.1u l=0.03u nf=1 m=1
v4 net7 gnd! dc='vgs'
v1 net1 gnd! dc='vds'
r3 net7 net4 r=1k

.dc v4 0 1.8 .02
.option opfile=1 split_dp=1
.option probe=1
.probe dc v(*) level=1
.probe dc isub(xm0.1)

.end
```

# [NMOS- Basic element in circuit design](#NMOS)

The pictorial representation of an NMOS device with its various terminals is as shown in figure below. Is consist of the followng

1. 4 terminal device - drain(D), gate(G), source(S), body(B)
2. Ptype substrate
3. Consist of Isolation region (Si0<sub>2</sub>)
4. n<sup>+</sup> drain and source terminal
5. Gate oxide
6. PolySi or metal gate – drives the nmos


![Nmos](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/MOSFET.jpg)

# [Regions of operation of NMOS](#operation)

There are three regions of operation in NMOS transistor. In each of the region The drain current (ID) changes with respect to the gate to source voltage (Vgs) and drain to source voltage(Vds). 
When Vsb = 0
- Initiallly Gate voltage, source voltage and drain voltage is at zero Volt. 
- The source substrate and drain substrate reqion forms a pn juction diode and both the jucnction are off as no voltage is applied to the drain and source terminal, 
- A high resistance area is formed between the source and the drain. Now 
- When the Vgs voltage is increased a positive charge is formed at the gate terminal which repels the positive charge of the p type substrate from the substrate which leaves negative charge at the surface. 
- Vgs is  further increase more and more positive charge of the ptype substrate repels resulting in depletion region. 
- the semiconductor surface inverts to n type material and the phenomenon is known as strong inversion.

***The gate to source voltage at which the strong inversion occurs is known as “Threshold voltage"***. 

When Vsb = +ve voltage

- The formation of the channel (strong inversion at the surface) is delayed as compared to when Vsb is 0.
- n<sup>+</sup> and the p substrate forms depletion region across the source substrate and drain substarte junction 
- Width of the depletion region more towards the source substrate junction
- More gate to source voltage Vgs required for strong inversion to occur
 
Threshold voltage:

![threshold_vtg](https://user-images.githubusercontent.com/63381455/154195071-d77cfd3e-bda3-4f6c-946a-6effe617f604.JPG)


All the above parameter are manufacturing process parameters, given by the foundry.


## [Cut off region](#cut)

In this region the gate to source voltage Vgs is less than the threshold voltage and thus no channel appears between the source and the drain region indicating a high resistance area between the source and the drain. Hence, the drain current is zero.

For cut off, 0 < Vgs < Vth 

ID = 0

<!---![3](https://user-images.githubusercontent.com/63381455/153581528-0e461759-bd12-4f00-8c3e-7201c17dae21.png)--->

## [Resistive or Linear region of operation](#linear)

- When Vgs voltage is higher than threshold voltage Vt, the voltage difference results in induce charges across the semiconductor surface (channel)
- The change in voltage is basically proportional to the change in the channel width
- When a small voltage is be appiled across the Vds a voltage gradient appears acorss the channel as Vbs is 0V and Vds is = some voltage
- The effective length (after inversion) L of the channel is less than the actual length which occurs due to the fabrication process
- V(x) is the voltage at the point x along the width of the channel as shown in the figure below

![induce charge](https://user-images.githubusercontent.com/63381455/154201052-40d1a250-32b2-47a1-a579-395e1753b1f0.JPG)

Drift current for resistive mode of operation

![Dc_resistive](https://user-images.githubusercontent.com/63381455/153920475-554c6aa6-e093-438c-b25b-afd46d8f4541.JPG)

## [Saturation region of operation](#sat)

- When (Vgs - Vds) >= Vt, the transistor is said to be in saturation region
- The voltage at which the difference in (Vgs - Vds) = Vt, pinch off condition is said to occur
- From the pinch off condition and further rise in threshold voltage, the channel near the drain region slowly starts disappearing as the required condition - strong inversion is violated
- The drain current in saturation region is given as

![sat](https://user-images.githubusercontent.com/63381455/154033086-00e2c735-9f3d-435c-b00b-92ed3135a661.JPG)

The figure below shows the drain current of both linear and saturation region with varying Vds. 

![IdVds](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/operating_regions.png)


# MOS simulations

## NMOS transistor characteristics  

The following set of programs are wriiten to generate the ID-VDs, ID-Vgs curve of different transistor sizes using SAED-32/28nm  TT corner. 

1a. [Spice deck for w=5u L=2u and generate ID-VDS graph](#deck1)
```bash
Spice deck to generate the ID-Vds curve of a NMOS transistor using sky130 technology 
*Model Description

.option search='/home/skandha/Tools_SNPS/Downloads_temp/SAED32nm_PDK_09302020/hspice'

.param vds=1.8 vgs=1.8
.option PARHIER = LOCAL
.option PORT_VOLTAGE_SCALE_TO_2X = 1
.option RUNLVL = 3

.option WDF=1
.temp 25
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09-SP2
*Sat Apr 30 02:31:39 2022

.global gnd!
********************************************************************************
* Library          : mos_testing
* Cell             : nmos4t_hvt_iv
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xm0 net1 net4 gnd! gnd! n105_hvt w=0.2u l=0.1u nf=1 m=1
v4 net7 gnd! dc='vgs'
v1 net1 gnd! dc='vds'
r3 net7 net4 r=1k

.dc vds 0 1.8 .02
.option opfile=1 split_dp=1
.option probe=1
.probe dc v(*) level=1
.probe dc isub(xm0.1)

.end

```

![01_IDVds_wF](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/nmos_id_vs_vds_higher.png)

1b. [Spice deck for w=1u L=0.5u to generate Id-Vgs curve](#deck3)

```bash
*Model Description
option search='/home/skandha/Tools_SNPS/Downloads_temp/SAED32nm_PDK_09302020/hspice'

.param vds=1.8 vgs=1.8
.option PARHIER = LOCAL
.option PORT_VOLTAGE_SCALE_TO_2X = 1
.option RUNLVL = 3

.option WDF=1
.temp 25
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09-SP2
*Sat Apr 30 02:17:42 2022

.global gnd!
********************************************************************************
* Library          : mos_testing
* Cell             : nmos4t_hvt_iv
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xm0 net1 net4 gnd! gnd! n105_hvt w=1u l=0.5u nf=1 m=1
v4 net7 gnd! dc='vgs'
v1 net1 gnd! dc='vds'
r3 net7 net4 r=1k

.dc vgs 0 1.8 .02
.option opfile=1 split_dp=1
.option probe=1
.probe dc v(*) level=1
.probe dc isub(xm0.1)

.end

```

![01_IDVgs_quadratic](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/nmos_id_vs_vgs_wL_higher.png)

# [Velocity saturation effect](#vel)

 This is one of the effect for short channel. Conventionally there are three mode of operation of mosfet viz cut off, linear and saturation region, however for lower nodes there are four mode of operation with velocity saturation region being one apart from the other three. In velocity saturation effect it is observe that for lower value of electric field the velocity is linear however after a certain time or higher electric field the velocity starts to saturate or becomes constant due to an effect known as scattering effect.
 
 ![vel_sat1](https://user-images.githubusercontent.com/63381455/154623491-a1872005-391b-4a17-bb4d-ecf157cd041e.JPG)


![vel_sat](https://user-images.githubusercontent.com/63381455/154621528-7f71d7d3-3888-400e-8cc2-d4a6131b3aa3.JPG)

Let us now see the effect of velocity saturation on the drain current equation.

![vel_sat2](https://user-images.githubusercontent.com/63381455/154627026-fd92de12-d1ec-40e2-888e-896b65b19bdb.JPG)

- Current should increase at lower nodes as shown in above equation
- However velocity saturation causes the device to saturate early

The below program shows the effect of velocity saturation on the lower node devices

2a. [Spice deck for w=0.1u L=0.05u and generate ID-VDS graph](#deck2)

```bash
Spice deck for w=0.39u L=0.15u and generate ID-VDS graph

*Model description
.option search='/home/skandha/Tools_SNPS/Downloads_temp/SAED32nm_PDK_09302020/hspice'

.param vds=1.8 vgs=1.8
.option PARHIER = LOCAL
.option PORT_VOLTAGE_SCALE_TO_2X = 1
.option RUNLVL = 3

.option WDF=1
.temp 25
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09-SP2
*Sat Apr 30 02:35:52 2022

.global gnd!
********************************************************************************
* Library          : mos_testing
* Cell             : nmos4t_hvt_iv
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xm0 net1 net4 gnd! gnd! n105_hvt w=0.1u l=0.05u nf=1 m=1
v4 net7 gnd! dc='vgs'
v1 net1 gnd! dc='vds'
r3 net7 net4 r=1k

.dc vds 0 1.8 .02
.option opfile=1 split_dp=1
.option probe=1
.probe dc v(*) level=1
.probe dc isub(xm0.1)

.end

```
![02_IDVds_0 39_w015l_wf](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/nmos_id_vs_vds_lower.png)


2b. [Spice deck for w=0.1u and l=0.05u to generate Id-Vgs curve](#deck4)

```bash
Spice deck for w=0.39u and l=0.15u to generate Id-Vgs curve

*Model description
.option search='/home/skandha/Tools_SNPS/Downloads_temp/SAED32nm_PDK_09302020/hspice'

.param vds=1.8 vgs=1.8
.option PARHIER = LOCAL
.option PORT_VOLTAGE_SCALE_TO_2X = 1
.option RUNLVL = 3

.option WDF=1
.temp 25
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09-SP2
*Sat Apr 30 02:24:33 2022

.global gnd!
********************************************************************************
* Library          : mos_testing
* Cell             : nmos4t_hvt_iv
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xm0 net1 net4 gnd! gnd! n105_hvt w=0.1u l=0.05u nf=1 m=1
v4 net7 gnd! dc='vgs'
v1 net1 gnd! dc='vds'
r3 net7 net4 r=1k

.dc vgs 0 1.8 .02
.option opfile=1 split_dp=1
.option probe=1
.probe dc v(*) level=1
.probe dc isub(xm0.1)

.end

```

![02_IDVgs_0 39_w015l_wf](https://github.com/bharath19-gs/CMOS-Circuit-Design-and-SPICE-Simulation-using-32-28nm/blob/main/mos_images/nmos_id_vs_vgs_wL_lower.png)

# CMOS 

Complementary MOSFET is a combination of NMOS and PMOS transistor. It is a basic building block of any circuit design. The figure below shows the CMOS circuit and its behaviour depending on the input voltage. Whenever Vin = Vdd, a direct path exixt between Vout and Vss and thus we can say that the capaciors discharges and Vout = 0. However, when Vin = 0, a direct path exist through Vdd and Vout and capacitor charges to Vdd and so also Vout = Vdd.

![Cmos1](https://user-images.githubusercontent.com/63381455/154632526-b59ab8be-f9fb-44ac-a79d-ddc36c1f907a.JPG)

## [Voltage tranfer characteristic](#tra)

 a) Mos as a switch : We know that, with fiinite ON resistance when |Vgs| > |vt|, curent flows through the circuit and we can say it is turn ON and with infinite OFF resistance whenever Vgs| < |vt|, the circuit is OFF that is there is no current flow from source to drain.
 
 b)	Introduction to standard MOS voltage current parameters: The snapshot below shows the CMOS voltage and current parameters. Certain observations are mode based on the parameters
	   

![Cmos_curr_vtg_par](https://user-images.githubusercontent.com/63381455/154633767-4ebe8956-dfa3-46b4-b9a7-328d25c19f83.JPG)

c) Voltage transfer characteristics for static inverter:
   Certain steps are involved in obtaining the VTC curve of an inverter. The characteristic/ load curve of both NMOS and PMOS transistors are used for the same. Let us assume Vdd =2v and it is long channel device.
 
 Step1: Select few Vgsp value
 
 | VgsP Value   | Vin = (VgsP + Vdd) |
|--------------|--------------------|
| VgsP1 = 0V   | Vin1 = 2v          |
| VgsP2 = -0.5 | Vin2 = 1.5V        |
| VgsP3 = -1   | Vin3 = 1V          |
| VgsP4 = -1.5 | Vin4 = 0.5         |
| VgsP5 = -2   | Vin5 = 0V               |   


Idsp = -Idsn

The curves which was originally in -IdsP and -VdsP for PMOS in 3rd quadrant is now change to 4th quadrant with -VdsP in x axis and Idsn in yaxis and sweep over by Vin = Vgs+Vdd. Now IdsN vs -VdsP curve is drawn for different Vin. The first step of converting VgsP to Vin is completed.

Step 2: Convert VdsP to Vout

Now as we change the VdsP to Vout, everthing remains same except the voltage shifts from -VdsP to Vout eg Vout = 0+2 = 2V i.e the current which is flowing at 2V is “0”iresspective of the value of Vin as the capacitor is fully charge. Now finally the graph shifts to the first quadrant. Also for example Vout = 2-2 = 0. In this we know that the capacitor discharges and a finite amount of Idsn current flows, which can be termed as the charging current. The snapshot of the PMOS load cirve is included below.

![pmos_charac](https://user-images.githubusercontent.com/63381455/154644915-304fb4d5-82b8-41c1-8087-3998b870b480.JPG)

Step 3: Load curve for NMOS transistor
  | VgsN Value   | Vin = VgsN |
|--------------|--------------------|
| VgsN1 = 0V   | Vin1 = 0v          |
| VgsN2 = 0.5 | Vin2 = 0.5V        |
| VgsN3 = 1   | Vin3 = 1V          |
| VgsN4 = 1.5 | Vin4 = 1.5         |
| VgsN5 = 2   | Vin5 = 2V               |   
 
 ![Nmos_load](https://user-images.githubusercontent.com/63381455/154650659-14d4ac38-1bc5-47e5-b166-e8462636aeba.png)

 
Step4: Merge the PMOS and NMOS load curve to obtain the VTC
    		Finally for VTC the curve between Vin and Vout is to be drawn. As, the Vin and Vout of both PMOS and NMOS are same. Thus in order to obtain the curves we need the superimpose the PMOS load curve over the NMOS load curve. This is necessary as the intersection point of both the load curves defines the region of operation of the transistor. For example, when Vin = 0V the point of intersection or Vout is 2V, From the figure we observe that PMOS is in linear region and NMOS is in saturation region and so on. Thus, by this way we can easily determine for the various sweep in voltage of Vin what is Vout and status of both the transistors. The voltage transfer characteristic of CMOS inverter is as shown in the snapshot below.
		
![LC_vtc](https://user-images.githubusercontent.com/63381455/154652762-3b5e4759-5fc7-4ef5-90ce-014969880255.JPG)


The following set of programs analyis the static and dynamic characteristics for CMOS inverter of differnt sizes. Here, the CMOS robustness for the variation in switching threshold, noise margin, power supply variation and device variation is plotted and verified as per theoritical understanding. Also, the change in the dynamic characteristic (rise delay, fall delay) is observe and the importance of selecting a proper set of transistor size as per requirement is seen.

3. Spice deck to plot the vtc characteristics of a cmos inverter circuit for wp/lp = 2.34wn/ln
```bash
Spice deck to plot the vtc characteristics of a cmos inverter circuit for wp/lp = 2.34wn/ln

*Model description
.param temp = 27

*Netlist description
XM1 out in Vdd Vdd sky130_fd_pr__pfet_01v8 W =0.84 L = 0.15
XM1 out in 0 0 sky130_fd_pr__nfet_01v8 W =0.36 L = 0.15 
cload out 0 50fF
Vdd Vdd 0 1.8V
Vin in 0 1.8V

*include model file
.lib "../sky130_fd_pr/models/sky130.lib.spice" tt

*simulation commands
.op
.dc Vin 0 1.8V 0.01

*interactive interpretor command
.control
run
display
setplot dc1
plot in out
.endc
.end
```

![03_vtc_2 34wl_wf](https://user-images.githubusercontent.com/63381455/152681920-f5cf0fa0-2bf9-4094-bb34-8c1cd3248615.png)

4. Spice deck to plot the dynamic characteristics(rise and fall delay) of cmos inverter circuit for wp/lp = 2.34wn/ln

```bash
Spice deck to plot the dynamic characteristics(rise and fall delay) of cmos inverter circuit for wp/lp = 2.34wn/ln

*Model descriptio
.param temp = 27

*Netlist description
XM1 out in Vdd Vdd sky130_fd_pr__pfet_01v8 W =0.84 L = 0.15
XM1 out in 0 0 sky130_fd_pr__nfet_01v8 W =0.36 L = 0.15 
cload out 0 50fF
Vdd Vdd 0 1.8V
Vin in 0 pulse 0 1.8V 0 10ps 10ps 2ns 4ns

*include model file
.lib "../sky130_fd_pr/models/sky130.lib.spice" tt

*simulation commands
.op
.tran 10ps 10ns

*interactive interpretor command
.control
run
display
setplot tran1
plot in out
.endc
.end

```
![03_riseFall_2 34wl_wf](https://user-images.githubusercontent.com/63381455/152681840-7b0078e8-33b7-46a7-9cc6-faae6b709947.png)

<!--![03_riseFall_2 34wl](https://user-images.githubusercontent.com/63381455/152681843-4b5273fc-fd11-455f-ba7b-f8b071b0b47a.png)-->

<!--![03_vtc_2 34wl](https://user-images.githubusercontent.com/63381455/152681922-93c4189c-1c95-4f93-a00e-1a0e85f8f9ad.png)-->

5. Spice deck to plot the dynamic characteristics(rise and fall delay) of cmos inverter circuit for wp/lp = wn/ln

```bash
Spice deck to plot the dynamic characteristics(rise and fall delay) of cmos inverter circuit for wp/lp = wn/ln

*Model description
.param temp = 27

*Netlist description
XM1 out in Vdd Vdd sky130_fd_pr__pfet_01v8 W =0.84 L = 0.15
XM1 out in 0 0 sky130_fd_pr__nfet_01v8 W =0.84 L = 0.15 
cload out 0 50fF

Vdd Vdd 0 1.8V
Vin in 0 pulse 0 1.8V 0 10ps 10ps 2ns 4ns

*include model file
.lib "../sky130_fd_pr/models/sky130.lib.spice" tt

*simulation commands
.op
.tran 10ps 10ns

*interactive interpretor command
.control
run
display
setplot tran1
plot in out
.endc
.end

```

![04_riseFall_1wl_wf](https://user-images.githubusercontent.com/63381455/152682013-253c279e-64e1-40de-a43d-938e3881fd39.png)



The delay table for different PMOS size with respect to constant NMOS size using sky130 technology file for tt, ss and ff corner is as shown below. The analysis starts from w = 0.42 and l = 0.15um. The programs required for evaluation of the rise delay and fall delay for different (w/l) ratio of NMOS and PMOS transistor is [here](https://github.com/Geetima2021/CMOS-Circuit-Design-and-SPICE-Simulation-using-SKY130-Technology/tree/main/Program) and so is the static characterisctics switching threshold evaluation. From each of the table it is observe thar for (w/l)p = 2.5(w/l)n, the rise and fall propagation delay is almost equal which makes them perfect for clock network. The other CMOS transistor can be use as regular buffers in other appliactions as in data path. Also seen that with increase in the PMOS size the rise propagation delay start decreasing indicating that the capacitor is charged faster as compared to discharging. The switching threshold volatges are also included for the different transistor sizes.  

***Ron(PMOS) ~ 2.5*Ron(NMOS)***

Table1: Delay table using sky130 tt corner

| **wp/lp** | **x.wn/ln** | **Rise delay (ps)** | **Fall delay(ps)** | **Switching threshold (V)** |
|-----------|-------------|---------------------|--------------------|----------------------------
| wp/lp     | 1(wn/ln)    |         149        |         73       |   0.831765   |  
| wp/lp     | 2(wn/ln)     |         88        |         75        |   0.889839   |  
| wp/lp     | 2.5(wn/ln)   |         73        |         75        |  0.895161    |
| wp/lp     | 3(wn/ln )    |         69         |         76       |   0.895161   |       
| wp/lp     | 4(wn/ln)     |         59         |         78       |    0.916129  |     
| wp/lp     | 5(wn/ln)     |         52         |         80        |   0.929032  |      

Table2: Delay table using sky130 ff corner

| **wp/lp** | **x.wn/ln** | **Rise delay (ps)** | **Fall delay(ps)** | **switching threshold (V)** |
|-----------|-------------|---------------------|--------------------|-----------------------------|
| wp/lp     | 1(wn/ln)    |         114         |         60         | 0.809677                    |
| wp/lp     | 2(wn/ln)    |         69          |         62         | 0.877419                    |
| wp/lp     | 2.5(wn/ln)  |          60         |         62         | 0.887097                    |
| wp/lp     | 3(wn/ln)    |         54          |         62         | 0.891935                    |
| wp/lp     | 4(wn/ln)    |          48         |         64         | 0.914516                    |
| wp/lp     | 5(wn/ln)    |          42         |         65         | 0.935484                    |

Table3: Delay table using sky130 ss corner

| **wp/lp** | **x.wn/ln** | **Rise delay (ps)** | **Fall delay(ps)** | **switching threshold (V)** |
|-----------|-------------|---------------------|--------------------|-----------------------------|
| wp/lp     | 1(wn/ln)    |         226         |         93         |             0.85            |
| wp/lp     | 2(wn/ln)    |         122         |         96         |           0.906452          |
| wp/lp     | 2.5(wn/ln)  |         103         |         97         |           0.908065          |
| wp/lp     | 3(wn/ln)    |          95         |         98         |           0.903226          |
| wp/lp     | 4(wn/ln)    |          76         |         100        |           0.922581          |
| wp/lp     | 5(wn/ln)    |          64         |         102        |           0.935484          |



6. Spice deck to find the noise margin of a cmos inverter circuit for (wp/lp=1/0.15) and (wn/ln=0.36/0.15)

```bash
Spice deck to find the noise margin of a cmos inverter circuit for (wp/lp=1/0.15) and (wn/ln=0.36/0.15)

*Model description
.param temp = 27

*Netlist description

XM1 out in Vdd Vdd sky130_fd_pr__pfet_01v8 W =1 L = 0.15
XM1 out in 0 0 sky130_fd_pr__nfet_01v8 W =0.36 L = 0.15 
cload out 0 50fF

Vdd Vdd 0 1.8V
Vin in 0 1.8V

*include model file
.lib "../sky130_fd_pr/models/sky130.lib.spice" tt

*simulation commands
.op
.dc Vin 0 1.8V 0.01

*interactive interpretor commandw
.control
run
display
setplot dc1
plot out vs in
.endc
.end

```

![09_noisemargin_wf](https://user-images.githubusercontent.com/63381455/152843755-83a4eb04-74a0-4db2-91c3-3469e562ad19.png)


<!--![09_noisemargin](https://user-images.githubusercontent.com/63381455/152843762-231be30e-8ef5-4314-a2b0-8ec7773097e1.png)-->


The noise margin defines the maximum allowable range for which the device can operate properly. NMH and NML is calculated using the formula below:

``` bash
   NMH = VOH - VIH
   NML = VIL - VOL
```   
   From the above plot 
```bash
VOH = 1.63125
VIH = 0.964516
NMH = 1.63125 - 0.964516 
    = 0.666734
```
```bash
VIL = 0.801613
VOL = 0.121875
NML = 0.801613 - 0.121875 
    = 0.679738
```
<!---The table below shows the noise margin for differnt transistor sizes. It is obseve that when (Wp/Lp) = 2.(Wn/Ln) there is a rise at the NMH because PMOS is responsible for holding the charges on the capacitance. When the size of PMOS is increased, a low-resistance path from supply to the capacitance is formed and as a result of that, the capacitance is able to retain the charge for a longer amount of time resulting in an increased NMH. - When (Wp/Lp) = 4.(Wn/Ln) there is a drop at the NML because the NMOS has now become weaker than the PMOS - When (Wp/Lp) = 5.(Wn/Ln) the NMh almost comes to a static point. - In the above table, NMl is not affected much but NMh has increased by 120mV but this range is still acceptable and this proves the CMOS inverter robustness with respect to the Noise MaFinally, the areas that can be used for digital and analog applications are stated in the figure below:
 
| **(Wp/Lp)um** | **(Wn/Ln)um** | **Switching threshold (Vm) V** | **NMH** | **NML** |
|---------------|---------------|--------------------------------|---------|---------|
| Wp/Lp         | 1.Wn/Ln       | ~0.99                          | 0.3V    | 0.3V    |
| Wp/Lp         | 2.Wn/Ln       | 1.1519 = ~1.2                  | 0.35V   | 0.3V    |
| Wp/Lp         | 3.Wn/Ln       | 1.25129 =~1.25                 | 0.4V    | 0.3V    |
| Wp/Lp         | 4.Wn/Ln       | 1.32054 =~1.32                 | 0.42V   | 0.27V   |
| Wn/Ln         | 5.Wn/Ln       | 0.918462 =~1.4                 | 0.42V   | 0.27V   |--->


7. Spice deck to check the dc characteristics of the CMOS transistor with varying power supply

This program shows another CMOS robustness characteristics, power supply variation. The output below shows that the change input voltage of CMOS keeping the size of the transistor constant does not have any impact on the behaviour of CMOS.
```bash
Spice deck to check the dc characteristics of the CMOS transistor with varying power supply

*Model parameters
.param temp =27

*Netlist description
XM1 out in Vdd Vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15
cload out 0 50fF
Vin in 0 1.8V
Vdd Vdd 0 1.8V

*include model file
.lib "../sky130_fd_pr/models/sky130.lib.spice" tt

*Control command

.control
let powersupply = 1.8
alter Vdd = powersupply
     let supplyvoltagevariation = 0
     dowhile supplyvoltagevariation < 6
     dc Vin 0 1.8 0.01
     let powersupply = powersupply - 0.2
     alter Vdd = powersupply
     let supplyvoltagevariation = supplyvoltagevariation + 1
    end

plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in xlabel "input voltage [V]" 
ylabel "output voltage [V]"  title "Inverter dc characteristics as a function of supply voltage" 

.endc
.end
```

![10_powsup_var_wf](https://user-images.githubusercontent.com/63381455/152846501-56336f02-80c4-4d1e-8522-1599c3b17e11.png)



8. Spice deck to plot the dc characteristics of a cmos inverter circuit for 7wp/0.15lp and 0.42wn/0.15ln

 This program shows another robust behaviour of the CMOS inverter, device variation. As seen from the output characteristics it is observe that the no change in the behavioural aspect of CMOS occurs accept that the switching threshold is shifted towards right.

```bash
Spice deck to plot the dc characteristics of a cmos inverter circuit for 7wp/0.15lp and 0.42wn/0.15ln

*Model Description
.param temp=27

*Including model file
.lib "../sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.42 l=0.15
Cload out 0 50fF
Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vin 0 1.8 0.01

*interactive interpretor command
.control
run
setplot dc1
display
plot out vs in
.endc
.end

```

![11_deviceVariation_wfVm](https://user-images.githubusercontent.com/63381455/152848582-b940d2db-eb05-4100-86c1-8094914cc61b.png)

[Acknowlegement](#ack)

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
- [Vrushab Damle](https://github.com/VrushabhDamle/sky130CircuitDesignWorkshop)


