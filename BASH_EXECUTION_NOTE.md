# About !bash in Claude Commands

## The Problem

Claude Code's documentation claims that `!bash` commands in markdown files auto-execute when slash commands are invoked. **This doesn't work reliably.**

```markdown
!bash ~/.claude/scripts/gemini-analyze.sh $ARGUMENTS
```

When you run `/gemini-analyze`, the documentation **should** auto-execute the bash, but it doesn't.

## Our Approach

**Don't rely on `!bash` execution.** Instead:

1. **Markdown files = Pure reference guides** (what to type, examples, arguments)
2. **Users invoke bash directly** via the Bash tool
3. **We execute it reliably**

## Before (Broken)
- Markdown: `!bash ~/.claude/scripts/gemini-analyze.sh $ARGUMENTS`
- User: `/gemini-analyze src docs 5 session -- "prompt"`
- Result: ❌ Markdown displays, bash doesn't execute

## After (Works)
- Markdown: Pure reference guide with clear examples
- User: Uses Bash tool directly with the exact command
- Result: ✅ Always executes, always works

## Files Using This Pattern

- `gemini-analyze.md` - Reference guide (no `!bash`)
- `gemini-ask.md` - Reference guide (no `!bash`)

Both explain how to use the Bash tool directly with clear examples.
