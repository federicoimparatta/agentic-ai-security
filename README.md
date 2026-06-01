# agentic-ai-security

A Claude skill that applies the **ASD/CISA/NSA/NCSC agentic AI security framework** inline during agent development — catching privilege, behaviour, and structural risks at the moment code is written, not after.

> Based on the joint guidance *"Careful adoption of agentic AI services"* (2026), co-authored by the Australian Signals Directorate, CISA, NSA, NCSC-UK, NCSC-NZ, and the Canadian Centre for Cyber Security.

---

## What it does

When you're building an LLM-based agent in Claude Code, this skill activates automatically and applies five security risk categories to everything being designed or coded:

| Category | What it catches |
|---|---|
| **Privilege** | Scope creep, confused deputy pattern, identity spoofing |
| **Design/Config** | Static permissions, poor segmentation, stale auth decisions |
| **Behaviour** | Goal misalignment, specification gaming, emergent tool chaining |
| **Structural** | Cascading failures, tool injection, rogue agents, third-party risks |
| **Accountability** | Opaque decision chains, hallucination propagation, log gaps |

It covers the full agent lifecycle: **Design → Develop → Deploy → Operate**.

---

## Triggers

The skill activates automatically when you're:

- Designing or coding AI agents (LLM-based)
- Defining tools, tool allow-lists, or tool descriptions
- Configuring agent permissions, scopes, or credentials
- Building multi-agent orchestration or delegation chains
- Implementing memory, RAG pipelines, or external data sources
- Writing system prompts for agents
- Reviewing existing agent architecture for security issues
- Asking about: privilege management · prompt injection · human-in-the-loop · agent identity · monitoring · confused deputy · cascading failures

You can also invoke it explicitly with `/agentic-ai-security`.

---

## Install

### Claude Code

```bash
# From the official skills repository (once published)
/install agentic-ai-security

# Or manually — clone and copy to your project
git clone https://github.com/YOUR_USERNAME/agentic-ai-security
cp -r agentic-ai-security/.claude/skills/agentic-ai-security YOUR_PROJECT/.claude/skills/
```

### Manual install (any project)

1. Copy the `agentic-ai-security/` folder into your project's `.claude/skills/` directory:

```
your-project/
└── .claude/
    └── skills/
        └── agentic-ai-security/
            ├── SKILL.md
            └── references/
                ├── risk-taxonomy.md
                ├── best-practices-checklist.md
                └── ul-certification-mapping.md
```

2. Claude Code will discover and load it automatically on next session.

---

## Skill structure

```
agentic-ai-security/
├── SKILL.md                              # Main skill — loaded when triggered
└── references/
    ├── risk-taxonomy.md                  # Five risk categories with scenario examples
    ├── best-practices-checklist.md       # Full per-phase checklists (Design/Develop/Deploy/Operate)
    └── ul-certification-mapping.md        # Maps controls to UL 3115 / 2900 / 4600 certification
```

The skill uses **progressive disclosure**: only `SKILL.md` is loaded into context when triggered. The reference files are read on demand when deeper detail is needed, keeping context window usage minimal.

---

## Example interaction

**You:** Design a multi-agent system where an orchestrator delegates research tasks to sub-agents that query external APIs.

**Claude (with skill active):**
> Before generating the orchestration code, let me flag a few security considerations from the agentic AI security framework:
>
> **Privilege**: The orchestrator should not pass its full credential set to sub-agents. Each sub-agent needs its own scoped identity — even if it only lives for one task.
>
> **Structural**: If a sub-agent queries an external API, that response flows back into the LLM's context. This is a prompt injection surface — tag each external response with its trust level before it reaches the orchestrator's reasoning step.
>
> **Confused deputy**: If the orchestrator acts on sub-agent outputs without re-validating the original caller's permissions, a low-privilege user can chain sub-agent calls to escalate. Add a principal verification step before any consequential action.
>
> Here's a scaffold with those controls built in: [...]

---

## Source framework

This skill encodes the security framework from:

**"Careful adoption of agentic AI services"** (2026)  
Co-authored by: ASD's ACSC (Australia) · CISA (USA) · NSA (USA) · NCSC-UK · NCSC-NZ · Canadian Centre for Cyber Security

The framework organises agentic AI security risks into five categories (Privilege, Design/Config, Behaviour, Structural, Accountability) and provides best practices across the full system lifecycle.

Full document: [cyber.gov.au](https://www.cyber.gov.au)

---

## Certification alignment (UL)

For teams building AI into regulated hardware, the controls in this skill double
as conformity-assessment evidence. `references/ul-certification-mapping.md` maps
them to:

- **UL 3115** — *Safety of AI-Based Products* (2025). The skill's four phases align
  with UL 3115's lifecycle and cover six of its nine assessment dimensions
  (security, accountability, robustness, plus partial reliability, safety,
  transparency). Fairness, bias, and full privacy remain a separate workstream.
- **UL 2900** — *Software Cybersecurity for Network-Connectable Products*. Per-request
  access control, secure architecture, tamper-evident logging, and pen/adversarial
  testing are skill defaults.
- **UL 4600** — *Autonomous Product Safety*. Hard constraints and graduated-autonomy
  exit criteria form the safety case.
- **Commanding certified hardware** (UL 1741 inverters, UL 9540 / 9540A energy
  storage, UL 1973 batteries): model the certified operating envelope as a
  deterministic clamp *below* the LLM — never let the agent be the only thing
  between a command and a UL-certified actuator.

This is *alignment*, not a compliance claim. UL certification is granted only by
UL Solutions after testing.

---

## What's covered vs. what's not

**Covered:**
- LLM-based agentic systems (single and multi-agent)
- Tool use, RAG, memory pipelines
- Privilege and identity management
- Behaviour risks including prompt injection and specification gaming
- Structural risks including third-party components and cascading failures
- Monitoring and accountability patterns

**Not covered (out of scope for this skill):**
- Non-agentic LLM usage (chatbots, completion APIs without tool use)
- Infrastructure security (network, cloud, OS hardening)
- Model training and fine-tuning security
- Regulatory compliance (GDPR, SOC 2, etc.)

---

## Contributing

Contributions welcome. Please open an issue before submitting a PR for significant changes.

**Good contributions:**
- New scenario examples from real-world deployments
- Additional code patterns for specific frameworks (LangChain, LlamaIndex, AutoGen, etc.)
- Updates when the source guidance is revised
- Corrections to risk descriptions or mitigations

**Before submitting a PR:**
- Keep `SKILL.md` under 500 lines
- Write description fields in third person
- Ensure code examples are concrete and runnable, not pseudocode stubs
- Test trigger behavior: does Claude activate the skill for the expected prompts?

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

## License

MIT — see [LICENSE](LICENSE).

The source security guidance is © Commonwealth of Australia 2026, published under Creative Commons Attribution 4.0 International. This skill is a derivative work.
