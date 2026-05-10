# ② Detection Testing

> Validating that detections fire on real attack behavior — and not on everything else.

---

## Why Testing Matters

Before deployment, detections must be validated to ensure they trigger on real attack behavior while avoiding excessive false positives. A detection that fires too broadly creates alert fatigue; one that fires too narrowly misses real threats.

Both failure modes are costly:
- **Too many alerts** → analysts stop investigating, real incidents get buried
- **Too few alerts** → attackers operate undetected inside your environment

---

## Testing Approaches

Listed in order of increasing fidelity:

### 1. Log Replay
Import historical logs from real incidents or public breach datasets and confirm the detection fires at the expected rate. Good for a quick sanity check before deeper validation.

### 2. Atomic Testing
Execute individual adversary techniques in a controlled environment and check whether the expected alert fires. [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) provides a library of small, focused tests mapped to MITRE ATT&CK — making it easy to test one technique at a time without running a full attack chain.

### 3. Purple Team Exercises
The red team runs techniques live while the blue team confirms detections fire in real time. This allows immediate feedback loops and is especially useful for identifying gaps in log coverage that aren't apparent from the rule alone.

### 4. Full Simulation Platforms
Tools like [AttackIQ](https://www.attackiq.com/) and [Splunk Attack Range](https://github.com/splunk/attack_range) automate scenario execution at scale, allowing you to validate dozens of detections against realistic attack sequences without manual effort.

---

## FP Baselining

> ⚠️ **False positive baselining matters as much as true positive validation.**

Before deploying any detection, run it against **30+ days of production data** to understand its false positive rate in your environment. A detection generating 500 alerts/day is operationally worse than no detection at all — it trains analysts to ignore alerts.

Questions to answer during baselining:

- How many alerts does this fire per day on current production data?
- Which assets, users, or processes are the noisiest false positive sources?
- Can those sources be excluded without losing meaningful coverage?
- What is the expected true positive rate based on threat intelligence?

---

## Testing Checklist

- [ ] Detection fires on at least one known-good sample of the target technique
- [ ] Detection does not fire on at least 5 known-benign samples
- [ ] FP rate over 30 days of production data is acceptable for the team's capacity
- [ ] Detection is mapped to a MITRE ATT&CK technique
- [ ] Logic has been peer-reviewed by at least one other engineer

---

## Next Step

→ [03-deployment.md](./03-deployment.md) — Getting your validated detection into production
