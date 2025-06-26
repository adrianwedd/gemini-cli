# Gemini CLI Usage Guide

## Overview
Gemini CLI provides an interactive or scriptable way to work with Google's Gemini models right from your terminal. It exposes tools for reading and writing files, running shell commands, fetching the web and more. The CLI consists of a frontend (`packages/cli`) and a backend (`packages/core`) which communicates with the Gemini API and manages tools.

## Quickstart
1. Install Node.js 18 or higher.
2. Run directly with `npx https://github.com/google-gemini/gemini-cli` or install globally:
   ```bash
   npm install -g @google/gemini-cli
   gemini
   ```
3. Choose a color theme.
4. Authenticate when prompted. Using a Google account grants a free daily quota. For higher limits you can set a `GEMINI_API_KEY` from Google AI Studio.

Once authenticated you can type prompts at the `gemini` prompt. Start a project by running `gemini` inside a directory, or pipe a prompt with:
```bash
echo "What is fine tuning?" | gemini
```
### Running the CLI
- **Standard install:**
  ```bash
  npm install -g @google/gemini-cli
  gemini
  ```
- **NPX execution:**
  ```bash
  npx @google/gemini-cli
  ```
- **Run latest commit from GitHub:**
  ```bash
  npx https://github.com/google-gemini/gemini-cli
  ```
- **Run from source:**
  ```bash
  npm link packages/cli
  gemini
  ```
- **Inside the sandbox image:**
  ```bash
  docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.1.1
  ```

## Authentication Options
- **Login with Google.** Good for personal accounts or Code Assist users. May require setting `GOOGLE_CLOUD_PROJECT` if you are on Google Workspace.
- **Gemini API key.** Obtain a key from <https://aistudio.google.com/apikey> and set `GEMINI_API_KEY`.
- **Vertex AI.** Set up Application Default Credentials and the environment variables `GOOGLE_CLOUD_PROJECT`, `GOOGLE_CLOUD_LOCATION` and `GOOGLE_GENAI_USE_VERTEXAI=true`.

See [docs/cli/authentication.md](./docs/cli/authentication.md) for details.

## Using the CLI
Gemini CLI supports interactive commands prefixed with `/`, file inclusion using `@`, and shell commands with `!`.

### Slash Commands
- `/help` – show help for all commands.
- `/tools` – list available tools (toggle descriptions with `desc` or `nodesc`).
- `/memory` – manage context loaded from `GEMINI.md` files (`show`, `refresh`, `add`).
- `/restore` – restore project files from checkpoints when checkpointing is enabled.
- `/stats` – display token usage and session stats.
- `/theme` – change the UI theme.
- `/quit` – exit the CLI.

More commands are described in [docs/cli/commands.md](./docs/cli/commands.md).

### At Commands
Use `@path` to insert file or directory contents into your prompt. The CLI respects `.gitignore` by default. Example:
```
@README.md Summarize this file
```

### Shell Mode
Use `!command` to run a system command or type `!` alone to toggle shell mode, where all input is sent to your shell.

## Core Tools
Here are common built-in tools with example usage:
- **read_file**
  ```
  read_file(path="README.md")
  ```
- **write_file**
  ```
  write_file(file_path="output.txt", content="Hello")
  ```
- **run_shell_command**
  ```
  run_shell_command(command="ls -la")
  ```
- **web_fetch**
  ```
  web_fetch(prompt="Summarize https://example.com")
  ```
- **google_web_search**
  ```
  google_web_search(query="latest AI research")
  ```
- **read_many_files**
  ```
  read_many_files(paths=["src/**/*.ts"])
  ```
- **save_memory**
  ```
  save_memory(fact="My favorite language is Python.")
  ```
## Configuration
Gemini CLI reads configuration from:
1. Defaults within the application.
2. `~/.gemini/settings.json` (user settings).
3. `.gemini/settings.json` in the project directory.
4. Environment variables and `.env` files.
5. Command‑line flags.

Important settings include:
- `theme` – choose a color theme.
- `sandbox` – enable sandboxed tool execution (`true`, `false`, `docker`, or command).
- `autoAccept` – auto‑approve safe tool calls.
- `mcpServers` – connect to Model Context Protocol servers for extra tools.
- `checkpointing.enabled` – save checkpoints before modifications.
- `usageStatisticsEnabled` – control optional data collection.

Environment variables such as `GEMINI_API_KEY`, `GOOGLE_API_KEY`, and `GEMINI_MODEL` can be set in your shell or `.env` file. See [docs/cli/configuration.md](./docs/cli/configuration.md) for the full list.

## Hierarchical Memory (GEMINI.md)
Instructional context can be provided via `GEMINI.md` files at various locations:
- `~/.gemini/GEMINI.md` (global)
- `project/.gemini/GEMINI.md` or parents
- Nested `GEMINI.md` files in subdirectories
The `/memory show` command displays the combined context.

## Checkpointing
When enabled (via `--checkpointing` or settings), Gemini CLI saves a Git snapshot and conversation state before tools modify files. Use `/restore` to list or restore checkpoints. See [docs/checkpointing.md](./docs/checkpointing.md).

## Extensions
Custom functionality can be added with extensions placed in `.gemini/extensions`. Each extension has a `gemini-extension.json` describing extra MCP servers or context files. Workspace extensions override user extensions. See [docs/extension.md](./docs/extension.md).

## Token Caching
API key and Vertex AI users benefit from token caching, which reuses system instructions to reduce costs. Use `/stats` to see cached token savings. OAuth authentication does not currently use caching. See [docs/cli/token-caching.md](./docs/cli/token-caching.md).
## Sandboxing
Enable isolation for shell and file commands.
```bash
# enable sandbox temporarily
gemini -s -p "run tests"
```
Set `GEMINI_SANDBOX=true` or `"sandbox": "docker"` in settings for persistent use.
For custom Dockerfile, add `.gemini/sandbox.Dockerfile` and run:
```bash
BUILD_SANDBOX=1 gemini -s
```

## Telemetry
Collect traces and metrics with OpenTelemetry. Example settings:
```json
{
  "telemetry": {"enabled": true, "target": "local"},
  "sandbox": false
}
```
Start a local collector:
```bash
npm run telemetry -- --target=local
```

## Themes
Change appearance with `/theme` or `"theme"` in settings. Available themes include ANSI, Atom One, Ayu, GitHub and several light variants. See [docs/cli/themes.md](./docs/cli/themes.md) for screenshots.

## Troubleshooting
- Update the CLI via `npm install -g @google/gemini-cli@latest`.
- Configuration files live in `.gemini/settings.json` (project or home directory).
- Token counts appear in `/stats` only when cached tokens are used.
- If sandboxing blocks operations, customize the sandbox profile or disable it.
See [docs/troubleshooting.md](./docs/troubleshooting.md) for more tips.

## Terms of Service
See [docs/tos-privacy.md](./docs/tos-privacy.md) for details on how different authentication methods affect data usage.
## Further Reading
Full documentation is available under the `docs/` directory with sections on architecture, tools, deployment and more. Start with [docs/index.md](./docs/index.md).
