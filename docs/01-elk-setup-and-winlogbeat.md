# Phase 1: ELK Stack + Winlogbeat Setup (Windows Log Collection)

## Objective
Build the foundational log collection layer of a home-lab SOC: Elasticsearch + Kibana running on Ubuntu, ingesting Windows Security and Sysmon events via Winlogbeat.

## Environment

| Component | Role | OS / Version |
|---|---|---|
| Ubuntu VM | SIEM (Elasticsearch + Kibana) | Elasticsearch/Kibana 8.19.16 |
| Windows VM | Log source (Sysmon + Winlogbeat) | Winlogbeat 9.4.2 |
| Kali VM | Attacker machine (planned, Phase 2) | — |

Platform: VMware Workstation, isolated internal network.

---

## Problem 1 — No data reaching Elasticsearch

**Symptom:** Kibana's Index Management showed no indices at all.

![No indices](../screenshots/01-no-indices-problem.png)

**Diagnosis:** Checked the Winlogbeat service status on Windows — it was `Stopped`.

![Service stopped](../screenshots/02-service-stopped-diagnosis.png)

**Fix:** `Start-Service winlogbeat`

---

## Problem 2 — Confirming data was actually live

Rather than trust a single snapshot, I ran a controlled test: opened and closed Notepad on Windows, then watched the Kibana data stream's "Last updated" timestamp to confirm it moved forward in real time.

![Live data stream proof](../screenshots/03-datastream-live-proof.png)

This confirmed events were being shipped continuously, not just on service start.

---

## Problem 3 — Service didn't survive a VM reboot

**Symptom:** After restarting the Windows VM, the Winlogbeat service was `Stopped` again, despite being configured as `Auto` start.

![Reboot issue](../screenshots/05-reboot-service-issue.png)

**Diagnosis steps:**
- Checked Service Control Manager event logs at boot — no start attempt was logged, suggesting a silent failure early in boot (likely a race condition with the network stack not being ready yet).
- Attempted fix: changed start type to Delayed Auto Start (`sc.exe config winlogbeat start= delayed-auto`).
- Result: issue persisted.

**Status:** Documented as a **known limitation**. Current workaround: run `Start-Service winlogbeat` manually after each VM boot. Root cause likely relates to service dependency timing in a virtualized network — flagged for future investigation (e.g., explicit `sc.exe config winlogbeat depend=...` on the network service, or a scheduled task on boot).

**Side discovery:** While diagnosing this, found via `sc.exe qc winlogbeat` that the service's actual log path was `C:\Program Files\Winlogbeat-Data\Winlogbeat\logs` — not the install directory. This explained why log files appeared stale even while the service was shipping data correctly. **Lesson:** verify actual service configuration rather than assuming default paths.

---

## Result — Data exploration and first dashboard

Verified live Sysmon/Security events in Kibana Discover (839 Process Creation events in a 15-minute window):

![Discover events](../screenshots/04-discover-sysmon-events.png)

Built a Kibana Data View (`winlogbeat-*`, 1,846 fields) and created the first visualization — a breakdown of `event.action` showing the most frequent event types (process creation, logons, credential reads), giving a baseline of "normal" system behavior before attack simulation begins.

Saved as the **SOC Homelab** dashboard:

![Final dashboard](../screenshots/06-final-dashboard.png)

---

## Outcome Summary

- ✅ Elasticsearch + Kibana operational (HTTPS on 9200, HTTP on 5601)
- ✅ Winlogbeat shipping Sysmon/Security/System logs continuously
- ✅ Data View + first Dashboard built in Kibana
- ⚠️ Known issue: Winlogbeat service doesn't auto-start reliably after reboot (manual start required)

## Next Phase
Use the Kali VM to simulate basic attack activity (e.g., port scanning, failed logons) and verify detection in this same dashboard — moving from "data is flowing" to "the SOC actually detects something."
