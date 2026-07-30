# Hancorp Homelab

<p align="center">
  <img src="hanco-logo.png" alt="HanCo Logo" width="200">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Active%20Directory-Windows%20Server%202022-0078D4?style=for-the-badge&logo=windows-server&logoColor=white" alt="Active Directory Badge">
  <img src="https://img.shields.io/badge/SIEM-Splunk%20Enterprise-FF6900?style=for-the-badge&logo=splunk&logoColor=white" alt="Splunk Badge">
  <img src="https://img.shields.io/badge/Offensive-Kali%20Linux-555555?style=for-the-badge&logo=kali-linux&logoColor=white" alt="Kali Linux Badge">
  <img src="https://img.shields.io/badge/Virtualization-VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white" alt="VirtualBox Badge">
  <img src="https://img.shields.io/badge/Container-Docker%20Desktop-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker Badge">
</p>

An isolated enterprise lab environment designed to emulate an Active Directory domain, simulate real-world attack vectors from Kali Linux, and analyze detection telemetry using Splunk and Wireshark.

**Purpose:** Built to practice Active Directory administration, offensive security fundamentals (Red Team tactics), and detection engineering (Blue Team SOC operations) in a legal, fully isolated environment.

---

## Lab Architecture

```mermaid
flowchart TD
    subgraph Host["Host System (Docker Desktop)"]
        SIEM["HANCORP-SIEM<br/>(Splunk Enterprise)"]
    end

    subgraph LabNet["Isolated NAT Network (192.168.50.0/24)"]
        Attacker["KALI<br/>(Kali Linux)"]
        Workstation["HANCORP-PC01<br/>(Windows 10 Pro)"]
        DC["HANCORP-DC01<br/>(Windows Server 2022)"]
    end

    %% Attack vectors
    Attacker -- "1. Recon & Initial Exploitation" --> Workstation
    Attacker -. "2. Credential Theft & Lateral Movement" .-> DC

    %% Telemetry pipeline
    Workstation -- "Sysmon & Event Logs" --> SIEM
    DC -- "Sysmon & Event Logs" --> SIEM

    %% Host-to-lab bridge
    Host -. "Bridged Adapter" .-> LabNet
```

---

## Core Security Stack

* **SIEM:** Splunk Enterprise (Windows Event Logs & Sysmon ingestion)
* **Packet Analysis:** Wireshark & TShark (`.pcap` capture and deep packet inspection)
* **Endpoint Telemetry:** Microsoft Sysmon (Process creation, network connections, memory access)

---

## Lab Documentation

* **[Lab Architecture & VM Setup](docs/setup.md)** 
* **[Chapter 1: Brute Force Attack](docs/simulation/01-brute-force.md)**