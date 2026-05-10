# 🛡️ Detection Engineering

> A structured guide to the detection lifecycle — from threat hypothesis to production alert.

![Detection Engineering](https://img.shields.io/badge/Blue%20Team-Detection%20Engineering-blue?style=flat-square)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red?style=flat-square)
![License](https://img.shields.io/badge/License-CC%20BY%204.0-green?style=flat-square)

---

## 📖 Overview

Detection engineering is the discipline of translating attacker behavior into reliable, low-noise alerts that drive meaningful SOC investigations. Unlike reactive security measures, it is a continuous, hypothesis-driven engineering practice, bridging threat intelligence, data engineering, and security operations.

This repository documents the full detection lifecycle and provides practical guidance on tooling, query languages, and common pitfalls.

---

## 🔄 The Detection Lifecycle

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   ① Creation  ──►  ② Testing  ──►  ③ Deployment  ──►  ④ Tuning │
│       ▲                                                    │    │
│       └────────────── Continuous Improvement ◄─────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## ① Detection Creation

Detection begins with a **threat hypothesis** — a theory about how an attacker would behave within your specific environment. Detection engineers translate adversary tradecraft into observable behaviors that can be monitored in available telemetry.

**Sources for threat hypotheses:**
- Threat intelligence feeds (ISACs, vendor reports, CTI platforms)
- [MITRE ATT&CK](https://attack.mitre.org/) techniques and sub-techniques
- New enterprise solutions/integrations introducing new attack surfaces
- Incident retrospectives and purple team findings

**The hypothesis is then converted into detection logic** — rules, queries, or analytics that identify suspicious patterns in logs. The goal is translating attacker behavior into operational detection logic that runs reliably at scale.

### Query Languages by Platform

| Language | Platform | Notes |
|----------|----------|-------|
| **KQL** (Kusto Query Language) | Microsoft Sentinel, Defender XDR | Column-based, highly readable. Strong for cloud-native environments. |
| **SPL** (Search Processing Language) | Splunk | Pipeline-based. Mature ecosystem with a large community rule library. |
| **SQL** | BigQuery, Panther, various SIEMs | Familiar to data engineers. Common in Detection-as-Code workflows. |
| **YARA-L** | Chronicle (Google SecOps) | Event-sequence based, purpose-built for multi-event correlation. |

**Example — KQL detection for encoded PowerShell (LOLBin technique):**

```kql
// Detects encoded PowerShell commands — a common living-off-the-land technique
DeviceProcessEvents
| where FileName == "powershell.exe"
| where ProcessCommandLine has_any ("-EncodedCommand", "-enc", "-e ")
| where InitiatingProcessFileName !in ("explorer.exe", "svchost.exe")
| project Timestamp, DeviceName, AccountName, ProcessCommandLine, InitiatingProcessFileName
| limit 100
```

---

## ② Detection Testing

Before deployment, detections must be validated to ensure they trigger on real attack behavior while avoiding excessive false positives.

### Testing Approaches (by increasing fidelity)

| Method | Description | Tooling |
|--------|-------------|---------|
| **Log replay** | Import historical logs from real incidents and confirm the detection fires | SIEM search |
| **Atomic testing** | Execute individual techniques in isolation and check for the expected alert | [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) |
| **Purple team exercises** | Red team runs techniques live while blue team confirms detection in real time | Internal |
| **Full simulation** | Automated scenario execution across multiple techniques at scale | [AttackIQ](https://www.attackiq.com/), [Splunk Attack Range](https://github.com/splunk/attack_range) |

> ⚠️ **FP baselining matters as much as TP validation.** Run your detection against 30+ days of production data before deployment to understand the false positive rate. A detection generating 500 alerts/day is operationally worse than no detection at all.

---

## ③ Detection Deployment

Once validated, detections are deployed into production monitoring systems and integrated into SOC workflows so alerts route to the right analysts with appropriate context.

### Deployment Patterns

| Pattern | Description | Best For |
|---------|-------------|----------|
| **Direct SIEM deployment** | Rules activated manually via SIEM UI | Smaller teams, less frequent changes |
| **Detection-as-Code (DaC)** | Rules stored in Git, deployed via CI/CD with automated testing gates | Mature engineering teams |
| **Platform APIs** | Programmatic deployment via SIEM REST APIs | Multi-tenant environments, MSPs |
| **Sigma + converter** | Write platform-agnostic rules, compile to target platform syntax | Teams operating across multiple SIEMs |

**Common platforms:**
- Splunk Enterprise Security
- Microsoft Sentinel
- IBM QRadar
- Elastic SIEM
- Chronicle (Google SecOps)

> ✅ **Detection-as-Code is the gold standard.** Storing detections in version control gives you change history, peer review via PRs, rollback capability, and the ability to run automated regression tests before any change reaches production.

---

## ④ Detection Tuning

After deployment, detections must be continuously refined to improve accuracy and reduce operational noise. An untuned detection degrades SOC efficiency over time as environments change and attacker TTPs evolve.

### Common Tuning Actions

- **Threshold adjustment** — Raise or lower count-based thresholds based on observed baseline activity
- **Allow-listing** — Add known-good infrastructure (IT tooling, scan assets, trusted automation) as exclusions
- **Context enrichment** — Join alert data with asset inventory, user roles, or threat intel to reduce investigation time
- **Risk scoring** — Weight alerts by asset criticality or user risk score to prioritize high-value signals
- **Correlation rules** — Combine multiple lower-fidelity detections into higher-confidence composite alerts

**Tooling:** SIEM analytics dashboards, alert triage queues, SOAR platforms

---

## 🆚 Threat Scenarios vs. Detection Use Cases

These two terms are frequently confused but operate at different levels of abstraction.

| | Threat Scenario | Detection Use Case |
|---|---|---|
| **Level** | Strategic / narrative | Technical / operational |
| **Purpose** | Describes *how* a threat could unfold | Defines *what* to detect and when |
| **Audience** | Security leaders, threat intelligence analysts | Detection engineers, SOC analysts |
| **Output** | Detection coverage map, priority matrix | SIEM rule, query, or analytic |

**Example Threat Scenario:**
> "A threat actor gains initial access via phishing, escalates privileges using a local exploit, then moves laterally to reach the domain controller."

**Example Detection Use Case derived from the above:**
> "Alert when a non-admin process creates a scheduled task that runs a Base64-encoded PowerShell command within 30 minutes of a new user logon event."

Each threat scenario should generate one or more use cases. A single scenario like *"credential theft via LSASS dumping"* might produce use cases for process injection patterns, LSASS access anomalies, and unusual outbound connections to harvesting infrastructure.

---

## 📚 Essential Resources

| Resource | Description |
|----------|-------------|
| [Awesome Detection Engineering](https://github.com/infosecB/awesome-detection-engineering) | Curated list of resources, tools, blog posts, and frameworks |
| [SigmaHQ / sigma](https://github.com/SigmaHQ/sigma) | Generic signature format — write once, deploy to any SIEM |
| [Sentinel-Queries](https://github.com/reprise99/Sentinel-Queries) | Community KQL query library for Microsoft Sentinel |
| [The Zen of Security Rules](https://br0k3nlab.com/resources/zen-of-security-rules/) | Principles for writing effective, maintainable detection rules |
| [MITRE ATT&CK](https://attack.mitre.org/) | Adversary tactics and techniques knowledge base |
| [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) | Library of small, focused tests mapped to ATT&CK |
| [Splunk Attack Range](https://github.com/splunk/attack_range) | Automated lab environment for detection testing |

---

## 📄 License

This content is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — feel free to share and adapt with attribution.
