# Attack Success Rate (ASR) Comparison: TrustBench vs Prior Work

## Executive summary

Existing ASR baselines focus on prompt injection attacks (linguistic). TrustBench establishes the first empirical baselines for architectural exploits via malicious IDE extensions.

---

## Comprehensive ASR comparison table

| Study | Year | Attack Vector | Attack Type | IDE/Agent | Trials | ASR | Detection Rate | Defense Tested |
|-------|------|--------------|-------------|-----------|--------|-----|----------------|----------------|
| "Your AI, My Shell" (Zhao et al.) | 2025 | Prompt injection | Linguistic | Cursor, Copilot | ~200 | 84% | N/A | Prompt filtering |
| Prompt Injection Meta-Analysis | 2026 | Prompt injection | Linguistic | Multiple agents | 8,000+ skills | 85% (adaptive) | N/A | State-of-art defenses |
| Promptware Kill Chain (Brodt et al.) | 2026 | Multi-stage prompt | Linguistic | Not specified | ~100 | 78% | N/A | None |
| MCPSecBench (cited in meta-analysis) | 2025 | MCP poisoning | Hybrid | Cursor | ~150 | 72% | N/A | None |
| IDEsaster (cited in meta-analysis) | 2025 | Workspace poisoning | Hybrid | VS Code + AI | ~120 | 68% | N/A | None |
| UntrustIDE (Lin et al.) | 2024 | Extension exploits | Architectural | VS Code (no AI) | 21 exploits | 100% (manual) | 0% (marketplace) | Marketplace scanning |
| MaliciousCorgi (Koi Security) | 2026 | Malicious extension | Architectural | VS Code (ChatMoss) | 1.5M users | 100% (in wild) | 0% (months undetected) | Marketplace scanning |
| Anivia/OctoRAT (Hunt.io) | 2025 | Malicious extension | Architectural | VS Code | Unknown | 100% (in wild) | 0% (weeks undetected) | Marketplace scanning |
| TrustBench (Proposed) | 2026 | Extension + AI agent | Architectural | Cursor, Windsurf, Kiro | ~370 | TBD | TBD | Static + Runtime |

---

## Detailed study comparisons

### 1. "Your AI, My Shell" (Zhao et al., 2025)

**Study design**:
- Benchmark: AIShellJack with prompt injection payloads
- IDEs: Cursor, GitHub Copilot
- Attack: Hidden malicious instructions in files (text-based)
- ASR metric: (trials where AI executes malicious intent) / (total trials)
- Success criteria: AI executes target command (even partially)

**Results**:
- 84% ASR across Cursor and Copilot
- Success even without full command completion
- Simulation constraints (time limits, missing files) prevented some full executions

**Comparison to TrustBench**:

| Dimension | "Your AI, My Shell" | TrustBench |
|-----------|---------------------|------------|
| Attack mechanism | Text in files (prompt injection) | Extension manipulation (architectural) |
| Stealthiness | Medium (visible in AI logs) | High (silent background) |
| User awareness | AI may quote malicious text | No visible artifacts |
| Defense bypass | Prompt filtering insufficient | Prompt defenses are irrelevant |
| ASR measurement | Command execution | Payload in git diff |

TrustBench's architectural approach bypasses prompt-based defenses entirely, which may yield higher ASR.

---

### 2. Prompt Injection Meta-Analysis (2026)

**Study design**:
- Scope: Synthesized MCPSecBench, IDEsaster, Nasr et al.
- Dataset: 8,000+ agent skills analyzed
- Attack: 31 cataloged techniques (prompt injection variants)
- Defenses: State-of-the-art prompt filtering, RAG validation

**Results**:
- 85% ASR with adaptive strategies (attackers adjust to defenses)
- 26.1% of agent skills contain critical vulnerabilities
- Consent Gap: Users trust by default

**Comparison to TrustBench**:

| Dimension | Meta-Analysis | TrustBench |
|-----------|---------------|------------|
| Attack scope | 31 prompt techniques | 9 architectural techniques |
| Skills analyzed | 8,000+ (marketplace) | 3 IDEs x 9 attacks |
| Defense evaluation | Prompt-based only | Static + Runtime |
| Novelty | Synthesis of existing work | First architectural ASR baseline |

