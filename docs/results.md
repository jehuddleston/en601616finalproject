# Results and Discussion

All data are aggregated over one baseline 5-minute iPerf3 run without backscatter and 27 runs of different configurations, across three backscatter tag positions (near phone / middle / near server) and the nine combinations of switching frequency (100 kHz, 1 MHz, 10 MHz) and duty cycle (25%, 50%, 75%).

## Summary

- **Downlink throughput (access point (AP) → phone) degrades** from a baseline of ~8.0 Mbps to ~0.7–1.0 Mbps, and this is mostly invariant across all 27 tag-present configurations.

- **Uplink throughput (phone → AP) is largely preserved**, ranging from ~33 to ~46 Mbps around the ~39.3 Mbps baseline.

- **Backscatter tag is most disruptive when placed close to the phone and least disruptive when placed close to the AP.**

- **Variation of biterate rises with duty cycle.** The bitrate CV increases from ~1.4 at 25% duty to ~1.9 at 75% duty for most frequency/position combinations, compared to a no-backscatter baseline of 0.71.


## Discussion

**Why is the downlink affected more than the uplink?** There are two possibilities: (1) the AP transmits at higher power than the phone, so AP-to-phone reflections are stronger relative to the direct path than phone-to-AP reflections; (2) the phone's compact antenna may be more easily affected by a nearby reflector than the AP's external antennas. 


## Limitations

- **Sample size:** 5-minute runs shows general trends but is still sensitive to noise and is hard to distinguish similar configurations.

- **Environmental complexity:** We only assumed that there was only single AP and single phone setup; ambient 2.4 GHz interference was not characterized.

- **TCP only:** According to our presentation feedback, it has been studied that backscatter communication's interference with the legacy link is more significant when legacy tranmission has very high throughput. In our experiments, however, this particular high throughput scenario is not carried out the way we intended to. This is because we used TCP as the transmission protocol for the iPerf3 speedtest. Since TCP is a reliable in-order delivery protocol, it does not play well with an unstable communication channel. Specifically, as backscatter communication leads to a lossy channel, TCP would automatically reduce its tranmission rate such that it stays in a safe range of low bitrate and low packet loss. Hence a high legacy link throughput is never achieved. The future direction of our project would be to switch to UDP, which does not reduce send rate upon packet loss, and see if backscatter causes a more significant degradation on the legacy link. 
