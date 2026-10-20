# Coding Agent Guide (`mastering-adk`)

## Project Overview

- **Project Name**: `mastering-adk` (manifest: `agents-cli-manifest.yaml`)
- **Root Agent Name**: `mastering_adk` (`app/agent.py` — must stay in sync with `root_agent_name` in `agents-cli-manifest.yaml`)
- **Framework**: Google ADK (`google-adk`) + FastAPI server (`app/fast_api_app.py`) with A2A protocol enabled
- **Local Auth**: Configured via `.env` (`GEMINI_API_KEY` or Vertex AI credentials). `app/fast_api_app.py` uses `load_dotenv(override=True)` so `.env` takes precedence over inherited shell variables.

## Prerequisites & Project-Scoped Skills (`skills.sh`)

Install the CLI (one-time):
```bash
uv tool install google-agents-cli
```

Install or update the official `google-agents-cli` skills **strictly at the project/workspace level** (`.agents/skills/` + `skills-lock.json`, powered by `skills.sh` / `npx skills` under the hood):
```bash
# Via agents-cli (recommended):
agents-cli setup --workspace --skip-auth

# Or directly via skills.sh:
npx skills add https://github.com/google/agents-cli -y

# To update workspace skills later:
agents-cli update --workspace -y
```

### Installed Project Skills (`.agents/skills/`)

| Skill | Purpose |
|-------|---------|
| `google-agents-cli-workflow` | Development workflow, phases, and operational rules |
| `google-agents-cli-scaffold` | Project creation (`scaffold create`), enhancement (`scaffold enhance`), and upgrades |
| `google-agents-cli-adk-code` | ADK Python/Go patterns: `Agent`, tools, `ToolContext`, callbacks, plugins, multi-agent, Workflow API, Live API |
| `google-agents-cli-eval` | Evaluation methodology, dataset schema (`tests/eval/`), metrics, and the eval-fix loop |
| `google-agents-cli-deploy` | Deployment targets (`agent_runtime`, `cloud_run`, `gke`), Terraform, and CI/CD |
| `google-agents-cli-observability` | Cloud Trace, Cloud Logging, and BigQuery Agent Analytics |
| `google-agents-cli-publish` | Publishing deployed agents to Gemini Enterprise / Agent Garden |

---

## Development Phases

### Phase 1: Understand Requirements
Before writing any code, understand the project's requirements, constraints, and success criteria.

### Phase 2: Build and Implement
Implement agent logic in `app/`. Use `agents-cli playground` for interactive testing. Iterate based on user feedback.

### Phase 3: The Evaluation Loop (Main Iteration Phase)
Start with 1-2 eval cases, run `agents-cli eval run`, iterate by making changes and rerunning it until satisfied. Expect 5-10+ iterations. Once you have a baseline, reach for `agents-cli eval compare` (regression diffs), `agents-cli eval analyze` (cluster failure modes), and `agents-cli eval optimize` (auto-tune prompts). See the **Evaluation Guide** for metrics, dataset schema, LLM-as-judge config, and common gotchas.

### Phase 4: Pre-Deployment Tests
Run `uv run pytest tests/unit tests/integration`. Fix issues until all tests pass.

### Phase 5: Deploy to Dev
**Requires explicit human approval.** Run `agents-cli deploy` only after user confirms. See the **Deployment Guide** for details.

### Phase 6: Production Deployment
Ask the user: Option A (simple single-project) or Option B (full CI/CD pipeline with `agents-cli infra cicd`).

## Development Commands

| Command | Purpose |
|---------|---------|
| `agents-cli info` | Show project configuration, paths, and installed skills |
| `agents-cli run "<prompt>"` | Non-interactive single-prompt execution for smoke testing |
| `agents-cli playground` | Interactive local testing |
| `uv run pytest tests/unit tests/integration` | Run unit and integration tests |
| `agents-cli eval dataset synthesize` | Synthesize multi-turn eval scenarios for your agent |
| `agents-cli eval run` | Run the agent over the eval dataset and grade the traces |
| `agents-cli eval generate` / `agents-cli eval grade` | Decoupled form: produce traces, then grade them |
| `agents-cli eval compare` | Compare two grade-results files (regression check) |
| `agents-cli eval analyze` | Cluster failure modes from grade results |
| `agents-cli eval metric list` | List built-in metrics available in the SDK |
| `agents-cli eval optimize` | Auto-tune agent prompts using eval data |
| `agents-cli lint` | Check code quality |
| `agents-cli infra single-project` | Set up project infrastructure (Terraform) |
| `agents-cli deploy` | Deploy to dev |
| `agents-cli scaffold enhance` | Add deployment target or CI/CD to project |
| `agents-cli scaffold upgrade` | Upgrade project to latest version |

---

## Operational Guidelines for Coding Agents

- **Code preservation**: Only modify code directly targeted by the user's request. Preserve all surrounding code, config values (e.g., `model`), comments, and formatting.
- **NEVER change the model** unless explicitly asked.
- **Model 404 errors**: Fix `GOOGLE_CLOUD_LOCATION` (e.g., `global` instead of `us-east1`), not the model name.
- **ADK tool imports**: Import the tool instance, not the module: `from google.adk.tools.load_web_page import load_web_page`
- **Run Python with `uv`**: `uv run python script.py`. Run `agents-cli install` first.
- **Stop on repeated errors**: If the same error appears 3+ times, fix the root cause instead of retrying.
- **Terraform conflicts** (Error 409): Use `terraform import` instead of retrying creation.
