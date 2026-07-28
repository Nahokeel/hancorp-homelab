# Hancorp Homelab
![HanCo Logo](hanco-logo.png)
An isolated enterprise lab environment designed to emulate an Active Directory domain, simulate real-world attack vectors from Kali Linux, and analyze detection telemetry using Splunk and Wireshark.

---

## Lab Topology

* **Attacker Machine:** Kali Linux (Nmap, Metasploit, etc.)
* **Victim Workstation:** Windows 10 Pro 
    * *Agents:* Sysmon, Splunk Universal Forwarder
* **Active Directory DC:** Windows Server 2022 ('HANCORP-DC01' / 'hancorp.local')
    * *Agents:* Sysmon, Splunk Universal Forwarder
* **SIEM & Monitoring:** Splunk Enterprise ('HANCORP-SIEM' via Docker)

---

## Core Security Stack

* **SIEM:** Splunk Enterprise (Windows Event Logs & Sysmon ingestion)
* **Packet Analysis:** Wireshark & TShark ('.pcap' capture and deep packet inspection)
* **Endpoint Telemetry:** Microsoft Sysmon (Process creation, network connections, memory access)

---

## Documentation

* [Network Topology & VM Setup](docs/setup.md)
* [Attack Simulation & PCAP Analysis](docs/attack-killchain.md)