# SOC Detection Lab using Elastic Stack

A home-lab Security Operations Center (SOC) environment built to practice log collection, detection engineering, and incident analysis using the Elastic Stack.

## Overview

This project simulates a small enterprise environment monitored by a SIEM, with the goal of understanding how logs flow from an endpoint into a detection platform — and eventually, how attacks show up in that data.

## Architecture

```
Windows 11 (Sysmon + Winlogbeat)
        │
        │  Security / System / Sysmon logs
        ▼
Elasticsearch (Ubuntu) ──► Kibana (Ubuntu)
        ▲
        │  simulated attacks
        │
      Kali Linux
```

## Environment

| Component | Role | Version |
|---|---|---|
| Ubuntu VM | SIEM (Elasticsearch + Kibana) | 8.19.16 |
| Windows 11 VM | Log source (Sysmon + Winlogbeat) | Winlogbeat 9.4.2 |
| Kali Linux VM | Attacker machine | — |

Platform: VMware Workstation, isolated internal network.

## Project Phases

| Phase | Description | Status |
|---|---|---|
| 1 | [ELK Stack + Winlogbeat Setup](docs/01-elk-setup.md) — log collection foundation | ✅ Complete |
| 2 | Attack Simulation (Kali) — port scanning, brute force, detection validation | 🔜 In progress |
| 3 | Detection Rules & Alerting | ⏳ Planned |

## Skills Demonstrated

- SIEM deployment and configuration (Elasticsearch, Kibana)
- Windows log forwarding with Sysmon and Winlogbeat
- Log analysis and visualization (Kibana Discover, Lens, Dashboards)
- Home-lab network design with VMware

## Documentation

Full write-ups for each phase are in [`docs/`](docs/).
