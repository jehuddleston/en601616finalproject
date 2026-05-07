# Measuring Backscatter Communication’s Effects on Active WiFi Transmissions

![experiment setup](images/experiment.jpg)

This project investigates the impact of backscatter on communication on existing 2.4GHz WiFi link. Backscatter devices are prevalent in IoT applications due to their ability to operate without a dedicated power source, but their reliance on ambient signal reflection introduces interference that may degrade nearby wireless communication. 

We tested a backscatter tag using the HMC550A RF switch and measure its effect on WiFi throughput using iPerf3 across varying switching frequencies, duty cycles, and physical positions relative to the transmitter and receiver.

We found that (1) backscatter communication significantly degrades downlink throughput by over 85%, while uplink throughput is largely unaffected; (2) backscatter is most disruptive when placed close to the phone and least disruptive when placed close to the WiFi access point; and (3) variation of throughput rises as backscatter device stays longer on the reflect state. 