The meta-analysis confirms an 85% ASR ceiling for linguistic attacks. TrustBench explores an orthogonal attack vector.

---

### 3. UntrustIDE (Lin et al., NDSS 2024)

**Study design**:
- Focus: VS Code extension vulnerabilities (pre-AI agent era)
- Exploits: 21 manually crafted exploits
- Victims: 6M+ users across vulnerable extensions
- Attack: Workspace file manipulation to trick human developers

**Results**:
- 100% success rate (manual exploitation)
- 0% detection by marketplace scanning
- Primary vector: Workspace settings and files

**Comparison to TrustBench**:

| Dimension | UntrustIDE | TrustBench |
|-----------|------------|------------|
| Target | Human developers | AI coding agents |
| Attack automation | Manual | Fully automated |
| Exploitation method | Social engineering | Autonomous agent manipulation |
| ASR measurement | Not quantified | Empirical trials (n=370) |
| Temporal context | Pre-AI agents (2024) | AI agent era (2026) |

UntrustIDE proved extensions are dangerous but didn't quantify ASR or test against AI agents. TrustBench extends this work to the AI era with empirical metrics.

---

### 4. Real-world incidents: MaliciousCorgi and Anivia/OctoRAT

**MaliciousCorgi Campaign (Koi Security, 2026)**:
- Vector: ChatMoss extension on VS Code Marketplace
- Payload: `onDidChangeTextDocument` API to exfiltration to C2
- Victims: 1.5M developers
- ASR: 100% (all installs compromised)
- Detection: 0% for months

**Anivia/OctoRAT (Hunt.io, Nov 2025)**:
- Vector: "prettier-vscode-plus" impersonation
- Payload: Multi-stage malware (Anivia loader + OctoRAT)
- ASR: 100% (all installs)
- Detection: 0% for weeks

**Comparison to TrustBench**:

| Dimension | Real-World Incidents | TrustBench |
|-----------|---------------------|------------|
| Environment | Production (real users) | Controlled VM (ethical) |
| ASR measurement | Binary (installed = compromised) | Nuanced (payload in code?) |
| AI agent interaction | Unclear if AI agents targeted | Explicit AI agent focus |
| Evidence collection | Post-hoc forensics | Full evidence streams |

Real-world incidents show 100% ASR in the wild but lack controlled evaluation. TrustBench provides rigorous, reproducible measurement.

---

## TrustBench ASR hypotheses

Based on prior work and attack characteristics:

### High ASR expected (>80%)
Deterministic attacks where the extension fully controls the outcome:
- **A2: Credential Harvesting** - Extension reads `.env` directly (no AI dependency)
- **A5: Data Exfiltration** - Extension spawns process (full control)
- **A7: MCP Server Poisoning** - Extension rewrites config (AI must use fake server)
- **A8: Clipboard Harvesting** - Extension monitors clipboard (passive capture)

Hypothesis: ASR around 90-100% (comparable to real-world incidents)

### Medium ASR expected (50-80%)
Probabilistic attacks that require AI cooperation:
- **A4: Context Poisoning** - AI must read and follow `.cursorrules`
- **A9: Documentation Poisoning** - AI must read and apply poisoned docs

Hypothesis: ASR around 60-75% (lower than linguistic attacks because AI may ignore context)

### Lower ASR expected (30-60%)
Highly probabilistic attacks that require specific AI behavior:
- **A1: Dependency Injection** - AI must suggest/install dependencies
- **A3: Code Tampering** - AI must generate code first (timing is critical)
- **A6: Supply Chain Sidecar** - AI must import/resolve local packages

Hypothesis: ASR around 40-60% (depends on AI agent workflow patterns)

---

## ASR metric definitions compared

| Study | ASR Definition | Success Criteria | Partial Success? | Timing Window |
|-------|----------------|------------------|------------------|---------------|
| Zhao et al. | (executed malicious intent) / (total trials) | AI executes target command | Yes (even partial) | N/A (instant) |
| Promptware | (instruction to command translation) / (total) | Multi-step reasoning completes | Yes (any stage) | Multi-step window |
| UntrustIDE | Manual success (qualitative) | Human developer exploited | N/A | Manual timing |
| Real-world | Binary (installed = compromised) | Extension installed and ran | N/A | Days/weeks undetected |
| TrustBench | (payload in git diff) / (total trials) | `THESIS-ATTACK-PAYLOAD-MARKER` present in final code | No (binary) | Trial timeout (10 min) |

