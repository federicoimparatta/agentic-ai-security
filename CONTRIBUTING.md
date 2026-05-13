# Contributing

Thanks for your interest in improving this skill.

## Before opening a PR

1. **Open an issue first** for significant changes — alignment on direction before writing saves time.
2. For small fixes (typos, broken links, outdated snippets), PRs are welcome directly.

## Quality bar for SKILL.md

The [official Anthropic skill authoring guidelines](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) apply. Key constraints:

- `SKILL.md` must stay under 500 lines
- Description fields must be written in **third person**
- All heavy detail goes in `references/`, not in `SKILL.md`
- Code examples must be concrete — no unimplemented stubs like `# TODO: implement`
- No time-sensitive information in the main SKILL.md (put it in a dated section if needed)
- Terminology must be consistent throughout

## Types of contributions

### High value
- **Real-world scenario examples** — concrete cases where the skill would have caught a risk, with before/after code
- **Framework-specific patterns** — how the controls apply in LangChain, LlamaIndex, AutoGen, CrewAI, LangGraph, etc.
- **Source guidance updates** — when ASD/CISA/NCSC publish revisions to the underlying framework
- **Trigger improvements** — if the skill fails to activate for a prompt it should catch, or activates for irrelevant prompts

### Acceptable
- New risk patterns not covered by the current five categories (open an issue to discuss categorization first)
- Additional checklist items with clear justification

### Not accepted
- Expanding scope to non-agentic LLM usage
- Infrastructure / cloud security (out of scope)
- Regulatory compliance content
- Content that hasn't been tested against real agent code

## Testing trigger behavior

Before submitting, test that Claude activates the skill for the right prompts. Representative trigger prompts:

```
"Build a LangChain agent that can search the web and send emails"
"Design a multi-agent system for procurement approval"
"Write a system prompt for a customer support agent with database access"
"How should I manage API keys for my agents?"
"My orchestrator agent needs to delegate to sub-agents — how do I structure permissions?"
```

The skill should NOT activate for:
```
"Write a Python function to parse JSON"
"Explain what an LLM is"
"Help me write a blog post"
```

## Changelog

Update `CHANGELOG.md` with a brief entry under `[Unreleased]` for any change that affects skill behavior.
