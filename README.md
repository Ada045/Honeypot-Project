# 🐝🕸 Honeynet Project on Microsoft Azure

## Global Threat Monitoring & Real-World Attack Analysis Using Microsoft Sentinel

### 🔎 Overview

This project demonstrates the design, deployment, and analysis of a cloud-based honeynet on Microsoft Azure. A Windows 10 virtual machine was deliberately exposed to the internet to study attacker behavior in real-time.

Security events were captured through Microsoft Sentinel, enriched with geolocation data, and visualized on an interactive global attack map. Within 48 hours, the environment generated over 65,000 logs, providing authentic data for SIEM analysis, threat intelligence, and incident response practice.


## 🧩 What is a Honeynet?

A **honeynet** is a decoy network intentionally designed to mimic real systems while remaining controlled and isolated. Its purpose is to observe and analyze malicious activity without risk to production systems.

This allows security analysts to:
- Observe malicious behavior safely
- Practice log analysis
- Studying attacker methods and timing
- Understand common brute-force and scanning activity
- Practicing SIEM log collection, querying, and visualization

---

## 🏗️ Architecture Overview

```
Azure Resource Group (Isolated)
│
├── Windows 10 VM (Honeypot)
│    ├── Public IP exposed
│    ├── RDP enabled to all IPs
│    ├── Windows Firewall disabled
│    └── Generates Event ID 4625 & 4624
│
├── Azure Virtual Network + Subnet
│
├── Log Analytics Workspace
│    └── Central collection of VM events
│
└── Microsoft Sentinel
     ├── Event ingestion
     ├── GeoIP enrichment
     └── Attack map visualization

```

This setup ensured controlled isolation while allowing authentic threat activity to be observed safely.

---

## 🧰 Technologies Used

### Cloud Infrastructure

- Microsoft Azure
- Azure Virtual Machines
- Azure Virtual Network (VNet)
- Network Security Groups (NSG)
- Azure Resource Groups

### Security & Monitoring

- Microsoft Sentinel (SIEM)
- Log Analytics Workspace (LAW)
- Windows Security Events via AMA
- Watchlists (GeoIP enrichment)
- Kusto Query Language (KQL)

### Data Tools

- CSV-based geolocation mapping
- Sentinel Attack Map Workbook
- map.json (Sentinel workbook import)

---

## 🎯 Objectives

The goals of this project were to:

- Deploy a cloud-hosted environment vulnerable by design
- Capture authentic attacker behavior
- Analyze logs at scale using Kusto Query Language (KQL)
- Enrich raw logs with geographic data
- Produce visual insights into global threat activity
- Practice SIEM workflows used in real SOC operations

## 🧪 Environment Setup & Exposure

The honeypot VM was intentionally configured to be vulnerable:

#### Firewall Disabled:
The Windows Firewall was turned off for all profiles via wf.msc.
<p align="center">
Firewall Disabled Screenshot:</br>
  <img src="https://imgur.com/wigIoLf.png" alt="map" />
</p>

#### RDP Exposure:
Remote Desktop Protocol was enabled for public access, allowing attackers to attempt login from anywhere.

#### Log Verification:
The Event Viewer confirmed incoming attack attempts before forwarding logs to Azure.
<p align="center">
Event Viewer Logs Screenshot:</br>
  <img src="https://imgur.com/Hf4K8W7.jpg" alt="map" />
</p>
---

## 📝 Log Collection & Analysis

The security logs were forwarded to a Log Analytics Workspace(LAG-soc-lab-001) in Microsoft Sentinel, where they were queried using KQL, revealing over 65,000 individual events.

#### The KQL query I used:
```
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Account, Activity , IPAddress
```
<p align="center">
Log Analytics Workspace Query Results:</br> 
  <img src="https://imgur.com/qf32nJn.jpg" alt="map" />
</p>


---

## 🌍 Log Enrichment & GeoIP Integration

Raw logs do not include geographic information, so I enriched attacker IP addresses using a 55,000-row GeoIP watchlist imported into Sentinel.

This allowed me to:

- Map each attacker to a specific city, region, and country
- Identify global attack patterns
- Group high-activity IP ranges
- Join geolocation data directly into KQL queries

KQL Querry that I used for enrichment: 

```
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
```
This transformed raw logs into meaningful threat intelligence.

---

## 🗺️ Visualization — Global Attack Map

To visualize attack patterns, I built a custom Sentinel Workbook using a JSON-based map configuration.
The map plotted each failed login attempt geographically, with clustering based on event volume.

This visualization made it immediately clear:

- Which regions were the most aggressive
- The distribution of global brute-force activity
- The intensity of scanning and probing within hours of exposure



<p align="center">
Attack Map Screenshot:</br>
  <img src="https://imgur.com/3lVmC4q.png" alt="map" />
</p>

---

## 📊Top Attacking Countries and Attempts

| Country                    | Attempts |
| -------------------------- | -------- |
| Netherlands                | 94.3K    |
| Poland                     | 32.9K    |
| Vietnam                    | 28.4K    |
| Switzerland                | 21.5K    |
| Greece                     | 19.5K    |
| South Africa               | 18.8K    |
| Germany                    | 15.6K    |
| China                      | 15.2K    |
| Other                      | 321K    |


Additional Observations
- Bots located the VM within minutes of exposure.
- Most attacks targeted RDP using automated credential guesses.
- Malicious activity originated from multiple continents simultaneously.
- Over 65,000 events were recorded, providing rich data for SIEM practice.
- Repeated IPs, timing of attacks, and attack clustering were analyzed.

--- 

## 🧠 Skills Demonstrated

### Cloud Security
- Azure architecture design
- Network Security Groups
- Resource isolation and sandboxing

### Threat Analysis
- Identification of brute-force and scanning activity
- Understanding attacker behavior in the wild
  
### SIEM & Log Analytics
- Microsoft Sentinel configuration
- Log Analytics Workspace management
- Watchlist creation and lookup operations
- KQL proficiency (joins, enrichments, aggregations)

### Visualization & Reporting
- Custom workbook building
- Attack map visualization
- Data correlation and summarization

---

## 📌 Summary

This project successfully demonstrates the end-to-end deployment of a honeynet, real-world data collection, security event enrichment, SIEM analysis, and threat visualization.
It replicates a realistic SOC workflow, involving:
- Log ingestion
- Querying
- Enrichment
- Interpretation
- Reporting

The final dataset and attack map provide a clear, visual story of global attack behavior observed in a 24-hour window.
