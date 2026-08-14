# Case Study NN: [Incident Name]

**Date reported:** [Date]
**Source:** [Original reporting source(s) — link them]
**Category:** [e.g. Supply Chain / Zero-Day / Ransomware / Identity Security]

## Summary

2-4 sentences: what happened, who was affected, what the impact was. Plain language, no framework jargon yet — that comes next.

## Timeline

| Date | Event |
|---|---|
| | |

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| | | |

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
