# Hancorp Homelab

An isolated enterprise lab environment designed to emulate an Active Directory domain, simulate real-world attack vectors from Kali Linux, and analyze detection telemetry using Splunk and Wireshark.

---

## Lab Topology

* **Attacker Machine:** Kali Linux (Nmap, Metasploit, etc.)
* **Victim Workstation:** Windows 10/11 (Sysmon, Splunk Universal Forwarder)
* **Active Directory DC:** Windows Server (`hancorp.local`) (Sysmon, Splunk Universal Forwarder)
* **SIEM & Monitoring:** Splunk Enterprise (Docker)

---

## Core Security Stack

* **SIEM:** Splunk Enterprise (Host logs & Sysmon event ingestion)
* **Packet Analysis:** Wireshark & TShark (`.pcap` capture and inspection)
* **Endpoint Telemetry:** Microsoft Sysmon (Process creation, network connections)

---

## Write-Ups & Documentation

* [Network Topology & VM Setup](docs/network-topology.md)
* [Attack Simulation & PCAP Analysis](docs/attack-killchain.md)