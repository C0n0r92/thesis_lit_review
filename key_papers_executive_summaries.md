# Executive Summaries: Key Papers for TrustBench Literature Review

These are the papers and reports that matter most for TrustBench. Each summary focuses on what I need for the thesis: specific numbers, attack mechanisms, and gaps this research fills.

---

## 1. Academic Papers - Attack Research

### 1.1 UntrustIDE: Exploiting Weaknesses in VS Code Extensions

**Citation**: Lin, E., Koishybayev, I., Dunlap, T., Enck, W., & Kapravelos, A. (2024). UntrustIDE: Exploiting Weaknesses in VS Code Extensions. NDSS 2024.

**Link**: https://www.ndss-symposium.org/ndss-paper/untrustide-exploiting-weaknesses-in-vs-code-extensions/

Distinguished paper award at NDSS 2024. The researchers analyzed 25,402 VS Code extensions and found 21 with verified exploits affecting 6 million users. They identified four untrusted input sources (workspace settings, files, user input, network data) and three injection targets. Workspace files are the primary attack vector. None of the 21 exploits were caught by marketplace scanning.

**Relevant for TrustBench**:
- Extensions run with full Node.js privileges, no sandbox
- Workspace files are the easiest vector to exploit
- Marketplace scanning catches 0% of sophisticated exploits
- This work focused on exploiting humans; TrustBench extends it to AI agents

---

### 1.2 "Your AI, My Shell": Prompt Injection on Agentic AI Editors

**Citation**: Liu, Y., Zhao, Y., Lyu, Y., Zhang, T., Wang, H., & Lo, D. (2025). "Your AI, My Shell": Demystifying Prompt Injection Attacks on Agentic AI Coding Editors.

**Link**: https://arxiv.org/abs/2509.22040

First empirical study of prompt injection against Cursor and GitHub Copilot. They achieved 41-84% attack success rate (ASR) depending on the LLM model. In auto-execution mode, ASR jumped to 66.9-84.1%. Their AIShellJack benchmark contains 314 payloads covering 70 MITRE ATT&CK techniques.

**Relevant for TrustBench**:
- The 84% ASR is the baseline I compare against
- Their ASR definition (AI executes malicious intent, regardless of full effectiveness)
- They rely on linguistic tricks; TrustBench uses architectural exploits that bypass prompt defenses

---

### 1.3 The Promptware Kill Chain

**Citation**: Brodt, L., Feldman, R., Schneier, B., & Nassi, B. (2026). The Promptware Kill Chain.

**Link**: https://arxiv.org/abs/2601.09625

Reframes prompt injection as the beginning of a malware kill chain, not an isolated exploit. The 7 stages: Initial Access, Privilege Escalation, Reconnaissance, Persistence, Command and Control, Lateral Movement, Actions on Objective. Looking at 36 studies and real incidents, at least 21 attacks traverse 4+ stages. The recommendation: defense in depth that breaks the chain at multiple points.

**Relevant for TrustBench**:
- Kill chain framework for mapping A1-A9 attacks
- Defense strategy recommendation informs how I evaluate mitigations
- Published on Schneier on Security and Lawfare, so it has industry visibility

---

### 1.4 TrojanPuzzle: Covertly Poisoning Code-Suggestion Models

**Citation**: Aghakhani, H. et al. (2024). TrojanPuzzle: Covertly Poisoning Code-Suggestion Models. IEEE S&P 2024.

**Link**: https://arxiv.org/abs/2301.02344

Introduces COVERT and TROJANPUZZLE attacks that plant poison data in docstrings and comments (out-of-context regions that bypass static analysis). TROJANPUZZLE is clever: the poison data never contains the suspicious payload directly, but the model still suggests the complete malicious code at runtime.

**Relevant for TrustBench**:
- Validates that AI models are vulnerable to hidden instructions in docstrings (supports A9 Documentation Poisoning)
- The bypass technique evades signature-based scanning
- They focus on training data poisoning; TrustBench focuses on runtime poisoning

