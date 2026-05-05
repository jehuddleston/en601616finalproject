# Measuring Backscatter Communication’s Effects on Active WiFi Transmissions

![experiment setup](images/experiment.jpg)

This project investigates the impact of backscatter on communication on existing WiFi links. Backscatter devices are prevalent in IoT applications due to their ability to operate without a dedicated power source, but their reliance on ambient signal reflection introduces interference that may degrade nearby wireless communication. 

We build a backscatter tag using the HMC550A RF switch and measure its effect on WiFi throughput using iPerf3 across varying switching frequencies, duty cycles, and physical positions relative to the transmitter and receiver.