# Case Study NN: [Incident Name]

**Date reported:** August 11, 2026

**Source:** [Wordfence (researcher Paolo Tresso), via The Hacker News](https://thehackernews.com/2026/08/bdthemes-supply-chain-attack-poisons.html)

**Category:** Supply Chain Compromise / Web Application Security

## Summary
[BdThemes](https://bdthemes.com/) makes popular WordPress plugins (Element Pack Addons has 100,000+ sites using it). Inside the plugin's admin dashboard, there's a small banner that shows promo messages like "Upgrade to Pro!" To display that banner, the plugin quietly fetches a small text file (JSON) from BdThemes' server every time an admin loads the dashboard.
Attackers didn't hack WordPress itself, and they didn't sneak bad code into the plugin files. Instead, they broke into wherever that little text file lives on BdThemes' server by poisoning a remote JSON data stream, and changed its contents.
Because the plugin automatically trusts and runs whatever comes back in that file, the attackers used it to smuggle in instructions. Those instructions told the plugin: "create a new admin account on this website." So on every site that loaded that dashboard banner, a fake administrator account got silently created handing the attackers full control of the site.
## Timeline

| Date | Event |
|---|---|
| Unknown | Attackers gain the ability to modify the remote JSON feed consumed by the promo banner |
| Unknown |	Poisoned JSON used to trigger rogue admin account creation on sites running affected plugins |
| Aug 11, 2026 | Wordfence publishes findings; WordPress.org disables downloads for affected plugins|

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

