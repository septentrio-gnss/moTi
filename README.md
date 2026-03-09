# MosaicTimeSync
Maintainer: [(Septentrio gnss github user)](githubuser@septentrio.com)
License:[OCP CLA](https://www.opencompute.org/documents/ocp-cla)

## Table of Content
* [What is the MosaicTimeSync?](What-is-the-MosaicTimeSync?)
* [What is a Time Card?](#What-is-a-Time-Card?)
* [What is a mosaic-G5 T?](#What-is-a-mosaicG5-T?)
* [Who is Septemtrio?](#who-is-septentrio)
* [User documentation](#User-documentation)
    * [System SetUp](#System-SetUp)
* [Design documentation](#Design-documentation)
* [Hardware files](#Hardware-files)


## What is the MosaicTimeSync?
![alt](/pictures/20260216_161412%20-%20Copy.png)

The MosaicTimeSync is a timing module that provides accurate and reliable asynchronisation signals for time-sensitive systems. It is based on a standardised M.2 form factor that allows to be easily integrated into compatible carrier boards such as the Open Compute Project (OCP) [Time Card](#what-is-a-Time-Card?).
This board receives precise timing information from Septentrio’s Moaic-G5 T GNSS Module (Global Navigation Satellite Systems) module. It then generates synchronisation outputs such as Pulse Per Second (PPS) and Time of Day (TOD) data. These signals are then used to synchronise services, in network interface cards, and other hardware in data centres and communication systems.


## What is a Time Card?
![card](/pictures/timecard.png)

A Time Card is a PCle card that is designed to plug into a server and turns into a precision time outputs such as PPS (Pulse Perr Second) and ToD (Time of Day) and provides this precise timing to the server’s lock and network hardware, enabling high-accuracy synchronisation using protocol like the NTP or PTP. TheMosaicTimeSync module connects to the Time Card as a timing source, supplying the card with GNSS time signals in standardised form factor.

Originally it was developed by Meta and released though the Open Computer Project

More information about the Timecard on this [link](https://github.com/Time-Appliances-Project/Time-Card?tab=readme-ov-file) 

## What is a mosaic-G5 T?
[Septentrio mosaic-G5 T](https://www.septentrio.com/en/products/gnss-receivers/gnss-receiver-modules/mosaic-g5-t) is a compact, low-power GNSS timing receiver module with multi-band, multi-frequency capability. Designed for critical infrastructure and other applications where resilient and precise timing is essential, it ensures maximum security and uptime. It tracks all Global Navigation Satellite System (GNSS) constellations and supports both current and future signals. 

#### Other mosaic versions
You can used other [Mosaic modules](https://www.septentrio.com/en/products/gnss-receivers?f%5B0%5D=type%3A604), but you need to pay attention and consider the functions and pins that could be exposed, then modify the design for the purpose of your own project. 

## Who is Septentrio?
![logo](/pictures/Septentrio_logo.png)

Septentrio is a top company that designs, manufactures and sells high precision and multi-frequency GPS/GNSS receivers for demanding applications. Septentrio products are used in different industries including automotive, marine, construction, rail, machine control, logistics, precision agriculture, geographic information systems (GIS), Unmanned aerial vehicles (UAVs), survey, mapping and scientific. Septentrio’s receivers constantly delivers accurate and precise GNSS positioning scalable to centimetre-level designed to perform perfectly in challenging environments. 

Septentrio's technology offers high accuracy and reliability thanks to GNSS + algorithms as well as [Advanced interference Monitoring and mitigation (AIM+)](https://www.septentrio.com/en/learn-more/advanced-positioning-technology/aim-anti-jamming-protection) This protects your application against jamming (RF interference) and spoofing (malicious attacks).

For more information about Septentrio products go to [**https://www.septentrio.com/**](https://web.septentrio.com/GH-SSN-home).

## User documentation

### System SetUp

Before you physically install anything: 
* Make sure you have a free PCle slot
* Make sure your BIOS supports Virtualisation/IOMMU for Linux use
* You must connect the GNSS antenna to its SMA connector before inserting the Time Card.
##### Boot Linux 
Boot Linux with the Time Card inserted. 

If your Linux uses the Kernel 5.15 or newer the Time Card driver is already included. So the kernel loads the driver automatically.

The devices are exposed by the driver like:

`/dev/ptpX` -> PHC clock device 

`/dev/ppsY` -> PPS pulse signal

`/sys/class/timecard/ocp0/`-> status and atttributes

These are the time interface paths in the OS.

Command example to see the devices:
```
ls -l /sys/class/timecard/ocp0/
```
    

This will show many devices including GNSS, PPS, PHC clock and atomic clock serial
##### Check the GNSS status (satellite time)
Once Lunix has detected the Time Card:

* GNSS time is usually on a serial port like `/dev/ttyS7` or simillar.
* You can run `gpsd` or a tool like `cgps` to check the satellite status.

For Example:

```
gpsd /dev/ttyS5
```

* To make sure GPS is sending message, use tio

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
The PHC (Presition Hardware Clock) device exposed by the driver can be used to sync the Linux system clock

##### When the card is installed and running
Once everything is configured:
* The Time Card provides accurate time to Linux
* You can serve time to network clients using NTP or PTP
* Your system becomes a Stratum 1 time source because it is tied to GNSS 

This means your server can act as a time source for the whole infrastructure.

##### If something goes wrong
* If the driver fails to load: make sure you have a new Linux kernel
* If /dev/ptp doesn’t show: check BIOS PCle settings
* If no GNSS: verify if antenna is connected.

These are normal steps to get the Time Card recognized by Linux.


## Design documentation

### Hardware files
* KiCad Project files
* Schematics PDF 
* BOM (Bill Of Materials)
#### Ordering mosaic

<img src="/pictures/block%20diagram.png" width="80%">

The above is a block diagram showing the overview communication between the antenna, receiver and M.2 edge connector. 

### Board connections and indicators

![connect](/pictures/connections.png)
These Pins are mandatory so they should be exposed to the Time Card.

| Mandatory interface signals  |
|-------------------|
|Power 3.3V         |
| USB 2.0           |
| UART              |
| LED1              |
| RESET             |
| 1 Hz PPS Out      |

**USB 2.0** pins USB_D+ and USB_D- 

When the module is inserted into a card that supports USB on its edge connector, the USB interface works just like it would on any other device. It is used for data, configuration, and control. It carries data such as , Time of Day (ToD) for the GNSS, diagnostic messages and firmware updates channels. The Time card will act as the USB host.

**UART** (Universal Asynchronous Receiver Transmitter)
This is a standard serial port using COM1, TX, COM1 and RX pins.

**LED#1** provides real time status 

**PPS Out** timing reference signal output. This supplies high-precision timing references directly into the Time Card Clocking system.

| Optional data interfaces signals |
|----------------------------------|
| PCIe                             |
| SGMII/USB3.0                     |
| I2C Controller interface         |
                               

| Optional Sync interface signals |
|---|
| Secondary 1PPS Out |
| 1PPS In |
| 10 MHz clock Out |
| 10 MHz clock in |

This LED indicates if the Module is ON/OFF
![alt](/pictures/LED2+.png)

##### Antenna connector
![alt](/pictures/antenna.PNG)

This connector is a U.FL (UMCC) Connector receptacle, Male PIn 50 Ohms 

#### Connection with the Timecard

## Technical documentation

#### Pinout for M.2 Connector

![alt](/pictures/M.2%20connector.PNG)

| Pin # |Signal            |
|-------|------------------|
| 7	 |USB D+               |
| 9	 |USB D-               |
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
| 70, 72, 74|   Power 3.3v        |
| GND	|GND               |

#### Pinout for MosaicG5 T

| Pin #	|Signal     |
|-------|-----------|
| 2	|MAIN RF signal |
| 7	|nRST_IN        |
| 9	|UART COM1 Rx   |
| 10	|UART COM1 Tx  |
| 12	|USB D+        |
| 13	|USB D-        |
| 14	|USB VBUS      |
| 54	|VANT          |
| 53	|GPIO2         |
| 52	|GPIO1         |
| 51	|VREF_O        |
| 50	|VREF_I        |
| 48	|REF_I         |
| 47	|REF_O         |
| 43	|PPS2          |
| 42	|PPS1          |
| 39	|EventA        |
| 31	|I2C SDA       |
| 30	|I2C SCL       |
         
### M.2 Edge connector Pin Functions
| Pin NUMBER | Signal Name | I/O Type | Voltage | Signal Description |
|---|---|---|---|---|
| 1 | CONFIG_3 | OD | - | N/C on the Module, Defines Module type and indicates whether a Module is present, connect weak pull-up on Platforms. If not used, connect to the ground. |
| 2 | 3.3V / VBAT | Power | - | 3.3V battery Supply pin, with a voltage tolerance of 3.134V to 4.4V. |
| 3 | GND | Power | - | Return current path. |
| 4 | 3.3V / VBAT | Power | - | 3.3V battery Supply pin, with a voltage tolerance of 3.134V to 4.4V. |
| 5 | GND | Power | - | Return current path |
| 6 | CARD_POWER_ | I, PU | 3.3V | Active low Module Power turn off signal, if not used, connect to 1K pull-up on Platforms. |
| 7 | USB_SER_DP | I/O | - | USB2.0 Data, Plus |
| 8 | W_DISABLE1# | I | - | Active low, turn off radio operation. If not used, keep floating on Platforms. |
| 9 | USB_SER_DM | I/O | - | USB2.0 Data, Minus |
| 10 | LED#1/Activity | OD | 3.3V | LED#1 Status indicator. Open drain, active low signal. |
| 11 | GND | Power | - | Return current path. |
| 12 | KEY-B | - | - | - |
| 13 | KEY-B | - | - | - |
| 14 | KEY-B | - | - | - |
| 15 | KEY-B | - | - | - |
| 16 | KEY-B | - | - | - |
| 17 | KEY-B | - | - | - |
| 18 | KEY-B | - | - | - |
| 19 | KEY-B | - | - | - |
| 20 | PPS_OUT2 | O | 3.3V | Pulse-per-second signal out#2. |
| 21 | CONFIG_0 | OD | - | N/C on the Module, Defines Module type as well as an indication of whether a Module is present or not, connect weak pull-up on Platforms. If not used, connect to the ground. |
| 22 | Module_Ref_CLK_ | I,PU | 3.3V | Module reference clock selection: Low - the Module will use his local oscillator. High - the Module will use the CLK_IN signal (pin #23). |
| 23 | CLK_IN | I | 3.3V | 10MHz reference clock input. If not used, connect to the ground |
| 24 | PPS_IN | I | 3.3V | Pulse-per-second reference signal input. If not used, connect to ground on Platforms. |
| 25 | NC | - | - | Not to be used, keep floating on platform |
| 26 | MSCL | I/O | 3.3V | I2C master, clock out signal, connect to 4.7K pull-up on Platforms. |
| 27 | GND | Power | - | Return path |
| 28 | LED#2/CLK_In_Stat | O | 3.3V | LED#2 Status indicator. Input clock status (Optional): Low - Module fails to lock on the input clock. High - Module is locked on the input clock. |
| 29 | SGMII_TX_n/USB3.1_Tx_ | O, LVDS | - | GBE Ethernet, SGMII, or USB3.1 TX Minus, platform receiver differential signal pair. |
| 30 | NC | - | - | Not used, keep floating on Platforms. |
| 31 | SGMII_TX_p/USB3.1_Tx_ | O, LVDS | - | GBE Ethernet, SGMII, or USB3.1 TX Plus, platform receiver differential signal pair. |
| 32 | NC | - | - | Not used, keep floating on Platforms. |
| 33 | GND | Power | - | Return current path. |
| 34 | NC | - | - | Not used, keep floating on Platforms. |
| 35 | SGMII_RX_n/USB3.1_Rx_ | I, LVDS | - | GBE Ethernet, SGMII, or USB3.1 RX Minus, platform transmitter differential signal pair. |
| 36 | NC | - | - | Not used, keep floating on Platforms. |
| 37 | SGMII_RX_p/USB3.1_Rx_p | I, LVDS | - | GBE Ethernet, SGMII, or USB3.1 RX Plus, platform transmitter  differential signal pair. |
| 38 | NC | - | - | Not used, keep floating on Platforms. |
| 39 | GND | Power | - | Return current path. |
| 40 | SMB_CLK | I/O | 3.3V | I2C slave clock input needs to connect to 4.7K pull-up on Platforms. |
| 41 | PETn0 | O, LVDS | - | PCIe Lane 0 Tx, Minus. |
| 42 | SMB_DATA | I/O | 3.3V | I2C slave data, need to connect to 4.7K pull-up on Platforms. |
| 43 | PETp0 | O, LVDS | - | PCIe Lane 0 Tx, Plus. |
| 44 | IRQ | O | 3.3V | Active low, Module interrupt indication. |
| 45 | GND | Power | - | Return current path. |
| 46 | PPS_OUT1 | O | 3.3V | Pulse-per-second signal out#1. |
| 47 | PERn0 | I, LVDS | - | PCIe Lane 0 Rx Minus. |
| 48 | CLK_OUT | O | 3.3V | 10MHz output clock. |
| 49 | PERp0 | I, LVDS | - | PCIe Lane 0 Rx Plus. |
| 50 | PERST# | I | 1.8V | PCIe Reset, function reset to the card as defined by the PCIe Mini CEM specification. If not used, connect to weak pull-up on Platforms. PCIe Lane 0 Rx Plus. |
| 51 | GND | Power | - | Return current path. |
| 52 | CLKREQ# | I/O, OD | 1.8V | Clock Request, a reference clock request signal as defined by the PCIe Mini CEM specification, active Low. |
| 53 | REFCLKn | I, LVDS | - | PCIe Reference Clock signals (100 MHz), Minus. |
| 54 | PEWake# | I/O | 1.8V | PCIe PME Wake , Open Drain, pull up on platform. Active Low. |
| 55 | REFCLKp | I, LVDS | - | PCIe Reference Clock signals (100 MHz), Plus. |
| 56 | NC | - | - | Not used, keep floating on Platforms. |
| 57 | GND | Power | - | Return current path |
| 58 | NC | - | - | Not used, keep floating on Platforms. |
| 59 | NC | - | - | Not used, keep floating on Platforms. |
| 60 | MSDA | I/O | 3.3V | I2C master, DATA, connects to 4.7K pull-up on Platforms. |
| 61 | NC | - | - | Not used, keep floating. |
| 62 | UART_ | I | 3.3V | Universal Asynchronous Receiver Transmitter, RX. |
| 63 | NC | P | - | - |
| 64 | UART_ | O | 3.3V | Universal Asynchronous Receiver Transmitter, TX. |
| 65 | NC | - | - | Not used, keep floating on Platforms. |
| 66 | NC | - | - | Not used, keep floating on Platforms. |
| 67 | RESET# | I, PD | 3.3V | Active Low, Module Power-On reset signal, if not used, connect to 1K pull-up on Platforms. |
| 68 | SUSCLK | I | 3.3V | 32.768 kHz input clock, enable critical keep alive circuitry during platform normal and suspend modes. If not used, connect to weak pull-up on Platforms. |
| 69 | Config_ | OD | - | Grounded on the Module, Defines Module type and indication of whether a Module is present, connect weak pull-up on Platforms. If not used, connect to the ground. |
| 70 | 3.3V | Power | - | 3.3 V Supply pin. |
| 71 | GND | Power | - | Return current path. |
| 72 | 3.3V | Power | - | 3.3 V Supply pin. |
| 73 | VIO_CFG | O | - | Grounded on the Module, as the Module supports 3.3V on the IO signals, connect weak pull-up on Platforms. |
| 74 | 3.3V | Power | - | 3.3 V Supply pin. |
| 75 | CONFIG_ | OD | - | N/C on the Module, Defines Module type as well as indication of  whether a Module  is present or not, connect weak pull-up onPlatforms. If not used, connect to the ground. |


