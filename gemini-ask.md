---
description: Reference guide - Ask follow-up questions in Gemini analysis session via Bash tool
---

# Gemini Follow-Up Questions

Ask additional questions about code that's already been analyzed by Gemini AI in a previous analysis session.

## How to Use

Execute via the **Bash tool** with this command:

```bash
~/.claude/scripts/gemini-ask.sh <sessionId> -- <prompt>
```

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `sessionId` | Yes | The exact session ID from your initial `gemini-analyze` command (e.g., `arch-review`) |
| `-- prompt` | Yes | Your follow-up question (MUST come AFTER `--` separator) |

## Prerequisites

- Must have previously run `gemini-analyze` with the same `sessionId`
- Session context includes all files sent in the initial analysis
- You can ask multiple follow-up questions in one session

## Examples

**Security analysis follow-up:**
```bash
~/.claude/scripts/gemini-ask.sh arch-review -- "Are there any security vulnerabilities I should fix?"
```

**Refactoring question:**
```bash
~/.claude/scripts/gemini-ask.sh final-analysis -- "How would you refactor the state management to reduce complexity?"
```

**Performance investigation:**
```bash
~/.claude/scripts/gemini-ask.sh component-audit -- "Which components are performance bottlenecks and how to optimize?"
```

**Clarification request:**
```bash
~/.claude/scripts/gemini-ask.sh arch-review -- "Can you explain why you recommended Web Workers for this?"
```

## When to Use

- Ask deeper questions about the analyzed code
- Request clarification on previous Gemini response
- Explore alternative implementations or refactoring strategies
- Get specific recommendations for identified issues
- Follow up on technical debt or architecture improvements

## Related Commands

Start a new analysis session:
```bash
~/.claude/scripts/gemini-analyze.sh <paths> <depth> <sessionId> -- <prompt>
```
