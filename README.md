## Introduction

This project investigates the impact of backscatter on communication on existing WiFi links. Backscatter devices are prevalent in IoT applications due to their ability to operate without a dedicated power source, but their reliance on ambient signal reflection introduces interference that may degrade nearby wireless communication. 

We focus on WiFi as the legacy link due to its high throughput, making interference more observable, and the availability of measuring tools like iPerf3. We build a backscatter tag using the HMC550A RF switch and measure its effect on WiFi throughput using iPerf3 across varying switching frequencies, duty cycles, and physical positions relative to the transmitter and receiver. Our goal is to characterize how backscatter communication disrupts the legacy link, which can informe the design of interference-aware backscatter systems. 

## Background

**Backscatter communication** is a system that allows a backscatter device to transmit signals without requiring a dedicated power source, such as a battery or wired power supply. This characteristic makes it particularly well-suited for Internet of Things (IoT) applications where energy sourcing and efficiency are often major design constraints. 

This system operates by using existing ambient radio signals (e.g., WiFi, Cellular, Bluetooth) through the following stages:

1. Signal Harvesting: The device captures ambient radio signals that are already present in the environment.
2. Energy Conversion: A portion of the harvested signal is converted into energy to power the device.
3. Signal Reflection and Modulation: The remainder of that signal is reflected back into the environment. During the reflection, the device modulates the signal to encode the data that it wants to transmit. 

## Problem statement

Since it replies on signal reflection, a backscatter communication environment always has two coexisting links. 
- Legacy Link: the pre-existing active signal in the environment, which the backscatter device harvests.
- Backscatter Link: the signal reflected and modulated by the backscatter device.

Because both links operate on the same radio frequency range, their signals may interfere with each other. This problem is known as **co-channel interference**. This project sets out to study the impact of backscatter communications on the exisiting communication (legacy) links.

## Hardware details

![backscatter tag](images/tag.jpg)

Backscatter device:
- \[LEFT\] RF signal terminator (Mini-Circuits, ANNE-50L+, 50Ω impedance, DC to 12 GHz frequency range, [datasheet](https://www.minicircuits.com/pdfs/ANNE-50L+.pdf))
- \[CENTER\] Backscatter tag (Hittite, 109266-HMC550A, DC to 6GHz frequency range, [datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/hmc550ae.pdf))
- \[RIGHT\] RF signal antenna (ANT-5GWWS3-SMA, [datasheet](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=srchrtrv&DocNm=ANT-5GWWS3-SMA&DocType=Data+Sheet&DocLang=English&DocFormat=pdf&PartCntxt=ANT-5GWWS3-SMA))

Power system:
- Siglent SPD3303X-E DC Power Supply. 
- Keysight Technologies E36312A Triple Output Programmable DC Power Supply.

Communication system: 
- iPerf client (iPhone 12)
- iPerf server (Linux PC with Ubuntu Noble)
- Router (NETGEAR Nighthawk Hotspot)

> Hardware modifications: All hardware components are used in our experiments *as is* without modification. 

## Software environment

We use [iPerf3](https://iperf.fr/iperf-doc.php) to measure and log WiFi data tranmission performance. We set up a iPerf3 client on an iPhone (download from [App Store](https://apps.apple.com/us/app/iperf-3-wifi-speed-test/id1462260546)), and a iPerf3 server on an Linux machine with Ubuntu Noble OS (download for [Ubuntu](https://iperf.fr/iperf-download.php)) with an public IP address `128.220.159.221`. 

For data analysis, we use a Python3 Jupyter Notebook environment, with all library dependencies specified in `requirements.txt` (see the Reproducibility Guide for details). The environment should be OS-agnostic as long as the operating system is reasonably up to date. Our setup uses Ubuntu 24.04 with Python3 and pip installed. 

## Reproducibility guide

1. Hardware assembly
![hardware assembly guide](images/experiment.jpg)

The backscatter tag assembly is centered around the HMC550A GaAs MMIC SPST switch. The Siglent SPD3303X-E DC power supply (3.3V) connects to the **Vdd** pin, and the Keysight E36312A function generator outputting a 3.3Vpp square wave connects to **Vctl** to drive the switching behavior. Both GND pins are tied to a common ground rail.

**Step 1: Verification**

![verification wiring](images/verification_wiring.png)

To verify correct switch operation before live experiments, a second function generator is connected to **RF2** to simulate an incoming RF signal. The modulated output is observed at **RF1** via an oscilloscope. The figure below shows the oscilloscope output confirming that the switch successfully modulates the input signal at the expected frequency and duty cycle.

![verification output](images/verification_output.jpg)

**Step 2: Live experiment**

![experiment wiring](images/experiment_wiring.png)

For the actual experiment, the function generator on RF2 is replaced by the **ANT-5GWWS3-SMA antenna**, which harvests ambient WiFi signals from the NETGEAR router and simultaneously reflects a modulated backscatter signal back into the environment. The **ANNE-50L+ 50Ω terminator** on RF1 absorbs the through signal. The Keysight function generator on Vctl drives the switch at the configured switching frequency and duty cycle while iPerf3 logs throughput on the legacy link in the background.

2. Software environment setup

Get the source code:
```bash
git clone git@github.com:jehuddleston/en601616finalproject.git
cd en601616finalproject
```

Set up the environment:
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

You will find the main data analysis script placed in `analysis/main.ipynb`. 

## Experiment design

We want to measure whether backscatter communication has an impact on normal WiFi transmission in the adjacent area. To do so, we set up experiments to compare WiFi performance when the backscatter device is present then absent. 

Although many communication technologies (e.g., WiFi, cellular, Bluetooth) could be used as the legacy link for a backscatter system, we selected WiFi for several reasons. First, it can be adjusted to high throughput which would make any degradation in performance more observable, whereas Bluetooth operates at low power levels, which would possibily make such effects harder to detect. Second, there is a richer list of experiment-ready software available for WiFi performance measurement compared to other technologies. All in all, WiFi offers relative simplicity in terms of setup and monitoring. 

With that in mind, we chose iPerf3 as the performance measurement tool. We set up the regular communication systems (on which the backscatter device harvest) as the following: 
- iPhone (iPerf3 client) → Wi-Fi → NETGEAR hotspot → Cellular → Linux server (iPerf3 server)

It is constantly on in the background. In the mean time, we set up the backscatter device to enumerate the following configurations: 

| Variable                      | Values                                                                                              |
|-------------------------------|-----------------------------------------------------------------------------------------------------|
| Switching frequency           | 100kHz (same channel), 1MHz (channel edge because 2MHz channel width), 10MHz (adjacent channel)     |
| Backscatter device position   | Near transmitter, middle, near receiver                                                             |
| Duty cycle                    | 25%, 50%, 75%                                                                                       |

TODO: describe how we do the experiments.
For each configuration, we run the performance test three times to reduce variance. 