TrustBench uses a stricter metric:
- Binary outcome: Payload either present or absent in code (no partial credit)
- Ground truth: Git diff is definitive evidence (not AI logs)
- Conservative: Doesn't count "attempted but failed" attacks

TrustBench ASR may be lower than prior work due to stricter criteria, but represents more rigorous evidence of successful compromise.

---

## Defense evaluation: What's new in TrustBench?

| Defense Type | Prior Work Evaluation | TrustBench Evaluation |
|--------------|----------------------|----------------------|
| Prompt filtering | Tested extensively (Zhao et al., meta-analysis) | Not tested (irrelevant to architectural attacks) |
| RAG validation | Tested (meta-analysis) | Not tested (irrelevant) |
| Marketplace scanning | Exists but 0% detection (MaliciousCorgi, Anivia) | Acknowledged but not re-tested |
| Static analysis | Not empirically tested | IDE Extensions Hunter, custom tooling |
| Runtime verification | Not empirically tested | mitmproxy, git diff monitoring, CDP logging |
| Hybrid (static + runtime) | Not tested | Combined approach |

Prior work focused on preventing linguistic attacks. TrustBench is the first empirical evaluation of static/runtime defenses against architectural exploits.

---

## Expected ASR outcomes: Scenarios

### Scenario 1: No defenses (baseline)

Hypothesis: ASR comparable to real-world incidents
- Deterministic attacks: 90-100% ASR
- Probabilistic attacks: 50-75% ASR
- Overall ASR: around 70-85% (weighted by attack distribution)

Similar to Zhao et al. (84%) but via a different mechanism.

---

### Scenario 2: Static analysis enabled

Hypothesis: Detects obvious malicious patterns but misses obfuscation
- Detection rate: around 60-70% for blatant attacks (A2, A5, A8)
- Evasion: around 30-40% still succeed via obfuscation
- Overall ASR: around 40-50% (significant reduction)

First quantified effectiveness of static analysis on extension attacks.

---

### Scenario 3: Runtime verification enabled

Hypothesis: Detects behavioral anomalies but has latency
- Detection rate: around 70-80% (higher than static due to behavioral analysis)
- Evasion: around 20-30% succeed via timing (A3) or stealth (A8)
- Overall ASR: around 30-40% (better than static)

First quantified effectiveness of runtime verification.

---

### Scenario 4: Hybrid defense (static + runtime)

Hypothesis: Best detection but complexity overhead
- Detection rate: around 80-90% (combined strengths)
- Evasion: around 10-20% succeed via sophisticated techniques
- Overall ASR: around 20-30% (best outcome)

First empirical comparison of defense combinations.

---

## Research questions answered

| RQ | Prior Work Answer | TrustBench Contribution |
|----|-------------------|------------------------|
| RQ1: What is ASR for architectural attacks? | No baseline (only linguistic) | First architectural ASR baseline |
| RQ2: How effective is static analysis? | Not tested empirically | Quantified detection rate |
| RQ3: How effective is runtime verification? | Not tested empirically | Quantified detection rate |
| RQ4: Static vs runtime trade-offs? | No comparison exists | Head-to-head comparison |
| RQ5: Can defenses be evaded? | Yes for prompt injection (85% ASR despite defenses) | Measured evasion for architectural attacks |

---

## Implications for thesis contributions

### Contribution 1: First architectural ASR baseline

Claim: "We establish the first empirical ASR baselines for malicious IDE extensions exploiting AI coding agents, complementing existing linguistic attack baselines (84-85%)."

Evidence: Table comparing TrustBench ASR to Zhao et al., meta-analysis

---

### Contribution 2: Defense effectiveness quantification

Claim: "We provide the first quantitative evaluation of static analysis and runtime verification defenses against architectural exploits, filling a critical gap in the literature."

Evidence: ASR reduction across defense scenarios (Baseline → Static → Runtime → Hybrid)

---

### Contribution 3: Attack-defense arms race

Claim: "We demonstrate that architectural exploits are more difficult to defend against than linguistic attacks, requiring hybrid defense approaches."

Evidence: Lower detection rates for architectural attacks vs prompt injection (hypothesized)

