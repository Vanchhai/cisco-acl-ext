# Extended Access Control List (ACL) Configuration Lab

## Key Characteristics
* **Range:** Use the numbering range 100–199 and 2000–2699.
* **Placement:** Should be placed as close to the source of the traffic as possible to save bandwidth and router resources.
* **Implicit Deny:** Every ACL ends with an invisible deny ip any any. If traffic doesn't match a permit statement, it is dropped.

## Network Topology 
The network topology consists of two distinct sites connected via a Serial WAN connection using the subnet `203.0.113.0/30`.

Below is the network diagram for this lab setup:

![Network Topology](./topology/topology-img2.png)

### Network Segmentation
* **Site A (Left Side - Core Router 1):**
  * **LAN 1 (Green Zone):** `192.168.1.0/24`
  * **LAN 2 (Pink Zone):** `192.168.2.0/24`
* **Site B (Right Side - Core Router 2):**
  * **LAN SV1 (Blue Zone):** `10.0.1.0/24` (Hosts Server1.10)
  * **LAN SV2 (Yellow Zone):** `10.0.2.0/24` (Hosts Server2.10)

## Project Overview
This repository contains a Cisco Packet Tracer laboratory focused on implementing **Extended Access Control Lists (ACLs)**. Unlike Standard ACLs, which only filter traffic based on the source IP address, Extended ACLs provide granular control by filtering traffic based on:
* Source and Destination IP addresses
* Protocols (IP, TCP, UDP, ICMP, etc.)
* Port numbers (e.g., HTTP Port 80)

## Lab Objectives & Traffic Requirements
The security policy demands traffic restriction between specific networks while keeping all other communication intact:

1. **Block HTTP Traffic:** Prevent all traffic originating from **LAN 1** (`192.168.1.0/24`) from reaching web services (HTTP / Port 80) on **LAN SV2** (`10.0.2.10/24`).
2. **Block PING Traffic:** Prevent all ICMP (Ping) traffic originating from **LAN 2** (`192.168.2.0/24`) from reaching **LAN SV1** (`10.0.1.10/24`).
3. **Permit All Other Traffic:** All other network segments must maintain seamless inter-VLAN and cross-WAN communication.


## Command Syntax
The general structure for an Extended ACL entry is:
**access-list [number] [permit|deny] [protocol] [source_address] [source_wildcard] [destination_address] [destination_wildcard] [operator] [port]**


## Configuration & Implementation

Extended ACLs should ideally be applied **as close to the source as possible** to prevent unwanted traffic from consuming network bandwidth across the WAN link.

### 1. Implementing ACL 101 (HTTP Restriction)
Applied on **Router 1** inbound on the `Gig0/0/0` interface:

```bash
Router1(config)# access-list 101 deny tcp 192.168.1.0 0.0.0.255 10.0.2.0 0.0.0.255 eq 80
Router1(config)# access-list 101 permit ip any any
Router1(config)# interface GigabitEthernet0/0/0
Router1(config-if)# ip access-group 101 in
```
### 2. Implementing ACL 102 (ICMP Restriction)
Applied on Router 1 inbound on the `Gig0/0/1` interface:
```bash
Router1(config)# access-list 102 deny icmp 192.168.2.0 0.0.0.255 10.0.1.0 0.0.0.255
Router1(config)# access-list 102 permit ip any any
Router1(config)# interface GigabitEthernet0/0/1
Router1(config-if)# ip access-group 102 in
```

## Show Configuration