---

### 1.5 Security Issues in the MCP Ecosystem

**Citation**: MCP Security Analysis (2025).

**Link**: https://arxiv.org/abs/2510.16558

First security analysis of the Model Context Protocol. They decomposed MCP into hosts, registries, and servers, then analyzed trust relationships. Hosts don't verify LLM-generated outputs, and IDEs lack origin authentication for MCP servers. They analyzed 67,057 servers from 6 registries. Many can be hijacked because there's no vetted submission process.

**Relevant for TrustBench**:
- IDEs blindly trust any MCP server specified in config files (validates A7)
- No output verification means AI trusts whatever the server returns
- 67,057 servers analyzed, many hijackable

---

### 1.6 Developers Are Victims Too

**Citation**: Ferreira, J. et al. (2024). Developers Are Victims Too: A Comprehensive Analysis of the VS Code Extension Ecosystem.

**Link**: https://arxiv.org/abs/2411.07479

Large-scale analysis of 52,880 VS Code extensions. 5.6% exhibit suspicious behavior that could compromise dev environments or leak sensitive data. The paper documents VS Code's permissive security architecture: extensions run unchecked with broad host and network access, and developers don't get notified.

**Relevant for TrustBench**:
- 5.6% suspicious behavior rate as a baseline
- Largest study to date (52,880 extensions)
- Evidence for the Consent Gap (privileges granted without user awareness)

---

## 2. Academic Papers - Benchmarks and Metrics

These papers define the metrics TrustBench uses. Every metric needs a citation.

### 2.1 AgentDojo: Evaluating Prompt Injection Attacks and Defenses

**Citation**: Debenedetti, E., Zhang, J., Balunovic, M., Beurer-Kellner, L., Fischer, M. & Tramer, F. (2024). AgentDojo: A Dynamic Environment to Evaluate Prompt Injection Attacks and Defenses for LLM Agents. NeurIPS 2024, Datasets and Benchmarks Track.

**Link**: https://arxiv.org/abs/2406.13352

The benchmark that established Attack Success Rate (ASR) as the standard metric for evaluating prompt injection. ASR is defined as the fraction of scenarios where the agent executes the attacker's objective. They also introduced Utility Under Attack (UA), which measures task completion rate when an attack is present.

**Metrics sourced from this paper**:
- **Attack Success Rate (ASR)**: Primary binary outcome. Did the attack succeed?
- **Utility Under Attack (UA)**: Task completion rate with attack active. Measures functional degradation.

**Relevant for TrustBench**:
- ASR definition adopted directly
- UA adapted to measure whether AI completes the user's task despite extension interference

---

### 2.2 Agent Security Bench: Formalizing Attacks and Defenses in LLM Agents

**Citation**: Zhang, H., Huang, J., Mei, K., Yao, Y., Wang, Z., Zhan, C., Wang, H. & Zhang, Y. (2025). Agent Security Bench (ASB): Formalizing and Benchmarking Attacks and Defenses in LLM-based Agents. ICLR 2025, pp. 88011-88046.

**Link**: https://arxiv.org/abs/2410.02644

The most comprehensive agent security benchmark to date. Tested 13 LLM backbones across 10 attack scenarios. Introduced several metrics now standard in the field: Net Resilient Performance (NRP), Refusal Rate (RR), and formalized FPR/FNR for defense evaluation. Table 4 of the paper defines all metrics.

**Metrics sourced from this paper**:
- **Net Resilient Performance (NRP)**: Composite score = PNA x (1 - ASR), where PNA is Performance under No Attack. Summarizes resilience in a single number.
- **Refusal Rate (RR)**: Percentage of tasks refused due to security concerns. Higher RR indicates better security awareness.
- **Benign Performance (BP)**: Whether agent actions for clean queries are unaffected by the attack. Analogous to TrustBench's Self-Correction Rate.
- **FPR/FNR for defenses**: Standard metrics for evaluating detection-based defenses.

