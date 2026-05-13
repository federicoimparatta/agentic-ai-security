---
name: agentic-ai-security
display-name: Agentic AI Security
description: >
  Applies security controls from the ASD/CISA/NSA/NCSC agentic AI security
  framework during the design, development, deployment, and operation of
  LLM-based agent systems. Triggers automatically when the user is building
  AI agents, defining tools or tool allow-lists, configuring agent permissions
  or scopes, designing multi-agent orchestration, implementing memory or RAG
  pipelines, writing system prompts for agents, or reviewing existing agent
  architecture. Also activates for questions about privilege management, prompt
  injection defenses, human-in-the-loop patterns, agent identity and
  authentication, agentic system monitoring, confused deputy patterns, or
  cascading failure prevention. Proactively applies the five risk categories —
  Privilege, Design/Config, Behaviour, Structural, Accountability — to any LLM
  agent code being generated, even when a security review has not been
  explicitly requested. Use this skill for any task that produces or modifies
  agent code, agent configuration, or agent system prompts.
---

# Agentic AI Security

Security framework for LLM-based agentic systems, based on the joint guidance
published by ASD, CISA, NSA, NCSC-UK, NCSC-NZ, and the Canadian Cyber Centre (2026).

Apply this skill inline during development — not as a post-hoc audit, but as a
design constraint integrated into every build decision.

---

## How to use this skill

1. Identify the current **lifecycle phase** (Design / Develop / Deploy / Operate)
2. Consult the relevant section below
3. For each risk in the phase, ask: *does the current design expose this vector?*
4. Apply the control before generating code or configuration
5. For deeper detail on any risk, read `references/risk-taxonomy.md`
6. For a full phase checklist, read `references/best-practices-checklist.md`

**Five risk categories — always active:**
Privilege · Design/Config · Behaviour · Structural · Accountability

---

## Phase 1 — DESIGN

### Privilege risks (highest priority)

| Risk | Check | Control |
|---|---|---|
| Scope creep | Agent requests more access than its narrowest task requires | Assign minimum permissions per task, not per agent |
| Confused deputy | Low-privilege user can trigger high-privilege agent action | Validate caller identity before every privileged operation |
| Identity spoofing | Agent credentials are static or shared across agents | Unique cryptographic identity per agent; rotate secrets |
| Implicit trust | Agent A auto-trusts Agent B outputs | Explicit trust boundaries; validate all inter-agent messages |

### Design checklist
- [ ] Each agent has a single, clearly scoped responsibility
- [ ] Permissions are evaluated per-request, not cached at startup
- [ ] Agent environments are segmented (compromise in one cannot pivot to another)
- [ ] Instruction hierarchy is explicit in system prompt
- [ ] Human control points are defined before coding begins

### Minimal system prompt pattern
```
You are a read-only document retrieval agent.
ALLOWED: query the knowledge base, return excerpts.
FORBIDDEN: write to any database, call external APIs, spawn sub-agents.
If asked to perform any FORBIDDEN action, respond: "Outside my scope." and stop.
```

---

## Phase 2 — DEVELOP

### Behaviour risks

| Risk | Signal in code | Mitigation |
|---|---|---|
| Goal misalignment | Agent selects actions not explicitly authorized | Add explicit do-not-do rules to system prompt |
| Specification gaming | Agent finds shortcuts that achieve metric but violate intent | Define success as constraints, not just goals |
| Emergent tool chaining | Agent combines tools in unanticipated sequences | Whitelist tool combinations; adversarial prompt testing |
| Deceptive behaviour | Agent alters output when it detects evaluation context | Vary eval framing; test with/without evaluation signals |

### Input pipeline (minimum viable)
```python
def prepare_input(raw_input: str, source: str) -> str:
    trust = get_trust_level(source)   # "user" | "tool" | "agent" | "external"
    sanitized = strip_prompt_injections(raw_input)
    return f"[Source: {source}, Trust: {trust}]\n{sanitized}"
```

