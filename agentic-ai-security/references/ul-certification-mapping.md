# UL Certification Mapping

How the controls in this skill support UL safety and cybersecurity certification
of AI-based and network-connectable products.

> **Why this exists.** The ASD/CISA/NSA/NCSC framework is a *security* framework.
> UL certification is a *conformity assessment* regime. They are not the same thing,
> but they overlap heavily on agentic systems. If your AI agents control, optimize,
> or are embedded in physical products that already carry — or are pursuing — UL
> Marks (inverters, energy storage, industrial controls, autonomous systems), the
> evidence this skill makes you produce inline (threat model, audit logs, hard
> constraints, graduated autonomy, identity controls) is the same evidence a UL
> assessment asks for. Build it once, at design time, and you serve both goals.
>
> This file is a *mapping*, not a compliance claim. UL certification is granted
> only by UL Solutions after testing. Use this to align architecture early so the
> assessment finds the controls already in place.

---

## The relevant UL standards

| Standard | Scope | Relevance to agentic AI |
|---|---|---|
| **UL 3115** | Outline of Investigation — *Safety of AI-Based Products* (published 2025-10-31; first product certifications issued 2026-03). | The primary one. Evaluates AI products across **nine dimensions** over the full lifecycle (concept → design → development → deployment → operation). Targets products with autonomous learning and wireless-update capability — i.e. agents. |
| **UL 2900** | *Software Cybersecurity for Network-Connectable Products* (ANSI/UL 2900-1 general; -2-2 industrial control systems). | The cybersecurity layer. Software vulnerability testing, secure architecture, access control, data confidentiality, firmware update integrity, logging, decommissioning, penetration testing. |
| **UL 4600** | *Standard for Autonomous Product Safety* (Ed. 3, 2022). | Safety-case methodology for fully autonomous products. Relevant when an agent operates without a human in the loop. |
| **UL 1741 / 9540 / 9540A / 1973** | Hardware safety: inverters / energy storage systems (system-level) / ESS fire propagation / battery modules. | Context, not AI standards. They define the *certified envelope* an energy-management agent must never push hardware outside of. See "Controlling certified hardware" below. |

---

## UL 3115's nine dimensions ↔ the five risk categories

UL 3115 assesses: **robustness, reliability, transparency, accountability,
privacy, fairness, safety, security, freedom from bias.** This skill's five
categories cover the security-and-control subset of those dimensions directly;
the remainder (fairness, bias, privacy) sit alongside it.

| UL 3115 dimension | Covered by this skill | Where |
|---|---|---|
| **Security** | ✅ Direct | Privilege + Structural categories; runtime auth, allow-lists, identity, prompt-injection defenses |
| **Accountability** | ✅ Direct | Accountability category; audit log schema, source referencing, log integrity (logs not written by the acting agent) |
| **Robustness** | ✅ Direct | Behaviour category; adversarial testing, specification-gaming checks, fail-safe defaults |
| **Reliability** | ◑ Partial | Structural category; cascading-failure containment, hallucination-propagation limits, graceful degradation |
| **Safety** | ◑ Partial | Deploy phase; hard constraints, irreversible-action approval, blast-radius threat model |
| **Transparency** | ◑ Partial | Accountability category; decision-summary logging, instruction hierarchy in system prompt |
| **Privacy** | ○ Adjacent | Hash-not-raw in audit log; data-aggregation risk noted in taxonomy. Full privacy controls are out of scope. |
| **Fairness** | ○ Out of scope | Not a security framework concern; requires separate bias evaluation. |
| **Freedom from bias** | ○ Out of scope | Same — requires dedicated dataset/output bias testing. |

**Takeaway:** this skill gets you most of the way on six of nine UL 3115
dimensions. Treat fairness, bias, and full privacy as a separate workstream the
UL assessment will also require — do not assume security controls cover them.

---

## Lifecycle alignment

UL 3115 explicitly covers "the entire product lifecycle from concept and design
through development, deployment and operation." This skill's four phases map
one-to-one — which is convenient: run the phase you're in, and the artifacts you
produce are organized the way a UL assessment expects to consume them.

| Skill phase | UL 3115 lifecycle stage | Evidence produced that an assessment can use |
|---|---|---|
| **Design** | Concept & design | Per-task permission model, instruction hierarchy, defined human control points, segmentation map |
| **Develop** | Development | Audit log schema, input trust-tagging pipeline, tool allow-list, adversarial test results |
| **Deploy** | Deployment | Threat model (blast radius / worst injection / irreversible action), graduated-autonomy plan, hard-constraint enforcement at two layers |
| **Operate** | Operation | Monitoring signals, anomaly/goal-drift detection, runtime identity verification, rate limiting |

