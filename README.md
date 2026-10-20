# mastering-adk

Hands-on project for exploring **Google Agent Development Kit (ADK)** patterns, tools, state management, multi-agent orchestration, evaluation, and A2A interoperability.

Scaffolded and managed with [**Google Agents CLI (`agents-cli`)**](https://google.github.io/agents-cli/) (`v1.10.0`) — Google's unified Agent Development Lifecycle toolchain for scaffolding, testing, evaluating, and deploying production-ready AI agents on Google Cloud ([Official Docs](https://google.github.io/agents-cli/) · [GitHub Repository](https://github.com/google/agents-cli) · [ADK Coding with AI Guide](https://adk.dev/tutorials/coding-with-ai/#agents-cli)).

## Project Structure

```
mastering-adk/
├── .agents/
│   ├── plugins/adk-docs/      # Project-scoped plugin exposing the ADK Docs MCP server
│   └── skills/                # Project-scoped ADK skills (installed via skills.sh)
├── app/                       # Core agent code
│   ├── agent.py               # Main agent logic (root_agent = mastering_adk)
│   ├── fast_api_app.py        # FastAPI Backend server + A2A routes
│   └── app_utils/             # App utilities and helpers
├── docs/                      # Detailed architecture & code tour documentation (in Spanish)
├── tests/                     # Unit, integration, and eval suites
├── agents-cli-manifest.yaml   # Project metadata for agents-cli
├── GEMINI.md                  # AI-assisted development guide
├── skills-lock.json           # Lockfile for project-scoped skills
└── pyproject.toml             # Project dependencies
```

> 💡 **Tip:** Use [Antigravity CLI](https://antigravity.google/) for AI-assisted development - project context is pre-configured in `GEMINI.md` alongside project-scoped skills and the `adk-docs` MCP plugin (see [ADK Coding with AI](https://adk.dev/tutorials/coding-with-ai/#agents-cli)).

## Requirements

Before you begin, ensure you have:
- **uv**: Python package manager (used for all dependency management in this project) - [Install](https://docs.astral.sh/uv/getting-started/installation/) ([add packages](https://docs.astral.sh/uv/concepts/dependencies/) with `uv add <package>`)
- **agents-cli**: Google's unified Agent Development Lifecycle CLI for building, evaluating, and deploying ADK agents ([Official Site](https://google.github.io/agents-cli/) · [GitHub Repo](https://github.com/google/agents-cli)) - Install with `uv tool install google-agents-cli`
- **Node.js / npx**: Used by `skills.sh` (`npx skills`) to manage agent skills in `.agents/skills/`
- **Google Cloud SDK**: For GCP services - [Install](https://cloud.google.com/sdk/docs/install)


## Quick Start

Install the `agents-cli` tool and project-scoped skills (into `.agents/skills/` and `skills-lock.json`):

```bash
# 1. Install agents-cli globally via uv
uv tool install google-agents-cli

# 2. Install/update the 7 official ADK skills at the workspace level (uses npx skills under the hood)
agents-cli setup --workspace --skip-auth
# Equivalent direct skills.sh command:
# npx skills add https://github.com/google/agents-cli -y
```

### Project-Scoped ADK Docs MCP Plugin (`.agents/plugins/adk-docs/`)

Following the [ADK Coding with AI guide](https://adk.dev/tutorials/coding-with-ai/#agents-cli), this repository includes a workspace-scoped Antigravity plugin in `.agents/plugins/adk-docs/`:

- `.agents/plugins/adk-docs/plugin.json` — Declares the local `adk-docs` (`v0.0.1`) plugin.
- `.agents/plugins/adk-docs/mcp_config.json` — Configures the `adk-docs-mcp` stdio server via `uvx` (`mcpdoc`) backed by [`https://adk.dev/llms.txt`](https://adk.dev/llms.txt).

Validate the plugin at any time with:

```bash
agy plugin validate .agents/plugins/adk-docs --strict
```

Install required Python packages:

```bash
agents-cli install
```

Configure authentication in `.env` (for example with `GEMINI_API_KEY`) and test the agent:

```bash
# Non-interactive single-prompt test in terminal
agents-cli run "What is the weather and current time in San Francisco?"

# Interactive local web playground
agents-cli playground
```

You can also use features from the [ADK](https://adk.dev/) CLI with `uv run adk`.

## Commands

| Command              | Description                                                                                 |
| -------------------- | ------------------------------------------------------------------------------------------- |
| `agents-cli install` | Install dependencies using uv                                                         |
| `agents-cli setup --workspace --skip-auth` | Install the ADK skills suite into `.agents/skills/` (workspace scope) |
| `agents-cli update --workspace -y` | Update workspace skills in `.agents/skills/` to their latest versions |
| `agents-cli info`    | Show project configuration, paths, and installed skills                               |
| `agents-cli run "<prompt>"` | Run the agent with a single prompt from the terminal (non-interactive)         |
| `agents-cli playground` | Launch local development environment                                                  |
| `agents-cli lint`    | Run code quality checks                                                               |
| `agents-cli eval`    | Evaluate agent behavior (generate, grade, analyze, and more — see `agents-cli eval --help`) |
| `uv run pytest tests/unit tests/integration` | Run unit and integration tests                                                        |
| `agents-cli deploy`  | Deploy agent to Agent Runtime                                                                |
| `agents-cli publish gemini` | Register deployed agent with Gemini                                    |
| [A2A Inspector](https://github.com/a2aproject/a2a-inspector) | Launch A2A Protocol Inspector                                                        |

## 🛠️ Project Management

| Command | What It Does |
|---------|--------------|
| `agents-cli scaffold enhance` | Add CI/CD pipelines and Terraform infrastructure |
| `agents-cli infra cicd` | One-command setup of entire CI/CD pipeline + infrastructure |
| `agents-cli scaffold upgrade` | Auto-upgrade to latest version while preserving customizations |

---

## Development

Edit your agent logic in `app/agent.py` and test with `agents-cli playground` - it auto-reloads on save.

## Deployment

```bash
gcloud config set project <your-project-id>
agents-cli deploy
```

To add CI/CD and Terraform, run `agents-cli scaffold enhance`.
To set up your production infrastructure, run `agents-cli infra cicd`.

## Observability

Built-in telemetry exports to Cloud Trace, BigQuery, and Cloud Logging.

## A2A Inspector

This agent supports the [A2A Protocol](https://a2a-protocol.org/). Use the [A2A Inspector](https://github.com/a2aproject/a2a-inspector) to test interoperability.
See the [A2A Inspector docs](https://github.com/a2aproject/a2a-inspector) for details.
