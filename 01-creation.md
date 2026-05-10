# ① Detection Creation

> Translating adversary behavior into observable, monitorable behavior.

---

## What is Detection Creation?

Detection begins with a **threat hypothesis**. a theory about how an attacker would behave within your specific environment. Detection engineers translate adversary tradecraft into observable behaviors that can be monitored in available telemetry.

Sources for threat hypotheses include:

- Threat intelligence feeds (ISACs, vendor reports, CTI platforms)
- [MITRE ATT&CK](https://attack.mitre.org/) techniques and sub-techniques
- New enterprise solutions or integrations introducing new attack surfaces
- Incident retrospectives and purple team findings

Once the hypothesis is formed, it is converted into **detection logic**, rules, queries, or analytics that identify suspicious patterns in logs and telemetry. The goal is translating attacker behavior into operational detection logic that runs reliably at scale on available data sources.

---

## Query Languages by Platform

| Language | Platform | Notes |
|----------|----------|-------|
| **KQL** (Kusto Query Language) | Microsoft Sentinel, Defender XDR | Column-based, highly readable. Strong for cloud-native environments. |
| **SPL** (Search Processing Language) | Splunk | Pipeline-based. Mature ecosystem with a large community rule library. |
| **SQL** | BigQuery, Panther, various SIEMs | Familiar to data engineers. Common in Detection-as-Code workflows. |
| **YARA-L** | Chronicle (Google SecOps) | Event-sequence based, purpose-built for multi-event correlation. |

---

## Example Detection — Encoded PowerShell (KQL)

A common attacker technique is using Base64-encoded PowerShell commands to evade command-line logging. The following KQL query detects this pattern in Microsoft Sentinel or Defender XDR:

```kql
// Detects encoded PowerShell commands — a common LOLBin technique
DeviceProcessEvents
| where FileName == "powershell.exe"
| where ProcessCommandLine has_any ("-EncodedCommand", "-enc", "-e ")
| where InitiatingProcessFileName !in ("explorer.exe", "svchost.exe")
| project Timestamp, DeviceName, AccountName,
          ProcessCommandLine, InitiatingProcessFileName
| limit 100
```

**Why this works:** Legitimate use of encoded PowerShell is rare outside of specific admin tooling. Filtering out known-good parent processes (like `explorer.exe`) reduces noise while preserving coverage for suspicious execution paths.

---

## Tips for Writing Good Detection Logic

- **Be specific about your data source**: know which logs you're querying and their coverage gaps
- **Map every detection to a MITRE ATT&CK technique**: this helps track coverage and prioritise gaps
- **Start narrow, tune wider**: it's easier to broaden a precise detection than suppress a noisy one
- **Document your hypothesis**: Engineers need to understand *why* the rule exists, not just what it does

---

## Next Step

→ [02-testing.md](./02-testing.md) — Validating your detection before it goes live
