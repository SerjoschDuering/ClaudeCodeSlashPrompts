I want to create a custom slash command in Claude Code that connects to my n8n webhook for the following use case:

## Use Case

- **Name**: [YOUR_USE_CASE_NAME]
- **Description**: [WHAT_YOUR_COMMAND_DOES]
- **Command Name**: `/[YOUR_COMMAND_NAME]`

## n8n Webhook Configuration

- **URL**: `[YOUR_N8N_WEBHOOK_URL]`
- **Method**: `[POST_OR_GET]`
- **Auth Header Name**: `[YOUR_AUTH_HEADER_NAME]` (leave blank if no auth)
- **Auth Token**: `[YOUR_AUTH_TOKEN]` (leave blank if no auth)

## Input Requirements

Define what data your workflow needs:

```json
[
  {
    "name": "[INPUT_NAME_1]",
    "type": "[user_text|file_contents|file_paths|json_data]",
    "description": "[WHAT_THIS_INPUT_IS]",
    "required": [true|false],
    "example": "[EXAMPLE_VALUE]"
  },
  {
    "name": "[INPUT_NAME_2]",
    "type": "[user_text|file_contents|file_paths|json_data]",
    "description": "[WHAT_THIS_INPUT_IS]",
    "required": [true|false],
    "file_patterns": ["[*.extension]", "[**/*.pattern]"]
  }
]
```

**Available input types**:
- `user_text`: Text from command arguments
- `file_contents`: Collected file contents (formatted as markdown)
- `file_paths`: List of file paths only (no contents)
- `json_data`: Custom JSON structure

## Output Configuration

How does your n8n workflow respond?

- **Response Format**: [DESCRIBE_JSON_STRUCTURE]
- **Response Field**: `[JSON_PATH_TO_MAIN_CONTENT]` (e.g., "result", "output.message", "data")
- **Success Indicator**: [HOW_TO_KNOW_SUCCESS] (e.g., "HTTP 200 and response.success === true")

## Requirements

Read the latest docs first: https://docs.claude.com/en/docs/claude-code/slash-commands

**1. Create a user-level slash command** (in `~/.claude/commands/`):

- Command: `/[YOUR_COMMAND_NAME]`
- Location: `~/.claude/commands/[YOUR_COMMAND_NAME].md`
- Script: `~/.claude/scripts/[YOUR_COMMAND_NAME].sh`

**2. Design an intuitive command signature** based on my inputs above.

Examples:
- For file analysis: `/[COMMAND] <directory> [options] -- [additional-args]`
- For deployments: `/[COMMAND] <environment> -- [message]`
- For simple tasks: `/[COMMAND] [args]`

**3. Input Processing**:

Based on the input configuration I provided:
- Parse required arguments from command line
- If `file_contents` type: Collect files matching specified patterns and format as markdown:
  ```
  ## filename.ext
  ### /full/path/to/filename.ext

  ```[file content]```

  ---
  ```
- If `file_paths` type: List file paths only
- If `user_text` type: Parse from command arguments
- Handle optional inputs with defaults

**4. Webhook Payload**:

Construct the payload based on my inputs. Send as JSON POST request:
```json
{
  "[INPUT_NAME_1]": "value from command args",
  "[INPUT_NAME_2]": "processed data",
  ...
}
```

**5. Response Handling**:

- Send HTTP request to webhook with auth header (if specified)
- Extract response from the field I specified
- Check for success using my success indicator
- Display results with color coding (green=success, red=error, blue=info)
- Show clear error messages if something fails

**6. User Experience**:

- Color-coded output
- Progress indicators (collecting files, sending request, waiting for response)
- Display relevant metrics (file count, request time, response size)
- Clear error messages with helpful suggestions
- Pretty-print responses

**7. Error Handling**:

- Handle network failures gracefully
- Clear messages for authentication errors
- Warn if webhook returns unexpected format
- Suggest checking n8n workflow if errors occur
- Validate inputs before sending request

## Example Usage

```bash
[PROVIDE_EXAMPLE_COMMANDS_FOR_YOUR_USE_CASE]
```

Please implement this integration now, creating an intuitive command signature and user experience based on my requirements.