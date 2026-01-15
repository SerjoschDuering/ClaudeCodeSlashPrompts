I want to create custom slash commands in Claude Code that allow me to analyze my codebase using Google Gemini AI via my n8n webhook.

## My Configuration

- **n8n Webhook URL**: `[YOUR_N8N_WEBHOOK_URL]`
  - Example: `https://your-n8n.com/webhook/geminiAnalysCode`
- **Auth Header**: `[YOUR_AUTH_HEADER]` (leave blank if no auth)
  - Example: `X-Webhook-Auth: your-secret-token`
- **Response Field**: `response` (the JSON field containing Gemini's response)
  - Adjust if your n8n workflow returns a different field name

## Requirements

Read the latest docs first: https://docs.claude.com/en/docs/claude-code/slash-commands

**1. Create two user-level slash commands** (in `~/.claude/commands/`):

- `/gemini-analyze <paths> <depth> <sessionId> -- <prompt>`
  - Collects files from the codebase (supports comma-separated paths)
  - Sends to n8n webhook for Gemini analysis
  - n8n handles session storage for follow-ups
  - Size limits: Warns at 5MB, stops at 10MB

- `/gemini-ask <sessionId> -- <prompt>`
  - Follow-up questions using session stored in n8n
  - Only sends the new question + sessionId
  - No files re-sent

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

**3. n8n Webhook Payload Format**:

**Initial Analysis Request**:
```json
{
  "sessionId": "string",
  "prompt": "user's analysis prompt",
  "files": "markdown formatted file contents",
  "isFollowUp": false
}
```

**Follow-up Request**:
```json
{
  "sessionId": "string",
  "prompt": "user's follow-up question",
  "isFollowUp": true
}
```

**4. Expected n8n Response Format**:

Your n8n workflow should return JSON with the AI response in the field you specified above.

Example:
```json
{
  "output": "AI response text here"
}
```

**5. Shell Script Implementation Details**:

The bash scripts should include:
- Argument parsing with `--` separator support
- JSON payload construction using `jq` for proper escaping
- Temp files for large payloads (avoid shell ARG_MAX limits)
- File size tracking and limits (5MB warning, 10MB hard limit)
- Color-coded output (success=green, error=red, info=blue)
- Progress indicators during file collection
- HTTP response handling (200, 413, 408, 504, etc.)
- Helpful error messages and suggestions

**6. Implementation Files**:

- Commands: `~/.claude/commands/gemini-analyze.md` and `~/.claude/commands/gemini-ask.md`
- Scripts: `~/.claude/scripts/gemini-analyze.sh` and `~/.claude/scripts/gemini-ask.sh`

**7. User Experience**:

- Color-coded output (success/error/info)
- Progress indicators (collecting files, sending request)
- Display file count
- Pretty-print AI responses
- Clear error messages with suggestions

**8. Error Handling**:

- Handle network failures gracefully
- Clear messages for authentication errors
- Warn if webhook returns unexpected format
- Suggest checking n8n workflow if errors occur

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

# Follow-up questions (n8n retrieves stored context)
/gemini-ask review123 -- Are there any security concerns?
/gemini-ask review123 -- How would you refactor the state management?
```

## Important Notes

**Common Mistakes to Avoid:**
- ❌ `src/components src/hooks` - Don't use spaces between paths
- ✅ `src/components,src/hooks` - Use commas without spaces
- ❌ `"src/**/*.ts"` - No glob patterns needed, script finds files automatically
- ❌ `"src/*.{ts,tsx}"` - Brace expansion doesn't work
- ✅ `src` - Just provide the directory

Please implement this integration now.