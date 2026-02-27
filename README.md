# SOC MONITORING & THREAT DETECTION LAB

## PROJECT OVERVIEW

This project showcases the design, implementation, and monitoring of a fully isolated **SOC Home Lab** built to simulate real-world cyber attack scenarios  and monitor them using a SIEM solution i.e, **Splunk Enterprise**.

The lab was created to gain hands-on experience in:

- Log collection and analysis
- Threat detection
- Network monitoring
- Attack simulation
- Writing detection queries

---
## LAB ARCHITECTURE

The environment is built on a **Host-Only Network** to ensure total isolation from the internet while allowing inter-VM communication

1️⃣**Windows 10 VM(Victim):** Serves as the primary log source.

2️⃣**Kali Linux VM(Attacker):** Used to simulate various stages of the Cyber Kill Chain

3️⃣**Splunk Enterprise VM (SIEM):** Acts as the central nervous system for log ingestion, parsing, and dashboarding.

![Architecture](images\LabArchitecture.png)

---
**NETWORK CONFIGURATION**

- Internal subnet: 192.168.x.x 

- Static / DHCP-based IP configuration

- Isolated lab environment (no internet exposure)
--- 

## TOOLS & TECHNOLOGIES USED:

| Category | Tools / Skills |
| :--- | :--- |
| **SIEM / Logging** | Splunk Enterprise, SPL (Search Processing Language), Sysmon |
| **Endpoint** | Windows Event Logs (Security, System, Application), PowerShell |
| **Network Security** | Nmap, TCP/IP Analysis, Port Scanning |
| **Virtualization** | Oracle VM VirtualBox |
| **OS** | Windows 10, Kali Linux, Ubuntu (Splunk Server) |

---

## ATTACK SIMULATION PERFORMED

 ### 1️⃣ Reconnaissance: Port Scanning (**T1595**)
* **Action:** Conducted `nmap -sV -T4` from Kali Linux to identify open services on the Windows target.
* **Splunk Detection:** Identified high-frequency TCP connection attempts across multiple ports from a single source IP.
* **SPL Query:**
    index=network sourcetype="stream:tcp" 
    | stats count distinct_ports as dest_port by src_ip 
    | where distinct_ports > 20

### 2️⃣ Credential Access: Brute Force (**T1110**)
* **Action:** Simulated multiple failed RDP/Local login attempts.
* **Splunk Detection:** Monitored **Event ID 4625** (An account failed to log on).
* **SPL Query:**
    index=wineventlog EventCode=4625 
    | stats count by TargetUserName, src_ip 
    | where count > 5
    
### 3️⃣ Persistence/Execution: Suspicious Process Creation (**T1059**)
* **Action:** Executed unauthorized PowerShell scripts and command-line tools.
* **Splunk Detection:** Leveraged **Sysmon Event ID 1** for granular process tracking.
* **SPL Query:**
    index=wineventlog source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 
    | table _time, ParentImage, Image, CommandLine

--- 

## PROJECT VISUALIZATION 

### 1. Virtualization Environment
![Lab environment](images\environment.png)
*Snapshot of the Oracle VM VirtualBox manager showing the three-tier lab architecture (Attacker, Victim, SIEM) running on an isolated host-only network.*


### 2. Network Reconnaissance
![Nmap Scan Result](images\kali.png)
*Executing a Nmap stealth scan to identify open ports on the target Windows machine.*

### 3. Local Security Logs
![Windows Event Viewer](images\Windows.png)
*Viewing Event ID 4625 (An account failed to log on) within the Windows Event Viewer. This confirms that the OS correctly logged the brute-force attempts locally before forwarding them to Splunk.*

### 4. Security Analytics Dashboard
![Splunk Dashboard](images\Dashboard.jpeg)
 *A custom **Splunk Audit Trail Dashboard** visualizing user activities and object modifications.*

--- 



##  Key Learning Outcomes
* **SIEM Mastery:** Gained proficiency in Splunk ingestion pipelines and complex SPL querying.
* **Endpoint Visibility:** Learned how to fine-tune Sysmon for deep visibility into Windows internals.
* **Incident Investigation:** Developed the ability to correlate disparate logs to reconstruct an attack timeline.
* **Network Security:** Understand how different scanning flags (SYN, Stealth) appear within network traffic logs.

---


##  Future Improvements
* **SOAR Integration:** Implement automated responses using Splunk Phantom or Tines.
* **Advanced Threat Hunting:** Incorporate Sigma rules and YARA for proactive detection.
* **Cloud Logging:** Expand the lab to ingest logs from AWS CloudTrail or Azure Monitor.
* **Dashboarding:** Create a real-time SOC Analyst dashboard for high-level threat visualization.

---


**Author:** Deepsikha Singh  

*Aspiring SOC Analyst | Cybersecurity Enthusiast*
