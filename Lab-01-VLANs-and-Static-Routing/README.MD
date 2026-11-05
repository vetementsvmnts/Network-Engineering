# Lab-01: Multi-Branch Enterprise Segmentation & Deterministic Routing

## 1. Executive Summary
This project documents the design, implementation, and validation of a dual-branch enterprise network topology. The primary architectural objective is to enforce departmental segmentation utilizing Layer 2 VLANs, establish secure Layer 3 boundaries via Inter-VLAN Routing (Router-on-a-Stick), and enable deterministic inter-branch communication across a dedicated WAN link using IPv4 Static Routing. 

This document serves as a design artifact detailing the rationale behind the security and routing decisions, validating the operational state, and outlining limitations for future enterprise scaling.

## 2. Topology & Architecture Overview
The network consists of two symmetrical branch sites (Site A and Site B) to maintain organizational consistency and simplify administrative overhead.

*   **Layer 2 Domain (Site Level):** Each site utilizes a Cisco 2960 switch. 
*   **Layer 3 Boundary (Site Level):** Inter-VLAN routing is handled by Cisco 1941 routers via 802.1Q subinterfaces (Router-on-a-Stick).
*   **Transit Network (WAN):** A point-to-point `/30` subnet (`10.0.0.0/30`) interconnects the branch routers. A `/30` mask is explicitly chosen to conserve IPv4 address space and limit the broadcast domain on the transit link to strictly the two router interfaces.

## 3. Security Architecture & Segmentation (Layer 2)
From a security architecture standpoint, the primary defense against lateral threat movement is network segmentation. 

*   **VLAN Isolation:** The network is segmented into three distinct broadcast domains:
    *   **VLAN 10 (Management):** Houses critical infrastructure (Servers). Strictly isolated to prevent user-plane traffic from directly interacting with management-plane assets.
    *   **VLAN 20 (Sales) & VLAN 30 (HR):** User data planes. HR is isolated from Sales to ensure confidentiality of sensitive employee data.
*   **Trunk Security (802.1Q):** The links between switches and routers are configured as trunks. *Architectural Note:* In this lab, the native VLAN defaults to VLAN 1. In a production hardening phase, the native VLAN must be changed to an unused blackhole VLAN, and DTP (Dynamic Trunking Protocol) must be disabled in favor of static trunking to prevent VLAN hopping attacks.

## 4. Routing & Control Plane Design (Layer 3)
*   **Inter-VLAN Routing:** Implemented via Router-on-a-Stick (RoaS). The router acts as the default gateway for all endpoints. 
    *   *Design Trade-off:* RoaS is highly cost-effective for small branches but introduces a single point of failure and a bottleneck at the physical router interface. It is not suitable for high-throughput core networks.
*   **Static Routing:** The control plane utilizes static routes rather than a dynamic protocol (like OSPF or BGP). 
    *   *Security Rationale:* Static routing eliminates protocol overhead and prevents rogue routing advertisements (e.g., OSPF neighbor spoofing). It enforces a strictly deterministic, administrator-defined path for all inter-site traffic.

## 5. IP Addressing & Systems Integration
Systems Engineers rely on correct subnet masks and default gateways to ensure OS-level TCP/IP stack functionality.

| Site | VLAN | Subnet | Gateway (L3 Interface) |
| :--- | :--- | :--- | :--- |
| **Site A** | 10 (Mgmt) | 192.168.10.0/24 | 192.168.10.1 |
| | 20 (Sales) | 192.168.20.0/24 | 192.168.20.1 |
| | 30 (HR) | 192.168.30.0/24 | 192.168.30.1 |
| **Site B** | 10 (Mgmt) | 192.168.110.0/24 | 192.168.110.1 |
| | 20 (Sales) | 192.168.120.0/24 | 192.168.120.1 |
| | 30 (HR) | 192.168.130.0/24 | 192.168.130.1 |

## 6. Verification & Operational Validation
The network was validated using the OSI model troubleshooting methodology:
1.  **Layer 2 ARP Resolution:** Verified that endpoints can successfully ARP for their default gateways.
2.  **Local Inter-VLAN Routing:** Validated that Sales PCs can reach the Management server within the same site.
3.  **WAN Transit (ICMP):** Validated end-to-end routing across the static routes (e.g., PC1 at Site A to PC3 at Site B). 
    *   *Observation:* Initial ICMP requests fail while the routers process the first packet and populate their ARP caches for the next-hop IP. Subsequent requests show 0% packet loss.

## 7. Future State & Architectural Enhancements
To transition this topology into an enterprise-ready design, the following enhancements are identified:
*   **Access Control Lists (ACLs):** Currently, Inter-VLAN routing allows unrestricted traffic between VLANs. Extended ACLs must be deployed on the router subinterfaces to enforce least-privilege access (e.g., Sales cannot access HR, but both can access specific Management services).
*   **High Availability (HA):** Transition from RoaS to a Layer 3 Switching topology (SVIs) with First Hop Redundancy Protocols (HSRP/VRRP) to eliminate single points of failure.
*   **Dynamic Routing:** Migrate the WAN to an OSPF or BGP topology with authentication to support rapid convergence and future multi-site scalability.

## 8. Artifacts
*   `Lab-01-Topology.pkt` - The functional Cisco Packet Tracer simulation file.
*   `/Configurations` - Directory containing sanitized `running-config` outputs for audit and rollback purposes.
