# Executive Summaries: Key Papers for TrustBench Literature Review

These are the papers and reports that matter most for TrustBench. Each summary focuses on what I actually need for the thesis: specific numbers, attack mechanisms, and gaps this research fills.

---

## 1. Academic Papers

### 1.1 UntrustIDE: Exploiting Weaknesses in VS Code Extensions

**Citation**: Lin, E., Koishybayev, I., Dunlap, T., Enck, W., & Kapravelos, A. (2024). UntrustIDE: Exploiting Weaknesses in VS Code Extensions. NDSS 2024.

**Link**: https://www.ndss-symposium.org/ndss-paper/untrustide-exploiting-weaknesses-in-vs-code-extensions/

This won the distinguished paper award at NDSS 2024. The researchers analyzed 25,402 VS Code extensions and found 21 with verified exploits affecting 6 million users. They identified four untrusted input sources (workspace settings, files, user input, network data) and three injection targets. The big finding: workspace files are the primary attack vector. None of the 21 exploits were caught by marketplace scanning.

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
- The 84% ASR is the baseline I'm comparing against
- I'm using their ASR definition (AI executes malicious intent, regardless of full effectiveness)
- They rely on linguistic tricks; TrustBench uses architectural exploits that bypass prompt defenses entirely

---

### 1.3 Prompt Injection Attacks: A Systematic Analysis

**Citation**: Meta-analysis (2026). Prompt Injection Attacks on Agentic Coding Assistants.

**Link**: https://arxiv.org/abs/2601.17548

A meta-analysis pulling from MCPSecBench, IDEsaster, and other benchmarks. The headline number: when attackers use adaptive strategies, ASR exceeds 85% against state-of-the-art defenses. The authors introduce the "Consent Gap" concept: users trust AI tools by default without understanding what privileges they're granting.

**Relevant for TrustBench**:
- 85% ASR for adaptive attacks against current defenses
- "Consent Gap" as a theoretical framework I'm validating empirically
- Their taxonomy for classifying agent-tool boundary violations

