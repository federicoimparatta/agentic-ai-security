# Best Practices Checklists — Per Lifecycle Phase

Source: ASD/CISA/NSA/NCSC "Careful adoption of agentic AI services" (2026)

---

## DESIGN

### Controlled context
- [ ] Prompt context uses a clear instruction hierarchy
- [ ] Trust level of each data source is explicitly modeled
- [ ] RAG/grounding implemented to reduce hallucination risk
- [ ] External data sources tagged by trust level before injection into context

### Oversight mechanisms
- [ ] Human control points defined in the design (not delegated to the agent)
- [ ] Explicit control flows bound autonomous planning
- [ ] Live monitoring and interruption capability during task execution
- [ ] Mandatory human approval defined for high-impact decision steps
- [ ] Reversibility mechanism defined for post-execution correction

### Identity management
- [ ] Each agent is a distinct principal with unique cryptographic identity
- [ ] Agent identity anchored via managed identity service, DIDs, or PKI
- [ ] All inter-agent and agent-to-service calls use mutual TLS
- [ ] Trusted registry maintained; identities bound to authorized roles
- [ ] Any agent/key not in registry is denied by default

### Defense in depth
- [ ] No single security mechanism relied upon exclusively
- [ ] Security controls at all data entry/exit points
- [ ] Agents for different functions are separated with strict handoff controls

---

## DEVELOP

### Testing
- [ ] Reward modeling and adversarial testing for specification gaming
- [ ] Training/testing in simulated controlled environments
- [ ] Synthetic adversarial examples reflecting real deployment scenarios
- [ ] Active learning on high-uncertainty inputs

### Evaluation
- [ ] Threat-model-based evaluation scenarios including edge cases
- [ ] Best-of-N sampling used to surface full behavior range
- [ ] Evaluation across different autonomy levels
- [ ] Contextual variation in evals (presence/absence of other agents, timing)
- [ ] Continuous evaluation across development lifecycle

### Input management
- [ ] Robust input validation and sanitization for all inputs
- [ ] Prompt injection filters and semantic analysis integrated
- [ ] Context validation before execution

### Red teaming
- [ ] Sandbox environments deployed before production
- [ ] Red team exercises for loopholes and unintended behaviors
- [ ] Capability elicitation techniques applied
- [ ] Multi-agent red teaming and chaos testing implemented

### Resilience
- [ ] Fail-safe defaults and containment mechanisms embedded
- [ ] Data loss prevention controls tuned to agent behaviors
- [ ] Versioning and rollback mechanisms implemented

### Accountability
- [ ] Comprehensive artifact logging integrated by default
- [ ] Unified audit logs for all inter-agent interactions
- [ ] Interpretability tools for observability of agent decisions
- [ ] Information source referencing required in agent outputs

### Third-party management
- [ ] All external components verified from trusted sources
- [ ] Trusted registry of third-party components maintained
- [ ] Tool use restricted to approved allow-list
- [ ] Tool descriptions standardized (no persuasive language)
- [ ] Agent tool usage logged in human-readable format
- [ ] Trigger-action protocols restrict permissions on unexpected behavior
- [ ] Separation of duties: Orchestrator / Reader / Actuator roles defined
- [ ] Consensus controls by action risk level (multi-agent → human-in-loop)
- [ ] Agents prohibited from modifying own privileges
- [ ] Delegation chains include expiry timers and recorded grants

---

## DEPLOY

### Threat modeling
- [ ] Threat modeling using OWASP GenAI / MITRE ATLAS taxonomies
- [ ] Security controls address emerging agent capabilities
- [ ] Zero Trust principles applied (NIST SP 800-207)
- [ ] Incident response procedures tested
- [ ] Regular third-party reviews of privileged architectures

### Governance
- [ ] Governance policies for autonomous agents implemented
- [ ] Legal accountability and risk ownership defined in policy
- [ ] Organization upskilled on AI literacy

### Progressive deployment
- [ ] Phased deployment with incrementally increasing access/autonomy
- [ ] Graduated autonomy with maintained human oversight
- [ ] Continuous evaluation determines scope expansion or rollback

### Secure by default
- [ ] Fail-safe defaults: agents stop and escalate in uncertain scenarios
- [ ] Error-handling and failover management implemented
- [ ] Graceful degradation models defined

### Guardrails
- [ ] Clear constrained objectives with explicit do-not-do rules
- [ ] Guardrails and hard constraints (deny lists, API-level safety policies)
- [ ] Declarative safety contracts agents cannot override
- [ ] Layered guardrail mechanisms (anomaly detection + rule-based + ML)
- [ ] High-risk incidents reviewed by humans (guardrail triggers, denied actions)
- [ ] Secondary validation agent deployed for new tasks

### Isolation
- [ ] Isolation and segmentation to limit blast radius
- [ ] High-risk agents in distinct domains
- [ ] Agents isolated into enclaves with no write access to their own logs

---

## OPERATE

### Monitoring & auditing
- [ ] Monitoring tools enhance human oversight
- [ ] All agent operations monitored (not just inputs/outputs)
- [ ] Identity and privilege changes monitored and audited for drift
- [ ] Agent outputs monitored for bias, data drift, anomalous patterns
- [ ] Comprehensive logs + real-time monitoring of live behavior
- [ ] Runtime anomaly detection using rules or behavioral baselines
- [ ] Discrepancies between stated intentions and observed behaviors flagged
- [ ] Multiple independent monitoring systems cross-validate
- [ ] Goal drift monitored vs. approved baseline specifications
- [ ] Tool usage and retrieved information integrated in logs
- [ ] Human review combined with automated log analysis
- [ ] Rapid response capability based on monitoring data
- [ ] Storage-efficient logging (no loss of critical signals)
- [ ] Regular penetration testing and agentic red team exercises

### Output validation
- [ ] Critical outputs validated against multiple sources
- [ ] Cross-checking via redundant agents validating each other's outputs
- [ ] Tool responses validated before passing to LLM

### Human in the loop
- [ ] Human approval decisions made by designers/operators (not the agent)
- [ ] High-impact actions require prior human approval
- [ ] Human review checkpoints for irreversible actions
- [ ] Log/audit record deletion requests quarantined for human review
- [ ] Clear accountability assignment for errors and adverse outcomes
- [ ] Risk assessments classify actions by impact, likelihood, reversibility

### Performance monitoring
- [ ] Agent ability to evade security measures assessed
- [ ] Regular evaluation of bypass capabilities
- [ ] Evaluation results used to validate and strengthen controls
- [ ] Rate limiting applied to interrupt long-running or malicious workflows

### Privileges & authentication
- [ ] Minimum required privileges enforced
- [ ] Narrowest possible privilege scope applied
- [ ] Agent reputation and trust scoring implemented
- [ ] Just-in-time credentials for high-impact actions
- [ ] API caller identity verified against user/agent groups
- [ ] Fresh cryptographic proofs required before every privileged call
- [ ] Cryptographic signing required for authorized commands
- [ ] Cryptographic integrity checks for task definitions
- [ ] Cryptographic attestation required (agents prove unmodified code)
- [ ] Continuous identity/authorization verification at runtime
