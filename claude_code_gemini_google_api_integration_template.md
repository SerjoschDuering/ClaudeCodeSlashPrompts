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

- `/gemini-analyze <paths> <depth> <sessionId> -- <prompt>`
  - Collects files from the codebase (supports comma-separated paths)
  - Sends to Google Gemini API directly
  - Stores context locally for follow-ups
  - Size limits: Warns at 5MB, stops at 10MB

- `/gemini-ask <sessionId> -- <prompt>`
  - Follow-up questions using stored session context
  - No files re-sent, loads from `~/.claude/gemini-sessions/`

**2. File Collection Features**:

- **Multiple paths**: Comma-separated (NO spaces): `src/components,src/hooks,src/utils`
- **Mixed inputs**: Directories + specific files: `src/components,src/App.tsx,src/index.ts`
- **Depth control**: For directories only (1=shallow, 5=medium, 10=deep)
- **Supported extensions**: `.ts`, `.tsx`, `.js`, `.jsx`, `.py`, `.java`, `.go`, `.rs`, `.c`, `.cpp`, `.h`, `.hpp`, `.swift`, `.kt`, `.css`, `.scss`, `.html`, `.md`, `.txt`, `.json`, `.yaml`, `.yml`, `.sh`, `.sql`, `.toml`
- **Size limits**:
  - Warns at 5MB payload
  - Hard limit at 10MB
  - Shows cumulative size during collection
- **Format as markdown**:
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

**5. Shell Script Implementation Details**:

The bash scripts should include:
- Argument parsing with `--` separator support
- JSON payload construction using `jq` for proper escaping
- Temp files for large payloads (avoid shell ARG_MAX limits)
- File size tracking and limits (5MB warning, 10MB hard limit)
- Color-coded output (success=green, error=red, info=blue)
- Progress indicators during file collection
- HTTP response handling (200, 429 rate limit, 413, etc.)
- Helpful error messages and suggestions

**6. Implementation Files**:

- Commands: `~/.claude/commands/gemini-analyze.md` and `~/.claude/commands/gemini-ask.md`
- Scripts: `~/.claude/scripts/gemini-analyze.sh` and `~/.claude/scripts/gemini-ask.sh`

**7. User Experience**:

- Color-coded output (success/error/info)
- Progress indicators (collecting files, calling API)
- Display file count and estimated token usage
- Pretty-print AI responses
- Show API usage/cost information
- Provide helpful next-step suggestions

**8. Error Handling**:

- Handle API rate limits gracefully
- Clear error messages for authentication failures
- Warn if content exceeds model's token limit
- Suggest alternatives (file filtering) for large codebases

## Example Usage

```bash
# Analyze single directory
/gemini-analyze src/components 5 review123 -- Review this code for best practices

# Analyze multiple directories
/gemini-analyze src/components,src/hooks,src/utils 5 arch_review -- Analyze the architecture

# Mix directories and specific files
/gemini-analyze src/components,src/App.tsx,src/index.ts 3 mixed_review -- Review this code

# Specific files only
/gemini-analyze src/App.tsx,src/index.ts,package.json 1 file_review -- Analyze these files

# Follow-up questions (loads from local session storage)
/gemini-ask review123 -- What security vulnerabilities do you see?
/gemini-ask review123 -- How would you refactor the state management?
```

## Important Notes

**Common Mistakes to Avoid:**
- ❌ `src/components src/hooks` - Don't use spaces between paths
- ✅ `src/components,src/hooks` - Use commas without spaces
- ❌ `"src/**/*.ts"` - No glob patterns needed, script finds files automatically
- ❌ `"src/*.{ts,tsx}"` - Brace expansion doesn't work
- ✅ `src` - Just provide the directory

Please implement this integration now, researching the latest API documentation first.