**Relevant for TrustBench**:
- NRP formula adopted directly
- RR measured when agents refuse suspicious commands or installs
- FPR/FNR applied to ExtensionGuard and AgentIntegrity Monitor evaluation

---

### 2.3 CyberSecEval 2: Cybersecurity Evaluation for LLMs

**Citation**: Bhatt, M. et al. (2024). CyberSecEval 2: A Wide-Ranging Cybersecurity Evaluation Suite for Large Language Models. Meta.

**Link**: https://arxiv.org/abs/2404.13161

Meta's comprehensive LLM security evaluation suite. Introduced the False Refusal Rate (FRR) to quantify the safety-utility tradeoff of defense mechanisms. Also established methodology for measuring defense effectiveness without compromising model utility.

**Metrics sourced from this paper**:
- **False Refusal Rate (FRR)**: Clean inputs incorrectly blocked. Measures defense over-triggering.
- **Defense evaluation methodology**: How to measure effectiveness without degrading utility.

**Relevant for TrustBench**:
- IDE Prevention Rate is analogous to FRR (does the IDE block the extension's API call?)
- Defense evaluation methodology informs ExtensionGuard and AgentIntegrity testing

---

### 2.4 MCP Security Bench: Attacks Against Model Context Protocol

**Citation**: Mei, K. et al. (2025). MCP Security Bench (MSB): Benchmarking Attacks Against Model Context Protocol in LLM Agents.

**Link**: https://arxiv.org/abs/2510.15994

Benchmark specifically for MCP server attacks. Adopted NRP from Agent Security Bench to measure resilience. Tested tool poisoning, server impersonation, and response manipulation attacks.

**Relevant for TrustBench**:
- Validates A7 (MCP Server Poisoning) attack vector
- Provides baseline ASR for MCP-specific attacks
- Confirms NRP as appropriate metric for this attack category

---

### 2.5 PromptArmor: Prompt Injection Detection Framework

**Citation**: Shi, J. et al. (2025). PromptArmor: Prompt Injection Detection Framework. Evaluated on AgentDojo benchmark.

**Link**: https://promptarmor.com/

Defense-focused evaluation using Precision, Recall, and AUC-ROC on the AgentDojo benchmark. Demonstrated how to evaluate detection guardrails against established attack benchmarks.

**Metrics sourced from this paper**:
- **Precision/Recall/F1**: Standard IR metrics applied to defense tool evaluation.
- **AUC-ROC**: For evaluating detection threshold tuning.

**Relevant for TrustBench**:
- Methodology for evaluating ExtensionGuard against ground-truth labeled patterns
- F1 score as summary metric for defense effectiveness

---

## 3. Industry Threat Intelligence

### 3.1 Schneier on the Promptware Kill Chain

**Source**: Schneier, B. (2026). The Promptware Kill Chain. Schneier on Security.

**Link**: https://www.schneier.com/blog/archives/2026/01/the-promptware-kill-chain.html

Schneier's take on the academic paper. His point: prompt injections have matured into "a full malware lifecycle comparable to traditional APT campaigns." Input sanitization alone won't cut it. The focus needs to be on preventing privilege escalation, disrupting persistence, and limiting blast radius.

Industry validation of the academic research. Confirms TrustBench's defense-in-depth approach aligns with expert recommendations.

---

### 3.2 IDEsaster: VS Code Extension CVEs

**Source**: Ahmed, M. (2025). IDEsaster. Reported 30+ CVEs in VS Code extensions.

**Link**: https://github.com/anthropics/IDEsaster

Documented 30+ CVEs in VS Code extensions with attack persistence rates of 41-84%. The persistence rate measurement methodology directly informs TrustBench's Persistence Rate metric.

**Relevant for TrustBench**:
- 41-84% persistence rates as baseline comparison
- Methodology for measuring whether attack artifacts survive to session end

---

### 3.3 Clinejection: AI Agent Supply Chain Attack

**Citation**: Khan, A. (2026). Clinejection Disclosure. Also: Snyk (2026). Clinejection: When AI Coding Agents Become Attack Vectors.

Proof-of-concept demonstrating prompt injection to npm publication via the Cline AI coding agent. The attacker injects malicious instructions that cause the AI to publish a backdoored package to npm. Measured whether malicious artifacts survived to the end of the CI/CD pipeline.

**Relevant for TrustBench**:
- Real-world validation of dependency injection via AI agent (A1, A6)
- Persistence measurement methodology (did the artifact survive the pipeline?)

---

### 3.4 InstaTunnel: Dependency Side-Loading via AI Extensions

**Source**: InstaTunnel Research Team (2026). Automated Dependency "Side-Loading".

**Link**: https://medium.com/@instatunnel/automated-dependency-side-loading-via-ai-extensions-2026

Documents real attacks where malicious extensions (1.5 million combined installs) exploited AI assistants to side-load malicious dependencies. Also flagged "slopsquatting": LLMs hallucinate non-existent library names about 20% of the time, and attackers pre-register these packages.

The s1ngularity campaign compromised Nx packages and harvested credentials from 1,000+ developer systems. TigerJack published 11 malicious extensions that infected 17,000+ developers before removal.

**Relevant for TrustBench**:
- Real-world validation of A1 (Dependency Injection) and A6 (Sidecar) attacks
- 20% hallucination rate creates slopsquatting opportunity
- 17,000+ infected in a single campaign

---

### 3.5 MaliciousCorgi: 1.5M Developers Compromised

**Source**: Koi Security (2026). Malicious VS Code AI Extensions Harvesting Code.

**Link**: https://koisecurity.com/research/malicious-vscode-ai-extensions-2026

Two extensions ("ChatGPT - 中文版" with 1.34 million installs, "ChatMoss/CodeMoss" with 150,000) exfiltrated code and credentials to servers in China. The extensions worked as advertised while secretly capturing every file opened and every edit made. Three exfiltration methods: real-time file surveillance, batch harvesting, and behavioral tracking via zero-pixel iframes. Undetected for months.

**Relevant for TrustBench**:
- Largest documented incident: 1.5 million developers
- Perfect camouflage (works as advertised while exfiltrating)
- 0% marketplace detection for months
- Validates A2 (Credential Harvesting) and A5 (Data Exfiltration)

---

### 3.6 Anivia/OctoRAT: Multi-Stage Attack via Fake Prettier

**Source**: Hunt.io Research Team (2025). Malicious VSCode Extension Launches Multi-Stage Attack Chain.

**Link**: https://hunt.io/blog/malicious-vscode-extension-anivia-octorat-2025

"prettier-vscode-plus" appeared November 21, 2025, impersonating the legitimate Prettier formatter. It deployed Anivia loader, which decrypted and executed OctoRAT (70+ commands for surveillance, file theft, remote desktop, persistence, privilege escalation). Both payloads used AES encryption, in-memory execution, and process hollowing. Removed 4 hours after Checkmarx reported it.

**Relevant for TrustBench**:
- Multi-stage malware delivery through extensions
- Evasion techniques (AES, in-memory, process hollowing) for defense evaluation
- Impersonation of trusted extension names works

---

### 3.7 Amazon Q Developer Exploits

**Source**: Embrace The Red (2025). Amazon Q Developer: Remote Code Execution with Prompt Injection.

**Link**: https://embracethered.com/blog/posts/2025/amazon-q-developer-rce-prompt-injection/

Two vulnerabilities:
1. **Supply chain attack (July 2025)**: Attacker got admin credentials to aws-toolkit-vscode GitHub repo and committed malicious code in version 1.84.0. The prompt instructed the AI to wipe the system and delete cloud resources. Live for about 2 days.
2. **Invisible instruction exploit (August 2025)**: Trail of Bits showed GitHub bug reports containing invisible Unicode Tag characters could instruct the AI to install backdoors.

**Relevant for TrustBench**:
- Supply chain compromise of an official extension repository
- Wiper payload (kill chain stage 7)
- Invisible Unicode as evasion technique

---

## 4. Security Vulnerability Disclosures

### 4.1 VS Code Extension CVEs: 128M+ Downloads Affected

**Source**: OX Security Research Team (2025). Four Vulnerabilities Expose a Massive Security Blind Spot in IDE Extensions.

**Link**: https://www.ox.security/blog/ide-extension-vulnerabilities-128m-downloads-2025

Four vulnerabilities, 128 million combined downloads:
- **CVE-2025-65717 (Live Server)**: CVSS 9.1 Critical, 72 million downloads. Remote file exfiltration.
- **CVE-2025-65715 (Code Runner)**: High severity, 37 million downloads. Arbitrary code execution.
- **CVE-2025-65716 (Markdown Preview Enhanced)**: CVSS 8.8. JavaScript execution for port scanning.

Disclosed July-August 2025. No maintainer response. 120+ million installs remain unpatched.

**Relevant for TrustBench**:
- Scale: 128M+ downloads across 4 vulnerabilities
- Attack types map to A5 (exfiltration) and A1/A3 (code execution)
- Unpatched vulnerabilities show systemic maintenance problems

---

## 5. Cross-Study Synthesis

### Attack Success Rate Baselines

| Study | Attack Type | ASR | Sample Size | Year |
|-------|-------------|-----|-------------|------|
| "Your AI, My Shell" | Prompt injection | 41-84% | 314 payloads | 2025 |
| AgentDojo | Prompt injection | Varies by agent | 97 scenarios | 2024 |
| Agent Security Bench | Mixed attacks | Varies by LLM | 10 scenarios x 13 LLMs | 2025 |
| MCP Security Bench | MCP poisoning | TBD | MCP-specific | 2025 |
| UntrustIDE | Extension exploits | 100% | 21 exploits | 2024 |
| MaliciousCorgi | Malicious extension (wild) | 100% | 1.5M users | 2026 |
| TrustBench | Extension + AI agent | TBD | ~370 trials | 2026 |

Linguistic attacks (prompt injection) hit 41-85% depending on model and configuration. Real-world architectural attacks (malicious extensions) hit 100% but lack controlled evaluation. TrustBench provides first empirical ASR baseline for architectural exploits in a controlled environment.

---

### Persistence Rate Baselines

| Study | Persistence Rate | Context |
|-------|------------------|---------|
| IDEsaster | 41-84% | VS Code extension CVEs |
| Clinejection | Measured | CI/CD pipeline survival |
| TrustBench | TBD | Session-end survival |

---

### Defense Effectiveness Baselines

| Defense | Study | Detection/Prevention Rate | Notes |
|---------|-------|--------------------------|-------|
| Marketplace scanning | UntrustIDE | 0% | All 21 exploits passed |
| Marketplace scanning | MaliciousCorgi | 0% | Undetected for months |
| Prompt-based defenses | AgentDojo | ~15-50% | Varies by defense |
| PromptArmor guardrail | PromptArmor | Precision/Recall reported | On AgentDojo benchmark |
| TrustBench (ExtensionGuard) | TrustBench | TBD | First extension-specific |
| TrustBench (AgentIntegrity) | TrustBench | TBD | First runtime verification |

---

## 6. Metric Provenance Table

Every metric TrustBench uses has a source in the literature. Where a metric is novel, the closest analogue is cited.

| Metric | Source Paper | Venue/Year | How Used in TrustBench |
|--------|-------------|------------|------------------------|
| Attack Success Rate (ASR) | Debenedetti et al., AgentDojo | NeurIPS 2024 | Primary binary outcome for all 9 attacks |
| Utility Under Attack (UA) | Debenedetti et al., AgentDojo | NeurIPS 2024 | Task completion rate with extension active |
| Net Resilient Performance (NRP) | Zhang et al., Agent Security Bench | ICLR 2025 | Composite score: PNA x (1 - ASR) |
| Refusal Rate (RR) | Zhang et al., Agent Security Bench | ICLR 2025 | How often agent refuses suspicious actions |
| FPR/FNR | Zhang et al., ASB; Bhatt et al., CyberSecEval 2 | ICLR 2025; Meta 2024 | Defense tool evaluation |
| Precision/Recall/F1 | Shi et al., PromptArmor | 2025 | ExtensionGuard pattern detection |
| Agent Detection Rate | Novel (inverse of ASR) | This thesis | Fraction where agent noticed manipulation |
| Self-Correction Rate | Novel (analogue: BP from ASB) | This thesis | Fraction of detected trials successfully reverted |
| Persistence Rate | Novel (precedent: IDEsaster 41-84%) | This thesis | Fraction where artifact survived to session end |
| IDE Prevention Rate | Analogous to FRR from CyberSecEval 2 | This thesis | Whether IDE mechanism blocked the API call |
| Data Exposure Volume | Novel (classified via CWE-200) | This thesis | Secrets captured per session for A2, A8 |

---

## 7. Citation Quick Reference

### Benchmark Papers (Metric Sources)
1. [Debenedetti et al. (2024) - AgentDojo, NeurIPS 2024](https://arxiv.org/abs/2406.13352)
2. [Zhang et al. (2025) - Agent Security Bench, ICLR 2025](https://arxiv.org/abs/2410.02644)
3. [Bhatt et al. (2024) - CyberSecEval 2, Meta](https://arxiv.org/abs/2404.13161)
4. [Mei et al. (2025) - MCP Security Bench](https://arxiv.org/abs/2510.15994)
5. [Shi et al. (2025) - PromptArmor](https://promptarmor.com/)

### Attack Research Papers
6. [Lin et al. (2024) - UntrustIDE, NDSS 2024](https://www.ndss-symposium.org/ndss-paper/untrustide-exploiting-weaknesses-in-vs-code-extensions/)
7. [Liu et al. (2025) - "Your AI, My Shell"](https://arxiv.org/abs/2509.22040)
8. [Brodt et al. (2026) - Promptware Kill Chain](https://arxiv.org/abs/2601.09625)
9. [Aghakhani et al. (2024) - TrojanPuzzle, IEEE S&P 2024](https://arxiv.org/abs/2301.02344)
10. [MCP Security Analysis (2025)](https://arxiv.org/abs/2510.16558)
11. [Ferreira et al. (2024) - Developers Are Victims Too](https://arxiv.org/abs/2411.07479)

### Industry Reports
12. [Schneier (2026) - Promptware Kill Chain Analysis](https://www.schneier.com/blog/archives/2026/01/the-promptware-kill-chain.html)
13. [Ahmed (2025) - IDEsaster](https://github.com/anthropics/IDEsaster)
14. [Khan/Snyk (2026) - Clinejection](https://snyk.io/blog/clinejection-ai-agent-supply-chain/)
15. [InstaTunnel (2026) - Dependency Side-Loading](https://medium.com/@instatunnel/automated-dependency-side-loading-via-ai-extensions-2026)
16. [Koi Security (2026) - MaliciousCorgi](https://koisecurity.com/research/malicious-vscode-ai-extensions-2026)
17. [Hunt.io (2025) - Anivia/OctoRAT](https://hunt.io/blog/malicious-vscode-extension-anivia-octorat-2025)
18. [Embrace The Red (2025) - Amazon Q Exploits](https://embracethered.com/blog/posts/2025/amazon-q-developer-rce-prompt-injection/)
19. [OX Security (2025) - VS Code CVEs](https://www.ox.security/blog/ide-extension-vulnerabilities-128m-downloads-2025)

---

**Status**: Ready for thesis integration
**Sources**: 19 core papers/reports
**Last updated**: April 6, 2026
