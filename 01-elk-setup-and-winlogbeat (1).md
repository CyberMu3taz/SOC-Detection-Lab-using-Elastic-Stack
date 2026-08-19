# Phase 1: ELK Stack + Winlogbeat Setup (Windows Log Collection)

## Objective
Build the foundational log collection layer of a home-lab SOC: Elasticsearch + Kibana running on Ubuntu, ingesting Windows Security and Sysmon events via Winlogbeat.

## Environment

| Component | Role | Version |
|---|---|---|
| Ubuntu VM | SIEM (Elasticsearch + Kibana) | 8.19.16 |
| Windows 11 VM | Log source (Sysmon + Winlogbeat) | Winlogbeat 9.4.2 |
| Kali VM | Attacker machine (Phase 2) | — |

Platform: VMware Workstation, isolated internal network.

## Architecture

```
Windows 11 (Sysmon + Winlogbeat)
        │
        │  Security / System / Sysmon logs
        ▼
Elasticsearch (Ubuntu) ──► Kibana (Ubuntu)
```

## What Was Built

- Elasticsearch and Kibana deployed on Ubuntu, secured with HTTPS and authentication
- Sysmon and Winlogbeat installed and configured on the Windows host to forward Security, System, and Sysmon Operational logs
- A Kibana Data View (`winlogbeat-*`) indexing 1,846 fields across all log sources
- Verified live event ingestion through Kibana Discover — hundreds of real-time events per 15-minute window (process creation, logons, credential access, etc.)
- Built the first Kibana Dashboard (**SOC Homelab**) with a visualization breaking down event types by frequency, establishing a baseline of normal system activity

![Final dashboard](../screenshots/06-final-dashboard.png)

## Outcome

- Elasticsearch + Kibana operational and reachable
- Windows host actively shipping Security/Sysmon telemetry
- First working dashboard providing visibility into system activity

## Next Phase
Use the Kali VM to simulate basic attack activity (e.g., port scanning, failed logon attempts) and verify detection in this same dashboard — moving from passive log collection to active threat detection.