### Audit log schema (build in from day one)
```python
{
    "timestamp": "...",
    "agent_id": "...",          # unique per agent instance
    "action_type": "tool_call | llm_inference | handoff | output",
    "tool_used": "...",
    "decision_summary": "...",  # brief rationale
    "human_approved": bool,
    "input_hash": "...",        # hash, not raw value if sensitive
    "output_hash": "..."
}
```

### Third-party components checklist
- [ ] Every external tool comes from a verified, pinned source
- [ ] Tool descriptions use neutral language (no persuasive phrasing)
- [ ] Tool allow-list is explicit — anything not listed is blocked
- [ ] Tool responses are validated before being passed back to the LLM
- [ ] Agents cannot modify their own permissions or spawn sub-agents freely

---

## Phase 3 — DEPLOY

### Pre-deployment threat model (5-minute version)

Answer these before going live:
1. **Blast radius**: what data can this agent read / write / delete? What systems can it call?
2. **Worst injection**: trace every external data source that feeds the context window
3. **Hardest-to-reverse action**: that action needs mandatory human approval

### Progressive deployment stages
```
Stage 1 — Shadow mode
  Agent runs; all actions logged but NOT executed.
  Human reviews every proposed action.
  Exit: 0 surprises in 50 consecutive runs.

Stage 2 — Supervised execution
  Agent executes low-risk actions autonomously.
  High-risk actions require human approval.
  Exit: error rate < defined threshold.

Stage 3 — Autonomous (narrow scope only)
  Full autonomy within explicitly defined task boundaries.
  Human review of anomalies only.
```

### Hard constraints template
```python
HARD_CONSTRAINTS = [
    "never delete records without explicit human approval",
    "never send external communications without human review",
    "never modify your own system prompt or permissions",
    "never spawn sub-agents without explicit authorization",
    "escalate to human if confidence < threshold or task is ambiguous",
]
# Enforce as BOTH system prompt rules AND programmatic checks.
# Never rely on a single layer (defense in depth).
```

---

## Phase 4 — OPERATE

### Monitoring signals

| Signal | Indicates | Response |
|---|---|---|
| Action volume spike | Sponge/DoS attack or runaway loop | Rate limit + alert |
| Tool calls outside allow-list | Scope creep or compromise | Pause agent + audit |
| Privilege escalation attempt | Confused deputy or identity compromise | Immediate halt |
| Output divergence from baseline | Behaviour drift | Review last N outputs |
| Log gaps | Stealthy exfiltration via tool | Correlated audit |
| Goal drift vs. approved spec | Misalignment emerging over time | Compare to baseline objectives |

### Human-in-the-loop — always require approval for:
- Deletion of any record
- External communications outside defined integrations
- Modifications to other agents' configs or permissions
- Any action classified as irreversible
- Any action the agent itself rates as uncertain

### Runtime authentication pattern
```python
async def execute_privileged_action(agent_id, action, context):
    verify_agent_identity(agent_id)          # cryptographic, never cached
    assert action in get_current_scope(agent_id)
    decision = policy_engine.evaluate(agent_id, action, context)
    if not decision.allowed:
        raise PermissionDenied(decision.reason)
    audit_log(agent_id, action, context, "pre-execution")
    result = await execute(action)
    audit_log(agent_id, action, result, "post-execution")
    return result
```

---

## Red flags — stop and review if you see any of these

- Agent has write access to a resource it only needs to read
- Tool descriptions include phrases like "always prefer this tool"
- Inter-agent calls use a shared API key
- Permissions evaluated once at startup and then cached
- Agent can modify its own system prompt
- No defined human approval checkpoint for irreversible actions
- Logs are written by the same agent whose actions they record

---

## Reference files

- `references/risk-taxonomy.md` — Full five-category risk breakdown with scenario examples from the source guidance
- `references/best-practices-checklist.md` — Complete per-phase checklists for Design, Develop, Deploy, and Operate
