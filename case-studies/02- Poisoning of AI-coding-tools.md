# Case Study 02: The Poisoning of AI Coding Tools

**Date reported:** Somewhere between January–February 2026
**Source:** CrowdStrike 2026 Threat Hunting Report
**Category:** Supply Chain

## Summary

A hacking group went after cryptocurrency and blockchain companies by tricking developers into opening trapped coding projects. They rode the wave of interest in AI coding tools, building projects for a popular AI-powered code editor that looked completely normal on the surface but had hidden malicious code buried inside. The moment a developer opened one of these projects, the malicious code ran on its own no clicking, no downloads, nothing else needed from the victim.


## Timeline

| Date | Event |
|---|---|
| Jan–Feb 2026 | Hackers runs a campaign targeting crypto/blockchain firms with trojanized project repositories |
| Ongoing during campaign | Repos are posted mainly on GitHub; at least one is shared directly through Telegram |
| On project open | Victim's IDE terminal or task runner auto-executes hidden commands, giving the attacker a foothold |

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Initial Access | Supply Chain Compromise (poisoned software dependencies) | T1195 |
| Execution | Malicious code triggered automatically by the IDE (via post-install hooks / config files) | T1059 (general scripting execution — exact sub-technique not specified in source) |

## Gap Analysis — why existing controls didn't stop this

- What security control category *should* have caught this (prevention / detection / response)?
- What assumption did the existing security stack make that turned out to be false?
- Was this a novel technique, or a known technique against an under-monitored asset class?

Close with one sentence stating the core false assumption, e.g.: *"The core assumption that failed: '...'"*

## Detection Sketch

Sigma-style or query pseudocode for what would catch this. Mark clearly whether this has been **tested** (link to `/labs/`) or is still **conceptual**.

```
title:
description:
logsource:
detection:
condition:
level:
```

## Lab Status

- [ ] Conceptual only (not yet tested)
- [ ] Tested against synthetic/sample data — link: `labs/...`
- [ ] Tested against real lab environment (e.g. Juice Shop/DVWA) — link: `labs/...`

## Lessons & Fixes

Numbered list, 3-5 items. Each should be a concrete control or process change — not "patch faster" or "train users" as the sole recommendation.

---
*Analysis based on public reporting from [source], [date]. Independent interpretation and detection sketch by the author.*
