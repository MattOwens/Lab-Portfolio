# Lab-Portfolio
# Matthew Owens – Enterprise Homelab  
**Revision 1.0**
This repository documents the design, implementation, and ongoing operation of an enterprise-style homelab network.
The lab is designed to replicate real-world enterprise network architecture, with an emphasis on segmentation, security, operational realism, and recoverability.
This is not a flat lab. It is intentionally built, operated, broken, and repaired like a production environment.

**Objectives:**
This homelab exists to support:

-CCNA → CCNP Enterprise & Security study

-Firewall, routing, and switching mastery

-Virtualization and enterprise service hosting

-Security-first network design

-Realistic troubleshooting and failure analysis

The goal is to design, deploy, harden, break, and recover a production-grade enterprise network.

**High-Level Architecture**
The lab follows a classic enterprise edge → firewall → core → access model.
```
                     ┌────────────────────┐
                     │   Simulated ISP     │
                     │   Cisco 1941        │
                     └─────────┬──────────┘
                               │
                               │  WAN / Public
                               │
                     ┌─────────▼──────────┐
                     │   Edge Router       │
                     │   Cisco 1941        │
                     └─────────┬──────────┘
                               │
                          WAN / Transit
                               │
                     ┌─────────▼──────────┐
                     │    OPNsense         │
                     │    Firewall         │
                     └─────────┬──────────┘
                               │
                       VLAN 40 (Transit)
                               │
                     ┌─────────▼──────────┐
                     │   L3 Core Switch    │
                     │   Cisco CBS350      │
                     └─────────┬──────────┘
                 ┌─────────────┴─────────────┐
                 │                           │
        ┌────────▼────────┐        ┌────────▼────────┐
        │  Server / VM     │        │  Access Switch   │
        │  Infrastructure  │        │  Catalyst 2960C │
        │  (Proxmox)       │        └────────┬────────┘
        └──────────────────┘                 │
                                  ┌─────────┼─────────┐
                                  │         │         │
                            ┌─────▼─────┐ ┌─▼──────┐ ┌▼────────┐
                            │  USERS     │ │ MGMT   │ │ Clients │
                            │ VLAN 20    │ │ VLAN10 │ │ Test    │
                            └────────────┘ └────────┘ └────────┘
```
```
VLAN & Subnet Design
VLAN	        Name	                  Purpose	                              Subnet
10	          MGMT	        Device management & admin access	          10.10.10.0/24
20           USERS	            End-user endpoints	                    10.10.20.0/24
30	         SERVERS	      Proxmox & enterprise services	              10.10.30.0/24
40	         TRANSIT	      Firewall ↔ Core routed transit network	    10.10.40.0/30
999	        PARKING	      Native VLAN for trunks (no hosts allowed)          	N/A
Notes
```
VLAN 40 is a pure routed transit network — no hosts, no DHCP, no management.
VLAN 999 is the native VLAN on all trunks and carries no traffic.
Inter-VLAN routing occurs at the core switch, with policy enforcement at the firewall.

**Virtualization Layer**

-Proxmox Infrastructure-

Proxmox hosts connect directly to the core switch using trunk ports.

This allows:

-VM placement into multiple VLANs
Clear separation of:
-Management
-Server
-User-facing services
-Enterprise-accurate traffic flow

Example Virtualized Services:

-Active Directory Domain Controller

-DNS

-Monitoring & logging

-Jump/Bastion host

-Application servers

**Status**

This is Revision 1.0 of the network design.

The architecture will continue to evolve as:

-Additional WAN simulations are added

-Routing protocols are introduced

-Security layers expand

-Automation and monitoring are implemented
