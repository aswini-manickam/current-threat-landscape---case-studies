# Case Study 04: The LiteLLM Supply Chain Attack

**Date reported:** March 24, 2026 (new exposure analysis published August 11–13, 2026)
**Source:** [LiteLLM Security](https://docs.litellm.ai/blog/security-update-march-2026)
**Category:** Supply Chain / AI Infrastructure / Credential Theft
**In-Progress**

## Summary
In March 2026, financially motivated threat actor TeamPCP hijacked Trivy (open-source vulnerability scanner), to steal LiteLLM's PyPI publishing credentials and push two malicious releases to a package downloaded 3.4 million times per day. The packages were live for roughly 40 minutes on March 24, but August 2026 analysis overturned the original timeline - 95% of the 2,500+ affected organisations were already exposed from a five-day collection run that began the moment Trivy was compromised on March 19. The credential stealer SANDCLOCK swept cloud keys, Kubernetes tokens, SSH keys, database passwords, and AI provider API keys from every host where an affected Python environment ran. Stolen credentials were subsequently brokered on Telegram and linked to the Vect ransomware affiliate programme.

---

## Background
### Who did this and why
A cybercriminal group called TeamPCP (tracked by [Google Threat Intelligence Group as code name UNC6780](https://cloud.google.com/blog/topics/threat-intelligence/mitigation-guidance-for-supply-chain-compromise)) attacked organisations for financial gain. 
### What LiteLLM is
[LiteLLM](https://www.litellm.ai/) is a free, open-source software tool that many companies use to connect their applications to AI models like ChatGPT or Claude. If a company wants their internal software to send requests to multiple AI providers, LiteLLM acts as the central point that handles those requests. Because it sits between an organisation's systems and their AI providers, it has access to a lot of sensitive credentials.
### What PyPI is
[PyPI](https://pypi.org/) is the official public website where Python software packages are published and downloaded. When a developer runs pip install litellm, their computer goes to PyPI and downloads the LiteLLM package automatically. Millions of downloads happen from PyPI every day without manual review of each package.
### What Trivy is and why it was targeted first ?
[Trivy](https://trivy.dev/) is a free security scanning tool that developers use to check their own software for known vulnerabilities. LiteLLM's development team used Trivy as part of the process of building and releasing their software. Because Trivy was plugged into LiteLLM's internal build process, it had access to LiteLLM's account credentials on PyPI the keys needed to publish new versions of the software.
TeamPCP did not attack LiteLLM directly. They attacked Trivy first, because compromising Trivy gave them access to LiteLLM's PyPI publishing account. From there, they could release a fake version of LiteLLM under the real project's name.
### What "publishing credentials" means
To publish a software package on PyPI, you need a username and password or an access token just like logging into any account. Whoever holds those credentials can release a new version of that package, and it will appear legitimate to anyone who downloads it. TeamPCP stole those credentials through the compromised Trivy tool.
### What happened NEXT?
TeamPCP used the stolen credentials to upload two fake, malicious versions of LiteLLM to PyPI - versions 1.82.7 and 1.82.8. These versions looked legitimate. Any developer or automated system that ran pip install litellm during that window received the malicious version automatically, without any warning. The packages were taken down about 40 minutes later.
LiteLLM is downloaded approximately 3.4 million times per day. At that volume, 40 minutes of a malicious version being available is enough for a very large number of automated systems to pull it down.
### The recent Analysis made in August
The August 2026 analysis showed that the 40-minute window was not actually where most of the damage happened. The data collection started five days earlier, on March 19, the moment Trivy itself was compromised. The malicious code was already running inside other organisations' systems before the fake LiteLLM packages even appeared. By March 24, 95% of the affected organisations had already been hit.
What the malicious code actually did
#### Once installed on a computer or server, the malicious software quietly searched the system for saved passwords, access keys, and tokens. Specifically, it looked for:
  - Cloud keys - credentials for services like Amazon Web Services, Google Cloud, or Microsoft Azure. These let whoever holds them access or control an organisation's cloud servers and data.
  - SSH keys - private files used to log into servers remotely without a password.
  - Kubernetes tokens - credentials for managing containerised software infrastructure. Many companies run their applications inside containers managed by a - system called Kubernetes; access tokens let someone control those containers and move between them.
  - Database passwords - the login credentials needed to read or modify a company's databases.
  AI provider API keys - specifically the keys organisations use to authenticate with OpenAI (OPENAI_API_KEY) and Anthropic (ANTHROPIC_API_KEY). Holding these keys lets an attacker make requests to those AI services and charge the costs to the victim's account.

All of this was collected without the organisation knowing, because the malicious code was designed to run silently in the background.
### What happened to the stolen credentials
The stolen data was packaged up and sold. TeamPCP advertised the full collection - over 150 gigabytes compressed on Telegram, the messaging platform. A ransomware group called Vect then formally partnered with TeamPCP, meaning the stolen credentials were handed to criminals whose business model is breaking into organisations and demanding payment to restore access. At least one confirmed Vect ransomware attack was later traced back to credentials stolen in this campaign.

---
## Timeline
| Date | Event |
|---|---|
| Late Feb 2026 | An autonomous agent ([hackerbot-claw](https://www.stepsecurity.io/blog/hackerbot-claw-github-actions-exploitation)) exploits a `pull_request_target` misconfiguration in Trivy's GitHub Actions workflows, obtaining a privileged personal access token (PAT) |
| Feb 27–28 2026 | The credential rotates the affected personal access tokens (PAT), but the rotation is not atomic, its residual access survives [diagram: How credential rotation works](https://github.com/aswini-manickam/current-threat-landscape---case-studies/blob/main/case-studies/diagrams/credential%20rotation(1).png) |
| Mar 1 2026 | Attacker begins using the surviving access |
| Mar 19, 17:43 UTC | Attacker force-pushes 76 of 77 `trivy-action` tags and all 7 `setup-trivy` tags to malicious commits; spoofed maintainer identities used |
| Mar 19, 17:47 UTC | Malicious Trivy 0.69.4 builds and publishes to Docker Hub; collection activity begins 18 minutes later at 18:05 UTC |
| Mar 19, 22:42 UTC | The compromised PAT is revoked |
| Mar 22 | Malicious Trivy 0.69.5 and 0.69.6 images pushed to Docker Hub |
| Mar 23, 01:40 UTC | Malicious Docker Hub tags removed |
| Mar 24, 10:39 UTC | LiteLLM v1.82.7 published to PyPI (payload injected into `proxy_server.py`) |
| Mar 24, 10:52 UTC | LiteLLM v1.82.8 published 13 minutes later, adding `litellm_init.pth` a Python interpreter startup file that executes without any LiteLLM import |
| Mar 24, ~11:19 UTC | PyPI quarantines both releases (~40 minutes after the first went live) |
| Mar 24, ~16:00 UTC | End of LiteLLM's recommended audit window; persistence backdoor continues running on already-infected hosts |
