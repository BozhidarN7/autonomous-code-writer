# Autonomous Code Writer

Multi-Agent GitHub Workflow using LangGraph and LangChain.

## Overview

An autonomous coding assistant that analyzes a GitHub repository you own, proposes a useful feature, implements it on a safe branch, opens a pull request, reviews its own work, and leaves the final merge decision to you.

**You provide:** a GitHub repo URL + a high-level request (e.g. *"Suggest a useful improvement for my Python project"*).

**The system does:** clone → analyze → propose → **ask you** → create issue → create branch → implement → verify → **ask you again** → open PR → self-review → post review comment.

**Safety:** never pushes to default branch, never merges PRs, never acts without your approval.

## Architecture

```
User Request
    │
    ▼
┌─────────────────┐
│ Request Parser  │  Extract repo URL, owner, name
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Repository      │  Clone, inspect files, detect language,
│ Analyst Agent   │  framework, test commands
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Feature         │  Generate conservative feature ideas,
│ Strategist Agent│  select best, build proposal
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ HITL Gate 1     │  INTERRUPT: approve / reject / revise
│ Human Review    │  the feature proposal
└─────────────────┘
    │ approve
    ▼
┌─────────────────┐
│ GitHub          │  Create issue + safe branch
│ Coordinator     │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Implementation  │  Plan + focused code changes on branch
│ Agent           │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Verification    │  Detect & run tests/lint, capture results
│ Node            │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ HITL Gate 2     │  INTERRUPT: approve / reject / revise
│ Human PR Review │  the implementation before publishing
└─────────────────┘
    │ approve
    ▼
┌─────────────────┐
│ GitHub          │  Push branch + create PR linking issue
│ Coordinator     │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ PR Review Agent │  Read diff, review for bugs / risks / tests
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Post Review     │  Structured review as GitHub PR comment
│ Comment         │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Final Response  │  Return issue URL, branch, PR URL, review URL
└─────────────────┘
```

## The Five Agents

| # | Agent | Responsibility | Tools |
|---|-------|----------------|-------|
| 1 | **Repository Analyst** | Understand the target repo before proposing changes | Git clone, file tree, metadata detection |
| 2 | **Feature Strategist** | Generate conservative feature ideas based on analysis | LLM reasoning over repo summary |
| 3 | **GitHub Coordinator** | All GitHub write actions (issue, branch, PR, comment) | GitHub API |
| 4 | **Implementation Agent** | Plan and execute focused code changes on the branch | File read/write, diff generation, LLM editing |
| 5 | **PR Review Agent** | Self-review the generated diff | GitHub diff retrieval, LLM review reasoning |

## State Object (TypedSchema)

The graph state carries these fields across nodes:

| Field | Description |
|-------|-------------|
| `user_request` | Original natural-language request |
| `repo_url` | Parsed GitHub repository URL |
| `repo_owner` | Repository owner |
| `repo_name` | Repository name |
| `repo_summary` | Detected language, framework, test commands, key files |
| `feature_proposal` | Title, body, scope, risk, acceptance criteria |
| `human_decision_gate1` | `approve` / `reject` / `revise` + feedback |
| `issue_url` | Created GitHub issue URL |
| `issue_number` | Created GitHub issue number |
| `branch_name` | Safe implementation branch |
| `implementation_plan` | Agent-generated plan for the change |
| `changed_files` | List of modified files |
| `diff_summary` | Concise diff description |
| `verification_results` | Test/lint output and pass/fail status |
| `human_decision_gate2` | `approve` / `reject` / `revise` + feedback |
| `pr_url` | Created pull request URL |
| `pr_number` | Created pull request number |
| `review_summary` | Structured PR review findings |
| `review_comment_url` | URL of posted GitHub review comment |
| `messages` | Conversation history (memory) |
| `error` | Error message if workflow fails |

## Memory & Checkpointing

The graph uses a **memory checkpointer** (`MemorySaver` or equivalent) so that:
- State is preserved across Human-in-the-Loop interruptions.
- The workflow can resume from the exact node where it paused.
- Revision loops retain prior context.
- Conversation history is available to every agent.

## Assignment Requirements Mapping

| # | Requirement | Implementation |
|---|-------------|----------------|
| 1 | Multi-agent workflow (≥2 agents) | Five distinct agents |
| 2 | LangGraph for stateful workflow | StateGraph with typed schema, nodes, edges, checkpointing |
| 3 | LangChain for model/tool integration | OpenAI chat model via LangChain, tool wrappers |
| 4 | Multiple distinct tools | Git clone, file inspection, GitHub API, test/lint execution |
| 5 | Conversational memory | Memory checkpointer preserving state and messages |
| 6 | Human-in-the-Loop interruptions | Two HITL gates: feature approval + PR approval |
| 7 | `execute_workflow(user_request: str)` | Single public function initializing graph, handling interrupts |
| 8 | Five test cases | Five separate calls with distinct requests on one demo repo |
| 9 | At least one approval test | Happy path with `approve` at both gates |
| 10 | At least one revision test | Feature revision + implementation revision demonstrated |
| 11 | Visible state transitions | Notebook prints state at major nodes |
| 12 | Grader-friendly structure | Markdown docs before every code section |

## Safety Boundaries

- **No default-branch pushes.** All work happens on a new branch.
- **No auto-merge.** The system never merges pull requests.
- **No secrets in output.** API keys loaded from env/Colab Secrets; never printed.
- **Conservative changes.** Avoids auth rewrites, migrations, deployment changes, broad dependency upgrades.
- **Best-effort verification.** Tests/lint run when detectable; missing verification is reported, not hidden.
- **Prototype scope.** Small-to-medium features in repositories you own.

## Human Responsibility

You retain final authority at every critical point:
- **Feature approval** — You decide whether the proposed feature is worth an issue.
- **Implementation approval** — You decide whether the code changes are good enough to publish.
- **Merge approval** — The system **never** merges automatically. You review and merge (or close) through normal GitHub processes.

## GitHub Token Permissions

- **Fine-grained token:** Contents read/write, Issues read/write, Pull requests read/write, Metadata read-only.
- **Classic token:** Repository scope sufficient for issue, branch, commit, PR, and comment operations.

## Running in Google Colab

1. Upload or open the notebook in Google Colab.
2. Store `OPENAI_API_KEY` and `GITHUB_TOKEN` in **Colab Secrets** (left sidebar → Secrets).
3. Run cells sequentially. The notebook will pause at HITL gates and prompt for input.
4. Respond with `approve`, `reject`, or `revise: <your feedback>`.

## Submission

Package the `.ipynb` notebook (and optionally this `README.md`) into a ZIP file for submission.

## Limitations

- No web UI — notebook interface only.
- No webhook handling.
- No auto-merge.
- No production-grade sandboxing.
- No multi-repository orchestration.
- No deployment or release creation.
- Full inline code review comments are out of scope; review is posted as a single structured PR comment.
