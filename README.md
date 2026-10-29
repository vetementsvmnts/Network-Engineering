#  Network Engineering & Security Architecture Portfolio

## Overview
This repository serves as a centralized documentation hub for enterprise network topologies, configurations, and architectural design decisions. The artifacts within this repository demonstrate practical implementations of routing, switching, network segmentation, and security boundaries using Cisco IOS and Packet Tracer.

---

## Design Philosophy & Architecture

The labs in this repository are built on the following core engineering principles:
*   **Security through Segmentation:** Utilizing VLANs to restrict broadcast domains and isolate departmental traffic (e.g., HR, Management, Sales) to enforce the principle of least privilege at Layer 2.
*   **Deterministic Routing:** Implementing explicit path selection (Static Routing) where network predictability and security auditing are prioritized over dynamic protocol overhead.
*   **Scalable Inter-VLAN Routing:** Utilizing Router-on-a-Stick (RoaS) for cost-effective Layer 3 boundary enforcement in small-to-medium branch deployments, while noting its limitations for future migration to Layer 3 Switching (SVI).
*   **Systems Integration:** Ensuring correct gateway assignments and subnetting to guarantee seamless integration with upstream systems (DHCP, DNS, and endpoint OS network stacks).


##  Technical Competencies Demonstrated

### Network Engineering
*   **Layer 2:** VLAN configuration, Access/Trunk port tuning, Spanning Tree Protocol (STP) considerations (implicit).
*   **Layer 3:** Subnetting, Inter-VLAN routing, Static Route configuration (Next-Hop vs. Directly Connected).
*   **WAN:** Point-to-point /30 subnetting for WAN links to conserve IP space and enhance security.

### Security Architecture
*   **Traffic Isolation:** Layer 2 VLANs act as a primary defense mechanism, preventing lateral movement between non-related business units.
*   **Gateway Security:** Using router subinterfaces to act as the single point of ingress/egress for specific subnets.

### Systems Engineering & Operations
*   **Verification & Validation:** End-to-end reachability testing (ICMP/ARP) to ensure systems can successfully traverse the OSI model. 
*   **Troubleshooting:** Packet-level simulation to diagnose ARP resolution, MAC address learning, and routing path selection.

---



---
*Maintained by Kitsana Thuekoh. For architectural discussions or collaboration, feel free to open an issue.*