**Note**: The 26.1% vulnerability rate in agent skills comes from a related paper, "Agent Skills in the Wild" ([arXiv:2601.10338](https://arxiv.org/abs/2601.10338)).

---

### 1.4 The Promptware Kill Chain

**Citation**: Brodt, L., Feldman, R., Schneier, B., & Nassi, B. (2026). The Promptware Kill Chain.

**Link**: https://arxiv.org/abs/2601.09625

This reframes prompt injection as the beginning of a malware kill chain, not an isolated exploit. The 7 stages: Initial Access, Privilege Escalation, Reconnaissance, Persistence, Command and Control, Lateral Movement, Actions on Objective. Looking at 36 studies and real incidents, at least 21 attacks traverse 4+ stages. The recommendation: defense in depth that breaks the chain at multiple points.

**Relevant for TrustBench**:
- Kill chain framework for mapping A1-A9 attacks
- Defense strategy recommendation informs how I evaluate mitigations
- Published on Schneier on Security and Lawfare, so it has industry visibility

---

### 1.5 TrojanPuzzle: Covertly Poisoning Code-Suggestion Models

**Citation**: Aghakhani, H. et al. (2024). TrojanPuzzle: Covertly Poisoning Code-Suggestion Models. IEEE S&P 2024.

**Link**: https://arxiv.org/abs/2301.02344

They introduce COVERT and TROJANPUZZLE attacks that plant poison data in docstrings and comments (out-of-context regions that bypass static analysis). TROJANPUZZLE is clever: the poison data never contains the suspicious payload directly, but the model still suggests the complete malicious code at runtime.

**Relevant for TrustBench**:
- Validates that AI models are vulnerable to hidden instructions in docstrings (directly supports A9 Documentation Poisoning)
- The bypass technique evades signature-based scanning
- They focus on training data poisoning; TrustBench focuses on runtime poisoning

---

### 1.6 Security Issues in the MCP Ecosystem

**Citation**: MCP Security Analysis (2025).

**Link**: https://arxiv.org/abs/2510.16558

First security analysis of the Model Context Protocol. They decomposed MCP into hosts, registries, and servers, then analyzed trust relationships. The findings: hosts don't verify LLM-generated outputs, and IDEs lack origin authentication for MCP servers. They analyzed 67,057 servers from 6 registries. Many can be hijacked because there's no vetted submission process.

**Relevant for TrustBench**:
- IDEs blindly trust any MCP server specified in config files (validates A7)
- No output verification means AI trusts whatever the server returns
- 67,057 servers analyzed, many hijackable

---

### 1.7 Developers Are Victims Too

**Citation**: Developers Are Victims Too (2024).

**Link**: https://arxiv.org/abs/2411.07479

Large-scale analysis of 52,880 VS Code extensions. 5.6% exhibit suspicious behavior that could compromise dev environments or leak sensitive data. The paper documents VS Code's permissive security architecture: extensions run unchecked with broad host and network access, and developers don't get notified.

**Relevant for TrustBench**:
- 5.6% suspicious behavior rate as a baseline
- Largest study to date (52,880 extensions)
- Evidence for the Consent Gap (privileges granted without user awareness)

---

## 2. Industry Threat Intelligence

### 2.1 Schneier on the Promptware Kill Chain

**Source**: Schneier, B. (2026). The Promptware Kill Chain. Schneier on Security.

**Link**: https://www.schneier.com/blog/archives/2026/01/the-promptware-kill-chain.html

Schneier's take on the academic paper. His point: prompt injections have matured into "a full malware lifecycle comparable to traditional APT campaigns." Input sanitization alone won't cut it. The focus needs to be on preventing privilege escalation, disrupting persistence, and limiting blast radius when an agent gets compromised.

Industry validation of the academic research. Confirms that TrustBench's defense-in-depth approach aligns with expert recommendations.

---

### 2.2 InstaTunnel: Dependency Side-Loading via AI Extensions

**Source**: InstaTunnel Research Team (2026). Automated Dependency "Side-Loading".

**Link**: https://medium.com/@instatunnel/automated-dependency-side-loading-via-ai-extensions-2026

Documents real attacks where malicious extensions (1.5 million combined installs) exploited AI assistants to side-load malicious dependencies. The AI reads invisible instructions in READMEs and imports malicious packages. They also flagged "slopsquatting": LLMs hallucinate non-existent library names about 20% of the time, and attackers pre-register these packages with malicious payloads.

The s1ngularity campaign compromised Nx packages and harvested credentials from 1,000+ developer systems. TigerJack published 11 malicious extensions that infected 17,000+ developers before removal.

**Relevant for TrustBench**:
- Real-world validation of A1 (Dependency Injection) and A6 (Sidecar) attacks
- 20% hallucination rate creates slopsquatting opportunity
- 17,000+ infected in a single campaign

---

### 2.3 MaliciousCorgi: 1.5M Developers Compromised

**Source**: Koi Security (2026). Malicious VS Code AI Extensions Harvesting Code.

**Link**: https://koisecurity.com/research/malicious-vscode-ai-extensions-2026

Two extensions ("ChatGPT - 中文版" with 1.34 million installs, "ChatMoss/CodeMoss" with 150,000) exfiltrated code and credentials to servers in China. The extensions worked exactly as advertised while secretly capturing every file opened and every edit made. Three exfiltration methods: real-time file surveillance, batch file harvesting, and behavioral tracking via zero-pixel iframes with Chinese analytics SDKs. Undetected for months.

**Relevant for TrustBench**:
- Largest documented incident: 1.5 million developers
- Perfect camouflage (works as advertised while exfiltrating)
- 0% marketplace detection for months
- Validates feasibility of A2 (Credential Harvesting) and A5 (Data Exfiltration)

---

### 2.4 Anivia/OctoRAT: Multi-Stage Attack via Fake Prettier

**Source**: Hunt.io Research Team (2025). Malicious VSCode Extension Launches Multi-Stage Attack Chain.

**Link**: https://hunt.io/blog/malicious-vscode-extension-anivia-octorat-2025

"prettier-vscode-plus" appeared on the marketplace on November 21, 2025, impersonating the legitimate Prettier formatter. It deployed Anivia loader, which decrypted and executed OctoRAT (70+ commands for surveillance, file theft, remote desktop, persistence, privilege escalation). Both payloads used AES encryption, in-memory execution, and process hollowing. The attacker rotated payloads frequently on GitHub to evade detection. Removed 4 hours after Checkmarx reported it.

**Relevant for TrustBench**:
- Multi-stage malware delivery through extensions is happening
- Evasion techniques (AES, in-memory, process hollowing) to consider in defense evaluation
- Impersonation of trusted extension names works

---

### 2.5 Amazon Q Developer Exploits

**Source**: Embrace The Red (2025). Amazon Q Developer: Remote Code Execution with Prompt Injection.

**Link**: https://embracethered.com/blog/posts/2025/amazon-q-developer-rce-prompt-injection/

Two separate vulnerabilities:

1. **Supply chain attack (July 2025)**: An attacker got admin credentials to the aws-toolkit-vscode GitHub repo and committed malicious code, which shipped in version 1.84.0 on July 17. The prompt instructed the AI to wipe the system and delete cloud resources. Live for about 2 days.

2. **Invisible instruction exploit (August 2025)**: Trail of Bits showed that GitHub bug reports containing invisible Unicode Tag characters could instruct the AI to install backdoors.

**Relevant for TrustBench**:
- Supply chain compromise of an official extension repository
- Wiper payload (Actions on Objective, kill chain stage 7)
- Invisible Unicode instructions as an evasion technique

---

## 3. Security Vulnerability Disclosures

### 3.1 VS Code Extension CVEs: 128M+ Downloads Affected

**Source**: OX Security Research Team (2025). Four Vulnerabilities Expose a Massive Security Blind Spot in IDE Extensions.

**Link**: https://www.ox.security/blog/ide-extension-vulnerabilities-128m-downloads-2025

Four vulnerabilities, 128 million combined downloads:

- **CVE-2025-65717 (Live Server)**: CVSS 9.1 Critical, 72 million downloads. Remote file exfiltration.
- **CVE-2025-65715 (Code Runner)**: High severity, 37 million downloads. Arbitrary code execution via crafted config entry.
- **CVE-2025-65716 (Markdown Preview Enhanced)**: CVSS 8.8. JavaScript execution for port scanning and data exfiltration.

Disclosed July-August 2025. As of writing, no maintainer has responded. 120+ million installs remain unpatched.

**Relevant for TrustBench**:
- Scale: 128M+ downloads across 4 vulnerabilities
- Attack types map to A5 (exfiltration) and A1/A3 (code execution)
- Unpatched vulnerabilities show systemic maintenance problems
- Single extension compromise enables lateral movement across orgs

---

## 4. Cross-Study Synthesis

### Attack Success Rate Baselines

| Study | Attack Type | ASR | Sample Size | Year |
|-------|-------------|-----|-------------|------|
| "Your AI, My Shell" | Prompt injection | 41-84% | 314 payloads | 2025 |
| Meta-Analysis | Prompt injection (adaptive) | 85% | 78 studies | 2026 |
| UntrustIDE | Extension exploits | 100% | 21 exploits | 2024 |
| MaliciousCorgi | Malicious extension (wild) | 100% | 1.5M users | 2026 |
| Anivia/OctoRAT | Malicious extension (wild) | 100% | 6 downloads | 2025 |
| TrustBench | Extension + AI agent | TBD | ~370 trials | 2026 |

Linguistic attacks (prompt injection) hit 41-85% depending on model and configuration. Real-world architectural attacks (malicious extensions) hit 100% but lack controlled evaluation. TrustBench fills the gap: first empirical ASR baseline for architectural exploits in a controlled environment.

---

### Detection Rate Baselines

| Defense | Study | Detection Rate | Notes |
|---------|-------|----------------|-------|
| Marketplace scanning | UntrustIDE | 0% | All 21 exploits passed |
| Marketplace scanning | MaliciousCorgi | 0% | Undetected for months |
| Marketplace scanning | Anivia/OctoRAT | 0% | Removed after researcher report |
| Prompt-based defenses | Meta-Analysis | 15% | 85% bypass rate |
| Static analysis | NHSJS Framework | Not evaluated | Proposal only |
| Runtime verification | Industry | Not evaluated | No published metrics |
| TrustBench | Static + Runtime | TBD | First empirical evaluation |

Current defenses run somewhere between 0% and 15% effective. TrustBench will provide the first empirical evaluation of static and runtime defenses against architectural exploits.

---

### Vulnerability Prevalence

| Study | Sample Size | Malicious Rate | Notes |
|-------|-------------|----------------|-------|
| UntrustIDE | 25,402 extensions | 0.08% | Verified exploits, manual analysis |
| Developers Are Victims Too | 52,880 extensions | 5.6% | Suspicious behavior, automated |
| Agent Skills in the Wild | 8,000+ skills | 26.1% | Critical vulnerabilities |

Extension-level malicious behavior ranges from 0.08% (verified exploits) to 5.6% (suspicious behavior). Agent skills show a higher rate at 26.1%, which suggests AI-specific attack surfaces may be easier to exploit than traditional extension threats.

---

## 5. Implications for TrustBench

### Research Gaps This Work Addresses

1. **ASR baseline for architectural attacks**: 84-85% is established for prompt injection, but no controlled baseline exists for extension-based attacks. Real incidents show 100% but aren't rigorous.

2. **Defense effectiveness numbers**: Static analysis and runtime verification are proposed or sold, never empirically evaluated. TrustBench provides first detection rate metrics.

3. **Attack-defense comparison**: Are architectural attacks easier or harder to defend against than linguistic attacks? Nobody has tested this.

4. **Multi-IDE testing**: Prior work tested single IDEs. TrustBench tests 3.

5. **Evidence collection**: Real incidents rely on forensics after the fact. TrustBench captures proxy logs, extension logs, and git diffs in real time.

### Positioning

Prompt injection attacks hit 41-85% ASR depending on model and configuration. Architectural exploits via malicious extensions hit 100% in the wild but lack controlled evaluation. TrustBench is the first controlled, reproducible evaluation of extension-based attacks against AI coding agents, providing ASR baselines and defense effectiveness numbers. Those are the two things missing from the current literature.

---

## 6. Citation Quick Reference

### Academic Papers
1. [Lin et al. (2024) - UntrustIDE, NDSS 2024](https://www.ndss-symposium.org/ndss-paper/untrustide-exploiting-weaknesses-in-vs-code-extensions/)
2. [Liu et al. (2025) - "Your AI, My Shell"](https://arxiv.org/abs/2509.22040)
3. [Meta-Analysis (2026) - Prompt Injection Attacks](https://arxiv.org/abs/2601.17548)
4. [Brodt et al. (2026) - Promptware Kill Chain](https://arxiv.org/abs/2601.09625)
5. [Aghakhani et al. (2024) - TrojanPuzzle, IEEE S&P 2024](https://arxiv.org/abs/2301.02344)
6. [MCP Security (2025)](https://arxiv.org/abs/2510.16558)
7. [Developers Are Victims Too (2024)](https://arxiv.org/abs/2411.07479)
8. [Agent Skills in the Wild (2026)](https://arxiv.org/abs/2601.10338)

### Industry Reports
9. [Schneier (2026) - Promptware Kill Chain Analysis](https://www.schneier.com/blog/archives/2026/01/the-promptware-kill-chain.html)
10. [InstaTunnel (2026) - Dependency Side-Loading](https://medium.com/@instatunnel/automated-dependency-side-loading-via-ai-extensions-2026)
11. [Koi Security (2026) - MaliciousCorgi Campaign](https://koisecurity.com/research/malicious-vscode-ai-extensions-2026)
12. [Hunt.io (2025) - Anivia/OctoRAT](https://hunt.io/blog/malicious-vscode-extension-anivia-octorat-2025)
13. [Embrace The Red (2025) - Amazon Q Exploits](https://embracethered.com/blog/posts/2025/amazon-q-developer-rce-prompt-injection/)
14. [OX Security (2025) - VS Code CVEs](https://www.ox.security/blog/ide-extension-vulnerabilities-128m-downloads-2025)

---

**Status**: Ready for thesis integration
**Sources**: 14 core papers/reports + 20+ supplementary
**Last updated**: April 6, 2026
