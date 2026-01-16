---
description: Reference guide - Send code to Gemini AI for analysis via Bash tool
---

# Gemini Code Analysis

Send code to external Gemini AI for comprehensive analysis, architecture reviews, or code audits.

## How to Use

Execute via the **Bash tool** with this command:

```bash
~/.claude/scripts/gemini-analyze.sh <paths> <depth> <sessionId> -- <prompt>
```

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `paths` | Yes | Comma-separated paths (NO SPACES). Examples: `src,docs` or `src/App.tsx,src/components` |
| `depth` | Yes | Directory search depth: `1` (shallow), `5` (medium), `10` (full tree) |
| `sessionId` | Yes | Unique session ID for follow-up questions (e.g., `arch-review`, `final-summary`) |
| `-- prompt` | Yes | Your analysis question (MUST come AFTER `--` separator) |

## Examples

**Analyze entire codebase:**
```bash
~/.claude/scripts/gemini-analyze.sh src,docs 5 arch-review -- "Analyze the architecture and identify tech debt"
```

**Specific component review:**
```bash
~/.claude/scripts/gemini-analyze.sh src/components 3 component-audit -- "Review these components for best practices"
```

**Specific files:**
```bash
~/.claude/scripts/gemini-analyze.sh src/App.tsx,src/index.ts 1 entry-point -- "Review entry points"
```

**Multiple directories:**
```bash
~/.claude/scripts/gemini-analyze.sh src/services,src/hooks,src/store 5 final-analysis -- "Provide final executive summary"
```

## Common Mistakes

| ❌ WRONG | ✅ CORRECT | Why |
|-------|---------|-----|
| `"src/**/*.ts"` | `src` | Script finds files automatically, globs not needed |
| `src/components src/hooks` | `src/components,src/hooks` | Use commas, not spaces |
| `-p src -d 5` | `src 5` | Args are positional, not flags |
| `/gemini-analyze src 5 ...` | Bash tool directly | Slash command won't execute the bash |

## Script Features

- **Auto-discovery:** Finds `.ts`, `.tsx`, `.js`, `.jsx`, `.py`, `.java`, `.go`, `.rs`, `.c`, `.cpp`, `.h`, `.css`, `.scss`, `.html`, `.md`, `.json`, `.yaml`, `.sh`, `.sql`
- **Size limits:** Warns at 5MB, hard stops at 10MB to prevent API issues
- **Progress tracking:** Shows file count and cumulative size during collection
- **Integration:** Sends complete files to Gemini AI via n8n webhook

## Follow-up Questions

For follow-up questions in the same analysis session:

```bash
~/.claude/scripts/gemini-ask.sh <sessionId> -- <follow-up question>
```

Example:
```bash
~/.claude/scripts/gemini-ask.sh arch-review -- "How would you refactor the state management?"
```
