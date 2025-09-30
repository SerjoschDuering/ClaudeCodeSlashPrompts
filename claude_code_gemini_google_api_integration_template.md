I want to create custom slash commands in Claude Code that allow me to analyze my codebase using Google Gemini AI directly via the official Google REST API.

## My Configuration

- **Gemini API Key**: `[YOUR_GOOGLE_GEMINI_API_KEY]`
- **Model**: `[YOUR_MODEL_CHOICE]` (e.g., gemini-2.0-flash-exp, gemini-exp-1206, gemini-2.0-flash-thinking-exp-01-21)
- **Session Storage**: `[local_files|none]`

## Research Task

Before implementing, research the latest Google Gemini API documentation at https://ai.google.dev/gemini-api/docs to ensure you're using:
- Current API endpoint URLs and authentication methods
- Latest request/response formats and best practices
- Token limits and context window capabilities

## Requirements

**1. Create two user-level slash commands** (in `~/.claude/commands/`):

- `/gemini-analyze <directory|pattern> <depth> <sessionId> -- <prompt>`
  - Collects files from the codebase
  - Sends to Gemini for analysis
  - Stores context for follow-ups (if session storage enabled)

- `/gemini-ask <sessionId> -- <prompt>`
  - Follow-up questions using stored session context
  - No need to re-send files

**2. File Collection Features**:

- Support directory paths with depth control: `src/components 2`
- Support file patterns: `*.ts`, `**/*.tsx`
- Include these extensions: `.ts`, `.tsx`, `.js`, `.jsx`, `.py`, `.java`, `.go`, `.rs`, `.c`, `.cpp`, `.h`, `.css`, `.html`, `.md`, `.txt`, `.json`
- Format as markdown:
  ```
  ## filename.ts
  ### /full/path/to/filename.ts

  ```[file content]```

  ---
  ```

**3. Google Gemini API Integration**:

Use the latest REST API structure from https://ai.google.dev/gemini-api/docs:

```bash
curl "https://generativelanguage.googleapis.com/v1beta/models/{MODEL}:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
    "contents": [{
      "parts": [{
        "text": "COMBINED_PROMPT_AND_FILES"
      }]
    }]
  }'
```

**Initial Analysis**: Combine user prompt + collected files, send to Gemini, store context locally

**Follow-up**: Load stored context, append new question, send to Gemini (or resend everything if storage disabled)

**4. Session Management** (if `local_files` storage):

- Create directory: `~/.claude/gemini-sessions/`
- Store sessions: `~/.claude/gemini-sessions/{sessionId}.md`
- Include metadata: timestamp, file count, original prompt

**5. Implementation Files**:

- Commands: `~/.claude/commands/gemini-analyze.md` and `~/.claude/commands/gemini-ask.md`
- Scripts: `~/.claude/scripts/gemini-analyze.sh` and `~/.claude/scripts/gemini-ask.sh`

**6. User Experience**:

- Color-coded output (success/error/info)
- Progress indicators (collecting files, calling API)
- Display file count and estimated token usage
- Pretty-print AI responses
- Show API usage/cost information
- Provide helpful next-step suggestions

**7. Error Handling**:

- Handle API rate limits gracefully
- Clear error messages for authentication failures
- Warn if content exceeds model's token limit
- Suggest alternatives (file filtering) for large codebases

## Example Usage

```bash
# Analyze React components
/gemini-analyze src/components 2 react_patterns -- Analyze React component patterns and suggest improvements

# Follow-up questions (reuses context, no files re-sent)
/gemini-ask react_patterns -- What security vulnerabilities do you see?
/gemini-ask react_patterns -- How would you refactor the state management?
```

Please implement this integration now, researching the latest API documentation first.