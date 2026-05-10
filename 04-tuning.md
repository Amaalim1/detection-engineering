# ④ Detection Tuning

> Continuously refining detections to improve accuracy and reduce operational noise.

---

## Why Tuning is Continuous

Deployment is not the end of the detection lifecycle, it is the beginning of the operational phase. After going live, detections must be continuously refined as:

- Your environment changes (new tools, new infrastructure, new user behaviours)
- Attacker TTPs evolve and detection logic becomes stale
- Analysts surface false positives that weren't caught during baselining
- New log sources become available that improve detection fidelity

An untuned detection degrades SOC efficiency over time. Alert fatigue is one of the biggest operational risks in a detection programme and it is almost always caused by poorly tuned detections.

---

## Common Tuning Actions

### Threshold Adjustment
Raise or lower count-based thresholds based on observed baseline activity. For example, if a detection fires when a user authenticates from more than 3 countries in 24 hours, but your sales team routinely travels internationally, raising the threshold or scoping it to non-sales accounts reduces noise without losing coverage.

### Allow-listing
Add known-good infrastructure as exclusions, IT tooling, vulnerability scanners, trusted automation accounts, and monitoring agents are common sources of false positives. Be precise with exclusions: exclude by specific asset name or service account rather than broad IP ranges where possible.

### Context Enrichment
Join alert data with asset inventory, user roles, or threat intelligence to reduce analyst investigation time. An alert that includes the asset's business owner, the user's department, and whether the IP appears on a threat feed is far more actionable than raw event data alone.

### Risk Scoring
Weight alerts by asset criticality or user risk score. A lateral movement alert on a domain controller is more urgent than the same alert on a developer workstation risk scoring helps analysts prioritise without manually triaging every alert.

### Correlation Rules
Combine multiple lower-fidelity detections into a single higher-confidence composite alert. For example, a single failed login is noise; failed logins from 10 different accounts on the same source IP within 5 minutes is a meaningful signal. Correlation turns weak individual signals into strong composite detections.

---

## Measuring Detection Health

Track these metrics per detection to identify what needs tuning:

| Metric | What it tells you |
|--------|-------------------|
| **Alerts per day** | High volume = likely needs threshold or exclusion tuning |
| **True positive rate** | Low rate = detection may be too broad or hypothesis is wrong |
| **Mean time to close** | High MTTC = alert lacks context or is too noisy to investigate properly |
| **Analyst feedback** | Direct signal from the people working the alerts daily |

---

## Tuning Checklist

- [ ] Review alert volume weekly for the first month after deployment
- [ ] Collect analyst feedback after every 20 alerts worked
- [ ] Document all exclusions added and the reason for each
- [ ] Re-test detection after any significant tuning change
- [ ] Review all active detections at least quarterly for staleness

---

## Back to Start

Tuning insights often generate new hypotheses a pattern of false positives may reveal a gap in your detection logic that points to a more precise, higher-fidelity approach.

→ [01-creation.md](./01-creation.md)  Start a new detection cycle
