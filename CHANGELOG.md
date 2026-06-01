# Changelog

All notable changes to this skill will be documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)

---

## [Unreleased]

### Added
- `references/ul-certification-mapping.md` — maps the framework's controls to UL certification regimes: UL 3115 (Safety of AI-Based Products, 2025), UL 2900 (software cybersecurity for network-connectable products), and UL 4600 (autonomous product safety). Includes a dimension-by-dimension mapping to UL 3115's nine criteria, lifecycle alignment, and guidance for agents that command UL-certified energy/industrial hardware (UL 1741 / 9540 / 9540A / 1973).
- `SKILL.md`: "Certification alignment" section and UL trigger keywords in the activation description.

## [1.0.0] — 2026-05-13

### Added
- Initial release
- `SKILL.md` covering four lifecycle phases: Design, Develop, Deploy, Operate
- Five risk categories: Privilege, Design/Config, Behaviour, Structural, Accountability
- `references/risk-taxonomy.md` — full risk breakdown with scenario examples
- `references/best-practices-checklist.md` — per-phase checklists
- Code patterns for input sanitization, audit logging, runtime authentication, progressive deployment, and hard constraints
- Red flags section for quick review during code generation

### Source
Based on "Careful adoption of agentic AI services" (2026), ASD/CISA/NSA/NCSC joint guidance.
