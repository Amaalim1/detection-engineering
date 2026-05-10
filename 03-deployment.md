# ③ Detection Deployment

> Moving validated detections into production and integrating them into SOC workflows.

---

## What is Detection Deployment?

Once validated, detections are deployed into production monitoring systems. This means activating rules within your SIEM or detection platform and integrating them into SOC workflows so alerts route to the right analysts with appropriate context.

The goal is not just making the rule live — it's ensuring that when the rule fires, an analyst receives a meaningful, actionable alert with enough context to investigate efficiently.

---

## Deployment Patterns

| Pattern | Description | Best For |
|---------|-------------|----------|
| **Direct SIEM deployment** | Rules activated manually via SIEM UI | Smaller teams, less frequent changes |
| **Detection-as-Code (DaC)** | Rules stored in Git, deployed via CI/CD with automated testing gates | Mature engineering teams |
| **Platform APIs** | Programmatic deployment via SIEM REST APIs | Multi-tenant environments, MSPs |
| **Sigma + converter** | Write platform-agnostic rules, compile to target platform syntax | Teams operating across multiple SIEMs |

---

## Detection-as-Code

Detection-as-Code (DaC) treats detection rules the same way software engineers treat application code — stored in version control, reviewed via pull requests, tested automatically, and deployed through a pipeline.

**Benefits:**
- Full change history — you can see who changed what and when
- Peer review via pull requests before anything reaches production
- Rollback capability if a rule causes unexpected noise
- Automated regression testing before deployment
- Consistent rule formatting and documentation standards

**Common tooling:**
- Git (GitHub, GitLab) for version control
- [Sigma](https://github.com/SigmaHQ/sigma) for platform-agnostic rule format
- CI/CD pipelines (GitHub Actions, GitLab CI) for automated deployment
- Detection platforms with API support (Sentinel, Splunk, Chronicle)

---

## Common SIEM Platforms

- **Microsoft Sentinel** — cloud-native, KQL-based, strong Azure integration
- **Splunk Enterprise Security** — mature, flexible, large community rule library
- **IBM QRadar** — enterprise-focused, strong correlation engine
- **Elastic SIEM** — open-source friendly, good for custom log sources
- **Chronicle (Google SecOps)** — petabyte-scale, YARA-L rules, built-in threat intel

---

## Deployment Checklist

- [ ] Detection has passed testing and FP baselining
- [ ] Rule is documented with hypothesis, data source, and MITRE mapping
- [ ] Alert severity and priority are correctly configured
- [ ] SOC runbook or investigation guide is linked in the alert
- [ ] Alert routing is confirmed — the right team will receive it
- [ ] Rule is stored in version control (if using DaC)

---

## Next Step

→ [04-tuning.md](./04-tuning.md) — Keeping your detections accurate over time
