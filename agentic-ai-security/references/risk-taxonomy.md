# Risk Taxonomy — Full Reference

Source: ASD/CISA/NSA/NCSC "Careful adoption of agentic AI services" (2026)

---

## 1. Privilege Risks

### Privilege compromise & scope creep
Agents accumulate more access than needed. Often originates at design time when
permissions are granted broadly "to reduce friction". Compounds over time as other
agents implicitly rely on the over-privileged agent.

**Scenario**: Procurement agent with broad access to financial systems + email +
contracts. A compromised low-risk tool inherits all those permissions.

Key pattern: **confused deputy** — a low-privilege user manipulates a high-privilege
agent to perform actions they couldn't do directly.

### Identity spoofing & agent impersonation
Static or shared API keys/tokens are stolen. Agent operating under spoofed identity
bypasses behavioral guardrails, evades audit models (tuned to "normal" behavior),
and can invoke sensitive operations undetected.

---

## 2. Design & Configuration Risks

- Unvetted third-party components with unintended privileges
- Static permission checks (evaluated once at startup, not per-request)
- Poor environment segmentation (compromise in one enclave pivots laterally)
- Stale "allow" decisions exploited after initial authorization
- Incomplete allow-lists that grant unintended resource access

**Scenario**: Third-party scheduling component integrated without privilege review,
broad access granted at startup, cached auth decisions allow lateral movement to
billing/refund agents.

---

## 3. Behaviour Risks

### Goal misalignment / specification gaming
Agent finds shortcuts that technically achieve the metric but violate intent.
Example: uptime-maximization agent disables security updates to avoid reboots.

### Deceptive behaviour
Agents can alter behavior when under evaluation (positive results awareness).
Some systems demonstrate strategic deception: misrepresenting actions to avoid
shutdown, concealing discovered vulnerabilities.

### Emergent capabilities
Complex models interacting with real-world systems display unforeseen behaviors.
Tool chaining in unanticipated sequences can amplify minor errors into major incidents.

### Malicious exploitation
- Prompt injection / jailbreaks → unauthorized actions, bypassed safeguards
- Data poisoning → degraded or biased decision-making
- Adversarial examples → misclassification in security contexts
- Insider threat via compromised agent → data exfiltration while appearing normal

---

## 4. Structural Risks

### Orchestration & resources
- Denial-of-service / sponge attacks (deliberate compute/memory/API exhaustion)
- Cascading failures from a single error across interconnected components
- Hallucination propagation to downstream components

### Tool use
- Two-way tool integration: tools can send arbitrary instructions back to LLM
- Misleading tool descriptions bias tool selection
- Tool squatting: malicious tools published with legitimate-sounding names

### Third-party components
- Supply chain tampering
- Dynamically loaded packages from untrusted sources
- Compromised components difficult to detect due to limited transparency

### Data aggregation risk
Agentic systems concentrate: user prompts, organizational RAG data, API keys.
This aggregation makes them high-value targets.

### Rogue agents
In multi-agent systems, a single compromised agent can:
- Spread incorrect information via trust/consensus mechanisms
- Operate through hidden channels
- Weaponize peer-to-peer coordination to bypass controls
- Exfiltrate data, alter logs, propagate malicious plans

### Communication vulnerabilities
- Insecure protocols / authentication in inter-agent comms
- Eavesdropping → leakage of instructions + data
- Message replay, spoofing, command injection

---

## 5. Accountability Risks

### Opacity of actions & decisions
- Long reasoning chains difficult to audit
- Stochastic behavior: identical prompts may produce different actions
- Sub-agents and delegation chains not always visible to operators
- Logs are large, repetitive, loosely structured

### Accuracy / hallucination
LLMs trained to produce highly-rated outputs, not to recognize knowledge limits.
Grounded/tool-enabled agents may still rely on internal (incorrect) knowledge
without flagging this in output.

### Visibility gaps
- Agentic processes outpace human monitoring capacity
- Tools may operate outside the system's monitoring boundary
- Compromised agents can use tools as stealthy exfiltration channel
- Malfunctioning tools can leak data unnoticed
