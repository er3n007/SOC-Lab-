# 🛡️ Mini SOC Lab

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&size=22&duration=3000&pause=900&color=00F7A5&center=true&vCenter=true&width=650&lines=Security+Operations+Center;Windows+Event+Monitoring;Wazuh+%7C+Sysmon+%7C+Active+Directory;Detect.+Investigate.+Respond." alt="Animated SOC introduction" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Focus-SOC%20%26%20Blue%20Team-00b894?style=for-the-badge&logo=shield&logoColor=white" alt="SOC and Blue Team" />
  <img src="https://img.shields.io/badge/SIEM-Wazuh-4C9AFF?style=for-the-badge&logo=wazuh&logoColor=white" alt="Wazuh" />
  <img src="https://img.shields.io/badge/Endpoint-Sysmon-6C5CE7?style=for-the-badge&logo=windows&logoColor=white" alt="Sysmon" />
  <img src="https://img.shields.io/badge/Environment-Home%20Lab-2d3436?style=for-the-badge&logo=proxmox&logoColor=white" alt="Home lab" />
</p>

<p align="center">
  <i>A hands-on Security Operations Center lab focused on endpoint telemetry, centralized log collection, and security monitoring.</i>
</p>

---

## `01` — Overview

This project explores how a small SOC environment can collect Windows security events, centralize endpoint telemetry, and provide visibility through a SIEM dashboard.

The goal is to understand the complete detection pipeline — from generating endpoint events to collecting and investigating them in a centralized security platform.

## `02` — Lab Architecture

```text
                  ISOLATED LAB NETWORK
                           |
           +---------------+---------------+
           |               |               |
      +---------+    +-------------+   +-----------+
      |  KALI   |    | WINDOWS AD  |   |  WAZUH    |
      |  LINUX  |    | DOMAIN CTRL |-->|   SIEM    |
      +---------+    +-------------+   +-----------+
                           |                ^
                     +-----------+          |
                     |  SYSMON   |          |
                     |  SECURITY |----------+
                     |   LOGS    |  Wazuh Agent
                     +-----------+
```

### Components

| Component | Purpose |
|---|---|
| Wazuh | Centralized security monitoring and alerting |
| Windows Server | Active Directory domain controller |
| Sysmon | Detailed Windows process and system telemetry |
| Wazuh Agent | Forwards endpoint events to the manager |
| Kali Linux | Controlled security testing and event generation |

**Environment:** `lab.local` — isolated Active Directory lab domain.

## `03` — Technology Stack

<p>
  <img src="https://img.shields.io/badge/Wazuh-SIEM-4C9AFF?style=flat-square" alt="Wazuh" />
  <img src="https://img.shields.io/badge/Sysmon-Telemetry-6C5CE7?style=flat-square" alt="Sysmon" />
  <img src="https://img.shields.io/badge/Active%20Directory-Windows-0078D4?style=flat-square&logo=windows" alt="Active Directory" />
  <img src="https://img.shields.io/badge/Kali%20Linux-Testing-557C94?style=flat-square&logo=kalilinux" alt="Kali Linux" />
  <img src="https://img.shields.io/badge/Windows%20Event%20Logs-Analysis-0078D4?style=flat-square" alt="Windows Event Logs" />
  <img src="https://img.shields.io/badge/Networking-Lab%20Infrastructure-00B894?style=flat-square" alt="Networking" />
</p>

## `04` — Implementation Highlights

- Configured a Windows Server Active Directory environment using the `lab.local` domain.
- Integrated Sysmon for enhanced Windows endpoint telemetry.
- Configured the Wazuh Agent to forward Windows Security and Sysmon event channels.
- Troubleshot virtual machine networking and connectivity between the endpoint and SIEM.
- Investigated unexpected domain controller shutdowns using Windows System event logs.
- Verified that endpoint events reached the Wazuh dashboard.

## `05` — Repository Structure

```text
mini-soc-lab/
├── docs/
│   └── build-notes.md
├── sysmon-config/
│   └── README.md
├── screenshots/
│   └── README.md
└── README.md
```

*The tree illustrates the intended organization; filenames may differ from the current repository.*

| Directory | Description |
|---|---|
| `docs/` | Lab setup notes and troubleshooting |
| `sysmon-config/` | Sysmon configuration and integration notes |
| `screenshots/` | Sanitized dashboard screenshots |

## `06` — Learning Roadmap

- [x] Set up the Active Directory lab domain
- [x] Integrate Sysmon and the Wazuh Agent
- [x] Configure Windows event collection
- [x] Verify endpoint telemetry in the SIEM
- [ ] Generate controlled attack simulations
- [ ] Investigate and document security alerts
- [ ] Develop and test custom detection rules
- [ ] Document incident investigation workflows



## `07` — Security Disclaimer

This project is intended for educational purposes in an isolated, controlled lab environment. Security testing should only be performed on systems for which authorization has been granted.

No real credentials, private keys, or sensitive organizational information should be committed to this repository.

---

<p align="center">
  <img src="https://img.shields.io/badge/LEARN-01-00B894?style=flat-square" alt="Learn" />
  <img src="https://img.shields.io/badge/DETECT-02-0984E3?style=flat-square" alt="Detect" />
  <img src="https://img.shields.io/badge/INVESTIGATE-03-6C5CE7?style=flat-square" alt="Investigate" />
</p>

<p align="center">
  <i>Building practical defensive security skills, one investigation at a time.</i>
</p>
