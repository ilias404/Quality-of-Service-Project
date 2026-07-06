# Presentation

[Presentation link (French)](https://www.canva.com/design/DAGoGwTDFm8/35JJel1J4f7a52BGF-JD_Q/view?utm_content=DAGoGwTDFm8&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=he570613bb1)

# Quality of Service (QoS) Analysis on an IP Network

A network simulation project built with **Cisco Packet Tracer**, aimed at designing a home network topology and implementing a traffic prioritization policy (QoS) to ensure good performance for priority applications and devices.

## Context and Problem Statement

With the growing number of connected devices in households (PCs, laptops, IP phones, smart TVs, etc.), bandwidth sharing becomes a critical issue. This project addresses the following question:

How can good performance be guaranteed for priority applications on a shared home network?

**Proposed solution:** implementation of QoS mechanisms (classification, marking, queuing, bandwidth allocation) on a simulated home network.

## Objectives

- Design a home network topology incorporating QoS  
- Implement a traffic prioritization policy  
- Analyze the impact of QoS on network performance (throughput, latency, packet loss)

## Network Topology

| Device | Role |
| :---- | :---- |
| HomeRouter (1941) | Main router, gateway, and QoS policy enforcement point |
| HomeSwitch (2960) | Distribution switch, CoS marking |
| Server0 | Server (WAN) |
| PC0 – PC3 | Client workstations (Main Office zone / Family zone) |
| IP Phone0 | IP phone |
| Laptop0 | Laptop computer |

**Defined QoS policy:** PC0 \= 65% of bandwidth (priority traffic), Other devices \= 35%.

### IP Addressing Plan

| Device | IP Address | Subnet Mask | Gateway |
| :---- | :---- | :---- | :---- |
| PC0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC1 | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| PC2 | 192.168.1.12 | 255.255.255.0 | 192.168.1.1 |
| PC3 | 192.168.1.13 | 255.255.255.0 | 192.168.1.1 |
| Laptop | 192.168.1.14 | 255.255.255.0 | 192.168.1.1 |

## QoS Configuration Approach

1. **Traffic classification** — Using ACLs (Access Control Lists) to identify the flows to be prioritized.  
2. **Class creation** — Defining the `PC0-TRAFFIC` and `OTHER-TRAFFIC` classes via `class-map`.  
3. **Policy definition** — Bandwidth allocation (65% / 35%) via `policy-map`.  
4. **Policy application** — Attaching the policy to the router's WAN interface (`service-policy output`).  
5. **Marking configuration** — Assigning CoS values on the switch (`mls qos cos`) to preserve prioritization at Layer 2\.

### Configuration Excerpt (Router)

HomeRouter(config)\#class-map match-all PC0-TRAFFIC

HomeRouter(config-cmap)\#match access-group 101

HomeRouter(config)\#class-map match-all OTHER-TRAFFIC

HomeRouter(config-cmap)\#match access-group 103

HomeRouter(config)\#access-list 101 permit ip host 192.168.1.10 any

HomeRouter(config)\#access-list 103 permit ip any any

HomeRouter(config)\#policy-map QOS-POLICY

HomeRouter(config-pmap)\#class PC0-TRAFFIC

HomeRouter(config-pmap-c)\#bandwidth percent 65

HomeRouter(config-pmap-c)\#priority

HomeRouter(config-pmap)\#class OTHER-TRAFFIC

HomeRouter(config-pmap-c)\#bandwidth percent 35

HomeRouter(config)\#interface GigabitEthernet0/1

HomeRouter(config-if)\#service-policy output QOS-POLICY

### Configuration Excerpt (Switch)

HomeSwitch(config)\#mls qos

HomeSwitch(config)\#interface FastEthernet0/1

HomeSwitch(config-if)\#mls qos cos 5

HomeSwitch(config-if)\#mls qos cos override

HomeSwitch(config)\#interface range FastEthernet0/2-4

HomeSwitch(config-if-range)\#mls qos cos 1

HomeSwitch(config-if-range)\#mls qos cos override

Dynamic **OSPF** routing is configured on the router for LAN/WAN interconnection, along with access security (passwords, MOTD banner, password encryption).

## Testing Methodology

- **Scenario 1 — Without QoS:** all devices generate traffic simultaneously, with no prioritization.  
- **Scenario 2 — With QoS:** same traffic conditions, but with the QoS policy enabled.

**Metrics analyzed:**

- Effective throughput per device  
- Response time (latency)  
- Packet loss rate

## Results — PC0 (Priority Traffic)

- Significant increase in effective throughput (consistent with the 65% bandwidth allocation)  
- Substantial reduction in latency  
- Decrease in packet loss  
- Direct positive impact on priority applications (remote work, video conferencing)

## Benefits of Home QoS

| Use Case | Benefit |
| :---- | :---- |
| Remote work | Stable video conferencing and professional applications |
| Entertainment | Smooth video streaming even during peak usage |
| Cohabitation | Fair resource sharing among users |
| Overall experience | Fewer usage conflicts and less frustration |

## Challenges and Limitations

- Requires network equipment that supports advanced QoS features  
- More complex configuration than a standard home network  
- QoS cannot create additional bandwidth — it can only redistribute it  
- Requires regular policy adjustments as usage patterns evolve

## Areas for Improvement

- Application-based QoS rather than device-based  
- Integration of IoT devices into the QoS policy  
- Implementing a dynamic policy that adapts to real-time needs  
- Combining with caching techniques to further optimize the network

## Conclusion

QoS is a powerful tool for optimizing home network performance. The implementation demonstrated significant improvements for priority devices, and the bandwidth allocation (65%/35%) proved effective for the use case studied. QoS is becoming an increasingly essential mechanism given the growing number of connected devices and bandwidth-intensive usage.

## Tools Used

- **Cisco Packet Tracer** (v8.2.2) — network topology simulation, CLI configuration (router/switch), and QoS scenario testing