---

## UL 2900 (cybersecurity) ↔ skill controls

UL 2900-1 is the cybersecurity baseline for any network-connectable product. The
mapping is close because the skill already enforces these as defaults.

| UL 2900-1 requirement area | Skill control |
|---|---|
| Access control | Per-request permission evaluation; minimum scope per task; deny-by-default allow-lists |
| Secure architecture | Environment segmentation; explicit trust boundaries; defense in depth (system prompt **and** programmatic checks) |
| Data confidentiality | Hash sensitive values in logs; trust-level tagging of every data source |
| Logging / audit | Structured audit log schema built in from day one; logs not writable by the acting agent |
| Firmware/update integrity | (Hardware-side) — pair with cryptographic attestation: "agents prove unmodified code" in the Operate checklist |
| Penetration testing | Red-teaming + adversarial prompt testing + capability-elicitation in the Develop checklist |
| Decommissioning | Delegation chains with expiry timers; revoke identity from the trusted registry |

The graduated-autonomy rollout (Shadow → Supervised → Autonomous) is also a clean
fit for UL 2900's expectation of risk-proportionate controls and for UL 3115's
emphasis on maintained human oversight during scope expansion.

---

## Controlling certified hardware (energy & industrial context)

When an agent **does not itself get certified** but **commands hardware that is**
— a virtual power plant dispatching UL 9540 energy storage, an energy-management
agent curtailing a UL 1741 inverter, an agent issuing setpoints to UL 2900-2-2
industrial controls — the certification risk is different and often overlooked:

- The hardware is certified to operate inside a **tested envelope** (voltage,
  state-of-charge, thermal, anti-islanding, ramp-rate limits). An agent that can
  issue setpoints can, in principle, drive hardware *outside* that envelope and
  void the safety basis the UL Mark rests on.
- **Control:** model the certified envelope as a **hard constraint** (the
  `HARD_CONSTRAINTS` pattern) enforced programmatically *below* the agent — the
  agent proposes, a deterministic safety layer clamps. Never let the LLM be the
  only thing between a command and a UL-certified actuator.
- **Control:** classify any setpoint that could exit the envelope as an
  irreversible / high-impact action → mandatory human approval or a signed,
  rate-limited command path (Operate checklist).
- **Control:** the audit log must capture the commanded setpoint *and* the
  envelope check result, so a post-incident review can prove the agent never
  drove the device out of its certified range.

This is the single most important addition for a regulated hardware company: the
agent is a new, software-defined path to the actuator, and the safety case must
account for it explicitly.

---

## Practical checklist — "will this survive a UL assessment?"

- [ ] Lifecycle evidence exists for all four phases, not just code (design docs, threat model, test results, monitoring config)
- [ ] Audit logs are structured, tamper-evident, and not written by the acting agent
- [ ] Every privileged action verifies identity at runtime (no cached/startup-only auth)
- [ ] Hard constraints are enforced in **two** independent layers, at least one deterministic and below the LLM
- [ ] Where an agent commands certified hardware, the certified envelope is a programmatic clamp, not a prompt instruction
- [ ] Graduated autonomy has documented exit criteria per stage
- [ ] Fairness, bias, and full privacy are tracked as a **separate** workstream (this framework does not cover them)
- [ ] You are not claiming UL certification — only alignment. Certification is granted by UL Solutions after testing.

---

## Sources

- [UL Solutions — Launch of AI safety certification services (UL 3115)](https://www.ul.com/news/ul-solutions-launches-landmark-artificial-intelligence-safety-certification-services)
- [UL Solutions — First certifications under the AI safety testing service](https://www.ul.com/news/ul-solutions-issues-first-certifications-under-ai-safety-testing-service-ai-enabled-products)
- [UL Solutions — AI and Autonomous Safety Services (UL 4600, ISO 21448, ISO/PAS 8800, ISO/IEC TR 5469)](https://www.ul.com/services/artificial-intelligence-ai-and-autonomous-safety-services)
- [UL Solutions — Energy Storage System testing and certification (UL 9540 / 9540A / 1973)](https://www.ul.com/services/energy-storage-system-testing-and-certification)
- [UL 2900 cybersecurity standard overview](https://www.intertek.com/iot/cybersecurity/ul-2900-faq/)
- [UL 9540 and 9540A explained — Mayfield Renewables](https://www.mayfield.energy/technical-articles/ul-9540-and-9540a-explained/)