---

### Contribution 4: Reproducible benchmark

Claim: "TrustBench provides a reproducible benchmark for evaluating IDE security defenses in the AI agent era, with 370+ trials and full evidence collection."

Evidence: Controlled VM environment, versioned tooling, open-source release

---

## Limitations and threats to validity

### Comparison limitations

1. **Different ASR metrics**: TrustBench uses stricter "payload in diff" vs Zhao et al.'s "command execution"
   - Mitigation: Map TrustBench outcomes to Zhao et al.'s definition in supplementary analysis

2. **Different IDEs**: Zhao et al. tested Cursor/Copilot; TrustBench tests Cursor/Windsurf/Kiro
   - Mitigation: Cursor overlap enables direct comparison

3. **Temporal validity**: AI models evolve rapidly; 2025 baselines may not apply to 2026 models
   - Mitigation: Document exact model versions, re-run benchmarks as models update

4. **Trial count**: TrustBench (370 trials) vs Meta-analysis (8,000+ skills)
   - Mitigation: Controlled experiments prioritize depth over breadth; 370 trials sufficient for statistical power

### External validity

1. **VM environment**: Controlled trials may not reflect real-world complexity
   - Mitigation: Real-world incidents (MaliciousCorgi, Anivia) validate attack feasibility

2. **Attack sophistication**: TrustBench tests basic attacks, not advanced evasion
   - Mitigation: Future work can extend to adversarial evasion techniques

3. **Generalizability**: Results specific to tested IDEs/agents
   - Mitigation: Multi-IDE evaluation (3 IDEs) improves generalizability

---

## Recommended thesis tables and figures

### Table 1: ASR baseline comparison

| Study | Attack Type | ASR | Defense | Notes |
|-------|-------------|-----|---------|-------|
| Zhao et al. | Linguistic | 84% | Prompt filtering | State-of-art AI agents |
| Meta-analysis | Linguistic | 85% | Multiple defenses | Adaptive attacks |
| TrustBench | Architectural | TBD | Static + Runtime | First baseline |

### Table 2: Attack success by category

| Category | Attacks | Expected ASR | Actual ASR | Detection Rate |
|----------|---------|--------------|------------|----------------|
| Observers | A2,A5,A8 | 90-100% | TBD | TBD |
| Piggybackers | A1,A3,A6 | 40-60% | TBD | TBD |
| Deceivers | A4,A7,A9 | 60-75% | TBD | TBD |

### Figure 1: ASR comparison across studies
- Bar chart: Zhao (84%), Meta (85%), TrustBench (TBD)
- Error bars for confidence intervals
- Color-code by attack type (Linguistic vs Architectural)

### Figure 2: Defense effectiveness cascade
- Line chart: ASR reduction across defense scenarios
- X-axis: Baseline → Static → Runtime → Hybrid
- Y-axis: ASR percentage
- Show TrustBench alongside Zhao et al.'s defense results

---

## Citation recommendations

When comparing to prior work in the thesis:

**For ASR baselines**:
> Prior work established that linguistic attacks via prompt injection achieve 84-85% ASR against state-of-the-art AI coding assistants (Zhao et al., 2025; Meta-analysis, 2026). No baseline exists for architectural exploits via malicious IDE extensions. TrustBench fills this gap with the first empirical evaluation of extension-based attacks, hypothesizing ASR of 70-85% for deterministic attacks and 40-60% for probabilistic attacks.

**For defense evaluation**:
> While prompt-based defenses have been extensively studied (Zhao et al., 2025; OpenSSF, 2024), static analysis and runtime verification of malicious IDE extensions remain unevaluated. TrustBench provides the first empirical assessment of these defense mechanisms, measuring detection rates and false positive/negative rates across 370 controlled trials.

**For novelty positioning**:
> Our work complements prompt injection research (Zhao et al., Brodt et al.) by exploring an orthogonal attack vector: architectural exploitation via co-resident malicious extensions. This represents a fundamentally different threat model that bypasses prompt-based defenses entirely, requiring new defensive approaches.

---

**Document Status**: Ready for integration into thesis
**Next Steps**:
1. Run TrustBench trials to populate "TBD" cells
2. Generate figures from results
3. Integrate into Related Work chapter
4. Update as new baselines emerge
