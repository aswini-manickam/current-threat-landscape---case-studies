# Case Study 02: The Poisoning of AI Coding Tools
A bit of background about the casestudy before jumping to the gap analysis. detection sketch and lessons learn. 

**Date reported:** Somewhere between January-February 2026
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
| Execution | Malicious code triggered automatically by the IDE (via post-install hooks / config files) | T1059 (general scripting execution - exact sub-technique not specified in source) |

## Gap Analysis - why existing controls didn't stop this

**The control that should have caught this at enterprise level - code and dependency scanning before a project is ever opened, plus sandboxing of untrusted repos.
**But the question is "Can a normal user afford this?"**.No.
So lets dive into the actual root cause here
npm lifecycle scripts (postinstall, preinstall) running automatically with full user privilege. This has free fixes as well:
  - According to [Splunk- Defending Against npm Supply Chain Attacks: A Practical Guide to Detection, Emulation, and Analysis](https://www.splunk.com/en_us/blog/security/npm-supply-chain-attack-detection-analysis.html) and [OpenReplay](https://blog.openreplay.com/npm-supply-chain-defense/)  

 ```bash
    npm install --ignore-scripts
 ```

 ```bash
    # in .npmrc
    ignore-scripts=true 
 ```
These blocks postinstall hook since these hooks execute with full user privileges and can access SSH keys, cloud credentials, and every environment variable, with no confirmation prompt or sandbox. However, one fix is never enough ,There is a case-study where even with --ignore-scripts turned on, the Bitwarden attack still ran its bad code. The hackers used the bin part of package.json to hide a harmful file. It ran whenever a user typed the bw command. Yet good to have
 - According to an article [Malicious npm packages abuse dependency confusion to profile developer environments ](https://www.microsoft.com/en-us/security/blog/2026/05/29/33-malicious-npm-packages-abuse-dependency-confusion-profile-developer-environments/) Switching to pnpm, which disables automatic execution of postinstall scripts in dependencies by default, letting you explicitly allowlist only trusted packages.

**Some Free, open-source and small setup**
- Package-Inferno (Docker-based, open source) - scans a project for malicious lifecycle hooks before you ever run npm install. Free, self-hosted.
- Windows Sandbox (built into Windows 10/11 Pro, free) or a throwaway Docker container — open a suspicious project inside it first; if it does something bad, you just delete the container.
- GitHub's built-in Dependabot alerts - free on public repos, catches known-bad packages (won't catch a brand-new one, but it's free and already on).

**Free tier of a paid product (limits, but usable)**
- Socket.dev — free GitHub App tier that flags risky install-script behavior in pull requests. Good if you're collaborating with others, not just solo.
- According to [dev.to](https://dev.to/alanwest/why-npm-supply-chain-attacks-keep-happening-and-how-to-harden-your-installs-97p) Package-Inferno — a Docker-first, open-source scanner that performs static behavioral analysis to catch credential theft, data exfiltration, and malicious lifecycle hooks.
  
- **Novel or known attack and why it matters:** this classification isn't a fix, it's a routing question: known techniques mean detection rules likely already exist somewhere and just need adopting; novel techniques mean nobody has written that rule yet. Here, hiding commands in install scripts is a known technique meaning off-the-shelf tools (above) should already
  catch it, which raises the real question: why weren't they in use?

## Detection Sketch

Sigma-style or query pseudocode for what would catch this.

```
title: Suspicious child process spawned by IDE on project open
description: Detects an AI coding IDE process (or its integrated terminal)
  spawning shell/script interpreters shortly after a new project/workspace
  is opened, consistent with malicious post-install hooks or editor tasks.
logsource: endpoint process creation
detection:
  parent_process: IDE binary (e.g. editor.exe / editor terminal helper)
  child_process: cmd.exe, powershell.exe, bash, sh, node, python
  timing: within seconds of workspace/project open event
condition: parent_process AND child_process AND timing
level: medium (tune to reduce noise from legitimate build tasks)
```

## Lab Status

- [x] Conceptual only (not yet tested)
- [ ] Tested against synthetic/sample data
- [ ] Tested against real lab environment 

## Lessons & Fixes

- Disable automatic execution of post-install scripts and editor tasks for projects opened from untrusted or newly-cloned sources; require explicit approval first.
- Monitor and alert on IDE processes spawning shells, script interpreters, or network connections shortly after a project is opened.
- Scan repository config files (package.json, .vscode/settings.json, install hooks) for suspicious commands before allowing a project to be opened on a managed device.
- Isolate first-time-opened, externally-sourced projects in a sandbox or disposable environment, especially for high-value targets like crypto/blockchain teams.
- Treat links shared over chat apps (e.g. Telegram) pointing to code repos as high-risk and route them through a review process before any engineer opens them.

---
*Analysis based on public reporting from CrowdStrike 2026 Threat Hunting Report, August 2026. Independent interpretation and detection sketch by the author.*
