# Case Study 04: The LiteLLM Supply Chain Attack

**Date reported:** March 24, 2026 (new exposure analysis published August 11–13, 2026)
**Source:** [LiteLLM Security](https://docs.litellm.ai/blog/security-update-march-2026)
**Category:** Supply Chain / AI Infrastructure / Credential Theft
**In-Progress**

Background
A cybercriminal group called TeamPCP (tracked by [Google Threat Intelligence Group as code name UNC6780](https://cloud.google.com/blog/topics/threat-intelligence/mitigation-guidance-for-supply-chain-compromise)) attacked organisations for financial gain. 
LiteLLM is a free, open-source software tool that many companies use to connect their applications to AI models like ChatGPT or Claude. If a company wants their internal software to send requests to multiple AI providers, LiteLLM acts as the central point that handles those requests. Because it sits between an organisation's systems and their AI providers, it has access to a lot of sensitive credentials.
PyPI is the official public website where Python software packages are published and downloaded. When a developer runs pip install litellm, their computer goes to PyPI and downloads the LiteLLM package automatically. Millions of downloads happen from PyPI every day without manual review of each package.
Trivy is a free security scanning tool that developers use to check their own software for known vulnerabilities. LiteLLM's development team used Trivy as part of the process of building and releasing their software. Because Trivy was plugged into LiteLLM's internal build process, it had access to LiteLLM's account credentials on PyPI — the keys needed to publish new versions of the software.
TeamPCP did not attack LiteLLM directly. They attacked Trivy first, because compromising Trivy gave them access to LiteLLM's PyPI publishing account. From there, they could release a fake version of LiteLLM under the real project's name.
To publish a software package on PyPI, you need a username and password or an access token — just like logging into any account. Whoever holds those credentials can release a new version of that package, and it will appear legitimate to anyone who downloads it. TeamPCP stole those credentials through the compromised Trivy tool.
