# ASTRA-network-ns3 (MLSynth-extended — priority scheduling)

This fork includes additional functionality we implemented to aid in our evaluation of **disaggregated LLM inference** workloads synthesised with [MLSynth](https://github.com/NetMLSim/MLSynth). It adds strict-priority (QoS) scheduling to the ns-3 RoCE backend, driven together with our companion [ASTRA-sim](https://github.com/andrea-germano/astra-sim) fork: flows carry a priority group (`pg`) set upstream and are served accordingly at both switches and end hosts.

All additions are opt-in via the network configuration file; with the defaults the backend behaves like upstream (round-robin).

## Current features

### Strict-priority scheduling
A new `STRICT_PRIORITY` network-config key selects the scheduling discipline (`0`: round-robin, the upstream default; `1`: strict priority). The value is propagated to the egress queues and to the end-host queue-pair scheduler.

### Priority dequeue on switches
`BEgressQueue::DequeuePRIO` serves packets from the non-empty, non-paused queue with the lowest index (= highest priority) instead of round-robin. Queue 0 keeps the highest priority, and the switch transmit path selects `DequeuePRIO` or the original `DequeueRR` according to `STRICT_PRIORITY`.

### Priority-aware QP selection on end hosts
In `QbbNetDevice`, when strict priority is enabled, the next queue-pair to transmit is chosen by best (lowest) priority group `m_pg` among the eligible QPs, falling back to round-robin among equal priorities. Round-robin is used unchanged when the flag is off.

### Misconfiguration warning
At setup, the backend prints a warning when `STRICT_PRIORITY=1` and `ACK_HIGH_PRIO=0`, since ACKs sharing the default class can be starved by high-priority data.

<details>
<summary>ASTRA-NETWORK-NS3</summary>

# ASTRA-NETWORK-NS3
This repository contains an NS3-based network simulator that acts as a network backend for ASTRA-sim. 
ASTRA-sim is a distributed machine learning system simulator, developed as a joint collaboration between Georgia Tech, Meta, and Intel.
For more details on ASTRA-sim, please refer to [the ASTRA-sim repository](https://github.com/astra-sim/astra-sim)

This repository is extended from [https://github.com/alibaba-edu/High-Precision-Congestion-Control](https://github.com/alibaba-edu/High-Precision-Congestion-Control), and simulates various congestion control algorithms. 
With ASTRA-sim this repository was used to study the performance of different network congestion control algorithms in largescale collective communications in multi-gpu scenarios.
More details on the study can be found in our paper:

T. Khan, S. Rashidi, S. Sridharan, P. Shurpali, A. Akella and T. Krishna, "Impact of RoCE Congestion Control Policies on Distributed Training of DNNs," In proceedings of the IEEE Symposium on High-Performance Interconnects (HOTI), 2022 [[pdf]](https://arxiv.org/abs/2207.10898)

For information on how to use and build this tool, please visit our wiki [ASTRA-sim wiki](https://astra-sim.github.io/astra-sim-docs/getting-started/installing-astra-sim.html#compile-program)

## Contact Us
For any questions about using this network backend with ASTRA-sim, you can email the ASTRA-sim User Mailing List: astrasim-users@googlegroups.com

To join the mailing list, please fill out the following form: https://forms.gle/18KVS99SG3k9CGXm6

</details>