I want to create custom slash commands in Claude Code that allow me to analyze my codebase using Google Gemini AI via my n8n webhook.

## My Configuration

- **n8n Webhook URL**: `[YOUR_N8N_WEBHOOK_URL]`
- **Auth Header Name**: `[YOUR_AUTH_HEADER_NAME]` (leave blank if no auth)
- **Auth Token**: `[YOUR_AUTH_TOKEN]` (leave blank if no auth)
- **Response Field**: `[JSON_FIELD_PATH]` (e.g., "output", "response", "data.message")

## Requirements

Read the latest docs first: https://docs.claude.com/en/docs/claude-code/slash-commands

**1. Create two user-level slash commands** (in `~/.claude/commands/`):

- `/gemini-analyze <directory|pattern> <depth> <sessionId> -- <prompt>`
  - Collects files from the codebase
  - Sends to n8n webhook for Gemini analysis
  - n8n handles session storage for follow-ups

- `/gemini-ask <sessionId> -- <prompt>`
  - Follow-up questions using session stored in n8n
  - Only sends the new question + sessionId

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

**5. Implementation Files**:

- Commands: `~/.claude/commands/gemini-analyze.md` and `~/.claude/commands/gemini-ask.md`
- Scripts: `~/.claude/scripts/gemini-analyze.sh` and `~/.claude/scripts/gemini-ask.sh`

**6. User Experience**:

- Color-coded output (success/error/info)
- Progress indicators (collecting files, sending request)
- Display file count
- Pretty-print AI responses
- Clear error messages with suggestions

**7. Error Handling**:

- Handle network failures gracefully
- Clear messages for authentication errors
- Warn if webhook returns unexpected format
- Suggest checking n8n workflow if errors occur

## Example Usage

```bash
# Analyze components
/gemini-analyze src/components 2 kpi_analysis -- Analyze the state management patterns and identify potential issues

# Follow-up questions (n8n retrieves stored context)
/gemini-ask kpi_analysis -- How does this compare to React best practices?
/gemini-ask kpi_analysis -- What security improvements would you recommend?
```

Please implement this integration now.