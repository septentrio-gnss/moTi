# MosaicTimeSync
| Author|  [laekaz](https://github.com/laekaz)|
|------|-------|
|Maintainer| [(Septentrio gnss github user)](githubuser@septentrio.com)|
|License|[OCP CLA](https://www.opencompute.org/documents/ocp-cla)|

This project is co-funded by the European Union Agency for the Space Programme under the Fundamental Elements Grant EUSPA/GRANT/02/2024


## Table of Content
* [Introduction](#introduction)
* [What is the MosaicTimeSync?](#what-is-the-mosaictimesync)
  * [Can I buy it?](#can-i-buy-it)
* [What is a Time Card?](#what-is-a-time-card)
* [What is a Mosaic-G5 T?](#what-is-a-mosaic-g5-t)
* [Who is Septentrio?](#who-is-septentrio)
  * [Why open-source](#why-open-source)
* [Disclaimer](#disclaimer)
* [Deliverables](#deliverables)
  * [M.2 Form Factor](#m2-form-factor)
* [User Documentation](#user-documentation)
  * [System Setup](#system-setup)
    * [Boot Linux](#boot-linux)
    * [Check the GNSS Status (Satellite Time)](#check-the-gnss-status-satellite-time)
    * [Output the PPS and Clock](#output-the-pps-and-clock)
    * [Use the PHC Clock from Linux](#use-the-phc-clock-from-linux)
    * [When the Card is Installed and Running](#when-the-card-is-installed-and-running)
    * [If Something Goes Wrong](#if-something-goes-wrong)
* [Design Documentation](#design-documentation)
  * [Ordering Mosaic](#ordering-mosaic)
  * [Board Connections and Indicators](#board-connections-and-indicators)
    * [Pinout for M.2 Key B Connector](#pinout-for-m2-key-b-connector)
    * [USB 2.0](#usb-20)
    * [UART](#uart)
    * [PPS Out](#pps-out)
    * [Event 1 (PPS IN)](#event-1-pps-in)
    * [LED D1](#led-d1)
    * [Antenna Connector](#antenna-connector)
## Introduction 
## What is the MosaicTimeSync?

<img src="/pictures/20260216_161412%20EDIT.png" width="80%">

The MosaicTimeSync is a timing module that provides accurate and reliable synchronization signals for time-sensitive systems. It is based on a standardised M.2 form factor that can be easily integrated into compatible carrier boards such as the Open Compute Project (OCP) [Time Card](#what-is-a-Time-Card?).
This board receives precise timing information from Septentrio’s mosaic-G5 T GNSS Module (Global Navigation Satellite Systems) module. It then generates synchronisation outputs such as Pulse Per Second (PPS) and Time of Day (TOD) data. These signals are then used to synchronise services, in network interface cards (NICs), and other hardware in data centres and communication systems.

#### Can I buy it?
Yes, you can buy this board from 

Website:

## What is a Time Card?
![card](/pictures/timecard.png)

A Time Card is a PCIe card that is designed to plug into a server and turns provides precision time outputs such as PPS (Pulse Per Second) and ToD (Time of Day) and provides this precise timing to the server’s lock and network hardware, enabling high-accuracy synchronisation using protocols like the NTP or PTP. The MosaicTimeSync module connects to the Time Card as a timing source, supplying the card with GNSS time signals in standardised form factor.

Originally, it was developed by Meta and released through the Open Computer Project

More information about the Time Card on this [link](https://fr.scribd.com/document/803083407/M-2-Sync-Module-OCP-Base-Specification-1-1-1) or [Github](https://github.com/Time-Appliances-Project/Time-Card?tab=readme-ov-file) 

## What is a mosaic-G5 T?
[Septentrio mosaic-G5 T](https://www.septentrio.com/en/products/gnss-receivers/gnss-receiver-modules/mosaic-g5-t) is a compact, low-power GNSS timing receiver module with multi-band, multi-frequency capability. Designed for critical infrastructure and other applications where resilient and precise timing is essential, it ensures maximum security and uptime. It tracks all Global Navigation Satellite System (GNSS) constellations and supports both current and future signals. 

## Who is Septentrio?
![logo](/pictures/Septentrio_Hex_logo.png)

Septentrio is a leading company that designs, manufactures and sells high precision and multi-frequency GPS/GNSS receivers for demanding applications. Septentrio products are used in different industries including automotive, marine, construction, rail, machine control, logistics, precision agriculture, geographic information systems (GIS), Unmanned aerial vehicles (UAVs), surveying, mapping and scientific development. Septentrio’s receivers constantly deliver accurate and precise GNSS positioning scalable to centimetre-level and designed to perform perfectly in challenging environments. 

Septentrio's technology offers high accuracy and reliability thanks to GNSS + algorithms as well as [Advanced interference Monitoring and Mitigation (AIM+)](https://www.septentrio.com/en/learn-more/advanced-positioning-technology/aim-anti-jamming-protection) This protects your application against jamming (RF interference) and spoofing (malicious attacks).

For more information about Septentrio products go to [**https://www.septentrio.com/**](https://web.septentrio.com/GH-SSN-home).

### Why open-source
This board is open-source to encourage collaboration, customization, and innovation. By making the design files publicly available, developers and engineers can study, modify, and adapt the hardware to fit their specific applications, reducing development time and cost. It also promotes transparency and avoids vendor lock-in, allowing users to fully understand and control the design.

## Disclaimer
This project is offered as-is. The main interfaces have been tested, but the design has not been fully checked or approved by the author or Septentrio. You are responsible for how you use it in your own projects. For guidance on working with Septentrio’s GNSS mosaic modules, we suggest reaching out to Septentrio directly.

Support website: https://www.septentrio.com/en/support

### Deliverables
This open-source contains the following files for designers, producers and integrators around Septentrio's mosaic modules.
|Files         |description   |
|--------------|--------------|
|MosaicTimeSync.kicad_pro| KiCAD project|
|MosaicTimeSync.kicad_sch|KiCAD schematic|
|mosaic-G5.kicad_sch|KiCAD mosaic-G5 schematic sheet |
|M2_Edge connector.kicad_sch|KiCAD M.2 Key B schematic sheet|
|Clockdetect.kicad_sch|KiCAD clock schematic sheet|
|MosaicTimeSync.kicad_pcb|KiCAD PCB layout|
|MosaicTimeSync.pdf|schematic PDF|

#### M.2 form factor

The M.2 form factor provides a compact interface for expansion cards.The MosaicTimeSync board is M.2 Key B conector, but it can also fit into some Key M or B+M slots. B+M slots are designed with extra notches so they accept both Key B and Key M modules, allowing flexibility in installation. This means your Key B board can interconnect with a Key M-compatible slot as long as it’s a B+M slot, ensuring proper electrical connections and compatibility.

<img src="/pictures/M.2 key B & M.png" width="35%">


## User documentation

### System Setup

Before you physically install anything: 
* Make sure you have a free PCIe slot
* Make sure your BIOS supports Virtualization/IOMMU for Linux use
* You must connect the GNSS antenna to its SMA connector before inserting the Time Card.
##### Boot Linux 
Boot Linux with the Time Card inserted. 

If your Linux uses the Kernel 5.15 or newer the Time Card driver is already included. So the kernel loads the driver automatically.

The devices are exposed by the driver like:

`/dev/ptpX` -> PHC clock device 

`/dev/ppsY` -> PPS pulse signal

`/sys/class/timecard/ocp0/`-> status and attributes

These are the time interface paths in the OS.

Command example to see the devices:
```
ls -l /sys/class/timecard/ocp0/
```
    

This will show many devices including GNSS, PPS, PHC clock and atomic clock serial
##### Check the GNSS status (satellite time)
Once Linux has detected the Time Card:

* GNSS time is usually on a serial port like `/dev/ttyS7` or similar.
* You can run `gpsd` or a tool like `cgps` to check the satellite status.

For Example:

```
gpsd /dev/ttyS5
```

* To make sure GPS is sending messages, use tio

```
tio -b 115200 /dev/ttyS5
```
* Check if the FPGA time is correct by checking the NMEA

```
tio -b 115200 /dev/ttyS0
```

##### Output the PPS and Clock
available_sma_outputs contains all available outputs

To list available outputs:
```
cat available_sma_outputs
    10Mhz PHC MAC GNSS1 GNSS2 IRIG DCF GEN1 GEN2 GEN3 GEN4 GND VCC
```
```
 cat /sys/class/timecard/ocp0/available_sma_outputs 
```
Output on SMA:
* FPGA PPS 
```
echo OUT: PHC >> sma1
```
* Atomic Clock PPS
```
echo OUT: MAC >> sma1
 ```
* GPS Module's PPS 
```
echo OUT: GNSS1 >> sma1
```

##### Use the PHC clock from Linux
The PHC (Precision Hardware Clock) device exposed by the driver can be used to sync the Linux system clock

##### When the card is installed and running
Once everything is configured:
* The Time Card provides accurate time to Linux
* You can serve time to network clients using NTP or PTP
* Your system becomes a Stratum 1 time source 

This means your server can act as a time source for the whole infrastructure.

##### If something goes wrong
* If the driver fails to load: make sure you have a new Linux kernel
* If /dev/ptp doesn’t show: check BIOS PCIe settings
* If no GNSS: verify if antenna is connected.

These are normal steps to get the Time Card recognized by Linux.

## Design documentation
#### Ordering mosaic
If you need to order a mosaic-G5 T please contact [Septentrio](#Who is Septentrio?)

<img src="/pictures/block%20diagram.png" width="80%">

The above block diagram is showing the overview communication between the antenna, receiver and M.2 edge connector. 

### Board connections and indicators
  ![connect](/pictures/connections_edit.png)

  
| interface signals |I/O Type | Voltage | Signal Description |
|-------------------|---------|---------|--------------------|
| Power             | power   |  3.3V   |3.3 V Supply pin.   |
| UART              |   I&O   |    3.3V | Universal Asynchronous Receiver Transmitter, RX & TX|
| PPS Out 1         |   O     | 3.3v    |Pulse-per-second signal out#1.
| PPS Out 2         |   O     | 3.3v    |Pulse-per-second signal out#2.
| Event 1 (PPS IN)  |   I     |   3.3V  |Pulse-per-second reference signal input. If not used, connect to ground on Platforms.|
| CLK_IN            |   I     | 3.3V    |10MHz reference clock input. If not used, connect to the ground|
| CLK_OUT           |   O     | 3.3V    |10MHz output clock.   |
| LED D1            |   O     | 3.3V    |Power Supply indicator|

#### Pinout for M.2 Key B Connector

<img src="/pictures/M.2%20connector.PNG" width="80%">


| Pin # |Signal            |
|-------|------------------|
| 7	    |USB D+            |
| 9	    |USB D-            |
| 23	|10MHz clock input |
| 67	|nRST_IN           |
| 2, 4	|VBAT              |
| 10	|LED#1             |
| 20	|PPS2              |
| 22	|Ref_CLK_SEL       |
| 24	|PPS input         |
| 46	|PPS1              |
| 48	|10MHz clock output|
| 62	|UART COM1 Rx      |
| 64	|UART COM1 Tx      |
| 70, 72, 74|   Power 3.3v |
| GND	|GND               |


**USB 2.0** pins USB_D+ and USB_D- 
When the module is inserted into a card that supports USB pins on its edge connector, the USB interface works just like it would on any other device. It is used for data, configuration, and control. It carries data such as , Time of Day (ToD) for the GNSS, diagnostic messages and firmware updates channels. The Time Card will act as the USB host.

**UART** (Universal Asynchronous Receiver Transmitter)
This is a standard serial port using TX and RX pins.

**PPS Out** timing reference signal output. This supplies high-precision timing references directly into the Time Card Clocking system. 

**Event 1 (PPS IN)** This pin detects a pulse from an external device and records the exact GNSS time when the pulse happened. It is connected to the eventB pin of the mosaic-G5 T

##### LED D1
![alt](/pictures/LED2+.png)
This LED indicates if the Module is ON/OFF

##### Antenna connector
![alt](/pictures/antenna.PNG)

This connector is a U.FL (UMCC) Connector receptacle, Male PIn 50 Ohms        



