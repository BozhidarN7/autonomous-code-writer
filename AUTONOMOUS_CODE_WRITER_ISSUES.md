# Autonomous Code Writer Issue Backlog

Source PRD: `AUTONOMOUS_CODE_WRITER_PRD.md`

This backlog breaks the PRD into independently grabbable tracer-bullet issues. The issues are stored locally only and are not published to any remote issue tracker.

## Proposed Breakdown Summary

1. **Notebook assignment shell and visible scenario walkthrough**
   **Type:** AFK
   **Blocked by:** None
   **User stories covered:** 1, 2, 43, 44, 45, 46, 47, 48, 52, 53

2. **Secure runtime setup with OpenAI and GitHub configuration check**
   **Type:** AFK
   **Blocked by:** None
   **User stories covered:** 41, 42

3. **Parse a repository request and inspect a real GitHub repository**
   **Type:** AFK
   **Blocked by:** 2
   **User stories covered:** 3, 4, 5, 6, 58, 59

4. **Generate a feature proposal and pause for human approval**
   **Type:** HITL
   **Blocked by:** 1, 3
   **User stories covered:** 7, 8, 9, 10, 11, 12, 13, 14, 15, 47

5. **Create an approved GitHub issue and safe implementation branch**
   **Type:** HITL
   **Blocked by:** 4
   **User stories covered:** 16, 17, 18, 19, 20, 40

6. **Implement an approved medium feature with focused repository changes**
   **Type:** AFK
   **Blocked by:** 5
   **User stories covered:** 21, 22, 23, 24, 25, 54, 55, 56

7. **Run best-effort verification and prepare implementation for PR approval**
   **Type:** AFK
   **Blocked by:** 6
   **User stories covered:** 26, 27, 28, 29, 31, 32

8. **Push the approved branch and create a traceable pull request**
   **Type:** HITL
   **Blocked by:** 7
   **User stories covered:** 30, 33, 34, 39, 40

9. **Review the created pull request and post a GitHub review comment**
   **Type:** AFK
   **Blocked by:** 8
   **User stories covered:** 35, 36, 37, 38, 57

10. **Expose the end-to-end `execute_workflow` interface with memory and resume handling**
    **Type:** AFK
    **Blocked by:** 4, 7, 9
    **User stories covered:** 45, 46, 47, 48, 60

11. **Add five reproducible notebook test cases against one owned demo repository**
    **Type:** HITL
    **Blocked by:** 10
    **User stories covered:** 49, 50, 51

12. **Document safety boundaries, limitations, and final submission guidance**
    **Type:** AFK
    **Blocked by:** 10, 11
    **User stories covered:** 1, 40, 41, 42, 52, 53

## Issue 1: Notebook assignment shell and visible scenario walkthrough

**Type:** AFK

**Status:** ✅ Completed

### What was built

Created the initial notebook structure for the Autonomous Code Writer assignment. The notebook now contains:

1. **Title and scenario description** — Clear explanation of the autonomous coding assistant scenario, the 10-step workflow, and the human-in-control design.
2. **Architecture text flow** — ASCII diagram showing every node from Request Parser through Final Response, including the two HITL gates, revision loops, and termination paths.
3. **State object documentation** — Typed schema table listing all 20+ state fields and their purposes.
4. **Memory & checkpointing explanation** — How `MemorySaver` preserves state across interruptions and revision cycles.
5. **Five agents documentation** — Repository Analyst, Feature Strategist, GitHub Coordinator, Implementation Agent, and PR Review Agent, each with role, tools, output, and safety notes.
6. **Requirements checklist** — 12-row table mapping assignment requirements to planned components, with status legend.
7. **Human responsibility section** — Explicit statement that the human retains final authority at feature approval, implementation approval, and merge decisions.
8. **Expected GitHub workflow** — Summary of the 5 GitHub artifacts created in sequence (issue, branch, commits, PR, review comment).
9. **Safety boundaries preview** — High-level safety rules before the full documentation is added in Issue 12.
10. **Implementation roadmap** — Placeholder listing all 11 remaining issues with their planned notebook sections.

The notebook is now readable and grader-friendly, with clear markdown sections before any implementation cells. A placeholder code cell confirms Issue 1 completion.

### Acceptance criteria

Create the initial notebook structure for the Autonomous Code Writer assignment. The completed slice should make the project understandable before any complex implementation exists. It should describe the selected scenario, the multi-agent architecture, the assignment requirement mapping, the expected GitHub workflow, and the role of the human reviewer.

The slice is complete when the notebook can be opened and a grader can understand what the system will do, which agents exist, where tools are used, where memory is used, and where Human-in-the-Loop interruptions occur.

### Acceptance criteria

- [x] The notebook contains a clear title and scenario description for the Autonomous Code Writer workflow.
- [x] The notebook includes an architecture diagram or text flow from user request to final PR review comment.
- [x] The notebook includes a requirements checklist mapping the assignment requirements to planned implementation components.
- [x] The notebook explains the five planned agents: Repository Analyst, Feature Strategist, GitHub Coordinator, Implementation Agent, and PR Review Agent.
- [x] The notebook explains that the human user remains responsible for final merge decisions.
- [x] The notebook remains readable and grader-friendly, with clear markdown sections before implementation cells.

### Blocked by

None - can start immediately.

---

## Issue 2: Secure runtime setup with OpenAI and GitHub configuration check

**Type:** AFK

### What to build

Add the runtime setup needed for Colab execution, including dependency installation, imports, secret loading, and model/client initialization. The slice should prove that the notebook can safely read required credentials without hardcoding or printing them.

The completed slice should fail clearly when credentials are missing and should initialize the OpenAI model and GitHub client when credentials are present.

### Acceptance criteria

- [ ] The notebook installs the required dependencies for LangChain, LangGraph, OpenAI, GitHub access, and Git operations.
- [ ] The notebook loads `OPENAI_API_KEY` and `GITHUB_TOKEN` from environment variables or Colab Secrets only.
- [ ] Missing secrets produce clear errors without exposing secret values.
- [ ] The OpenAI chat model is initialized with configurable model name defaulting to `gpt-5.4-mini-2026-03-17`.
- [ ] The GitHub client can authenticate and report the current user or repository access without printing tokens.
- [ ] The setup section documents the required GitHub token permissions.

### Blocked by

None - can start immediately.

---

## Issue 3: Parse a repository request and inspect a real GitHub repository

**Type:** AFK

### What to build

Create the first end-to-end repository-analysis path. Given a natural language user request containing a GitHub repository URL, the workflow should extract the repository owner/name, clone the repository into a temporary working directory, inspect the file tree, detect project metadata, identify likely test or lint commands, and produce a concise repository summary.

This is a tracer bullet through request parsing, GitHub/repository tools, state updates, and visible notebook output.

### Acceptance criteria

- [ ] A user request containing a GitHub repository URL is parsed into owner, repository name, and repository URL.
- [ ] The target repository is cloned into an isolated local working directory.
- [ ] The repository file listing ignores heavy or irrelevant directories such as `.git`, dependency folders, virtual environments, and build artifacts.
- [ ] The system detects common project metadata such as language indicators, package manager files, and likely test or lint commands.
- [ ] The Repository Analyst Agent stores a repository summary in the workflow state.
- [ ] The notebook output shows the repository summary and detected stack without exposing secrets.

### Blocked by

- Issue 2: Secure runtime setup with OpenAI and GitHub configuration check

---

## Issue 4: Generate a feature proposal and pause for human approval

**Type:** HITL

### What to build

Add the feature strategy slice. After repository analysis, the Feature Strategist Agent should generate conservative, useful feature ideas, select the most appropriate option, create an issue-ready title and body, and pause the graph for human approval before any GitHub issue is created.

The slice should demonstrate the first required Human-in-the-Loop interruption. Human responses must support approval, rejection, and revision feedback.

### Acceptance criteria

- [ ] The Feature Strategist Agent uses repository analysis to generate feature ideas relevant to the target repository.
- [ ] The selected feature proposal includes title, body, value, implementation scope, risk level, and acceptance criteria.
- [ ] The workflow pauses before creating any GitHub issue.
- [ ] Human input of `approve` routes the workflow toward issue creation.
- [ ] Human input of `reject` stops the workflow without creating GitHub artifacts.
- [ ] Human input starting with `revise` routes back to regenerate or update the proposal using the feedback.
- [ ] The human decision and feedback are stored in workflow state.

### Blocked by

- Issue 1: Notebook assignment shell and visible scenario walkthrough
- Issue 3: Parse a repository request and inspect a real GitHub repository

---

## Issue 5: Create an approved GitHub issue and safe implementation branch

**Type:** HITL

### What to build

Create the GitHub coordination slice that performs the first real repository write actions after human approval. Once the feature proposal has been approved, the workflow should create a GitHub issue, generate a safe branch name, create a local implementation branch based on the default branch, and store all resulting identifiers and URLs in state.

The completed slice should prove that the system can perform real GitHub issue creation while enforcing branch-based safety boundaries.

### Acceptance criteria

- [ ] A GitHub issue is created only after the feature proposal has been approved by the human user.
- [ ] The issue title and body match the approved feature proposal.
- [ ] The issue URL and issue number are stored in workflow state.
- [ ] The workflow detects the repository default branch.
- [ ] A new implementation branch is created locally and never replaces or modifies the default branch directly.
- [ ] The generated branch name references the issue number and feature slug.
- [ ] The workflow clearly documents that it will not merge pull requests automatically.

### Blocked by

- Issue 4: Generate a feature proposal and pause for human approval

---

## Issue 6: Implement an approved medium feature with focused repository changes

**Type:** AFK

### What to build

Add the implementation slice. After a GitHub issue and feature branch exist, the Implementation Agent should create an implementation plan, modify repository files according to the approved issue, keep the change focused, and produce a diff summary.

The implementation should be safe and conservative. If the repository structure is unclear, the agent should prefer documentation, examples, tests, or small code changes over risky architecture changes.

### Acceptance criteria

- [ ] The Implementation Agent creates a concise implementation plan based on the approved issue.
- [ ] The agent modifies files only on the implementation branch.
- [ ] The changes are focused on the approved issue and avoid unrelated refactors.
- [ ] The agent avoids secrets, credential files, default branch modifications, broad dependency upgrades, migrations, and deployment changes.
- [ ] The agent adds or updates tests when feasible for the selected change.
- [ ] The changed files and git diff are captured in workflow state.
- [ ] The notebook output summarizes the implementation without overwhelming the reader.

### Blocked by

- Issue 5: Create an approved GitHub issue and safe implementation branch

---

## Issue 7: Run best-effort verification and prepare implementation for PR approval

**Type:** AFK

### What to build

Add the verification and PR-preparation slice. After implementation, the workflow should detect and run available test or lint commands where possible, capture results, prepare a pull request title and body, and pause-ready implementation summary for the human user.

The slice is complete when the workflow can show the user what changed, what verification ran, and whether the implementation is ready for PR approval.

### Acceptance criteria

- [ ] The workflow detects likely test or lint commands from repository metadata.
- [ ] The workflow runs best-effort verification commands when available.
- [ ] Verification output, pass/fail status, and unavailable-command notes are captured in state.
- [ ] Verification failure does not automatically discard the implementation, but the failure is made visible.
- [ ] The workflow prepares a PR title and body linking the approved issue and summarizing verification results.
- [ ] The workflow prepares a human-readable implementation summary for PR approval.

### Blocked by

- Issue 6: Implement an approved medium feature with focused repository changes

---

## Issue 8: Push the approved branch and create a traceable pull request

**Type:** HITL

### What to build

Add the second Human-in-the-Loop slice and PR creation path. Before publishing the branch, the workflow should pause and ask the user to approve, reject, or revise the implementation. Approval should commit changes, push the branch, and create a GitHub pull request. Revision should route back to implementation. Rejection should stop without creating a PR.

The completed slice demonstrates the second required HITL interruption and the real GitHub PR creation path.

### Acceptance criteria

- [ ] The workflow pauses before pushing the implementation branch and creating a pull request.
- [ ] The approval prompt includes changed files, diff summary, verification results, and proposed PR metadata.
- [ ] Human input of `approve` commits changes, pushes the feature branch, and creates the pull request.
- [ ] Human input of `reject` stops the workflow before PR creation.
- [ ] Human input starting with `revise` routes back to implementation using the feedback.
- [ ] The pull request links the approved GitHub issue.
- [ ] The PR URL and PR number are stored in workflow state.
- [ ] The workflow never merges the created pull request.

### Blocked by

- Issue 7: Run best-effort verification and prepare implementation for PR approval

---

## Issue 9: Review the created pull request and post a GitHub review comment

**Type:** AFK

### What to build

Add the PR review slice. After the pull request is created, the PR Review Agent should inspect the generated diff, identify risks or findings, produce a structured review summary, and post that review as a GitHub pull request comment.

The completed slice should make the system behave like a self-reviewing autonomous coding assistant while leaving final merge control to the human.

### Acceptance criteria

- [ ] The workflow retrieves or uses the created pull request diff for review.
- [ ] The PR Review Agent reviews for correctness, regressions, security risks, missing tests, and maintainability concerns.
- [ ] The review output lists findings first, or explicitly says no blocking issues were found.
- [ ] The review includes residual risks and verification notes.
- [ ] The review is posted as a GitHub pull request comment.
- [ ] The review summary and comment URL are stored in workflow state when available.
- [ ] The notebook final output includes the PR review result.

### Blocked by

- Issue 8: Push the approved branch and create a traceable pull request

---

## Issue 10: Expose the end-to-end `execute_workflow` interface with memory and resume handling

**Type:** AFK

### What to build

Connect the implemented slices into the required public workflow function. The `execute_workflow` function should accept a single string user request, initialize the LangGraph workflow, use a memory checkpointer, handle interruptions, collect human feedback, resume execution, and return structured final state.

The completed slice should be the primary interface used by all notebook test cases.

### Acceptance criteria

- [ ] The notebook defines `execute_workflow(user_request: str)`.
- [ ] The function initializes or uses the compiled LangGraph workflow.
- [ ] The workflow uses a memory checkpointer to preserve state across interruptions.
- [ ] The function detects graph interruptions and displays the approval prompt clearly.
- [ ] The function collects human feedback and resumes the graph from the interruption.
- [ ] The function supports multiple interruption cycles for revision flows.
- [ ] The returned final state includes repository summary, issue URL, branch name, PR URL, verification results, review summary, and review comment URL where applicable.
- [ ] The notebook output makes the agent interactions and state progress visible.

### Blocked by

- Issue 4: Generate a feature proposal and pause for human approval
- Issue 7: Run best-effort verification and prepare implementation for PR approval
- Issue 9: Review the created pull request and post a GitHub review comment

---

## Issue 11: Add five reproducible notebook test cases against one owned demo repository

**Type:** HITL

### What to build

Add the required assignment test cases. The notebook should include five separate workflow executions against the same owned demo repository, each with a different user request. The test cases should demonstrate the happy path, feature proposal revision, implementation revision, documentation or developer-experience improvement, and maintainability or validation improvement.

The completed slice should make it easy for a grader to reproduce the workflow and see both Human-in-the-Loop approval and revision behavior.

### Acceptance criteria

- [ ] The notebook defines a single configurable demo repository URL used by all test cases.
- [ ] The notebook includes five distinct calls to `execute_workflow` with different initial user requests.
- [ ] At least one test case demonstrates approving the feature proposal and PR creation.
- [ ] At least one test case demonstrates revising the feature proposal before issue creation.
- [ ] At least one test case demonstrates revising the implementation before PR creation.
- [ ] Each test case documents expected human responses for reproducibility.
- [ ] Each test case creates separate issues and branches so the runs do not conflict.
- [ ] The test cases demonstrate real GitHub issue, branch, PR, and review-comment behavior when credentials are configured.

### Blocked by

- Issue 10: Expose the end-to-end `execute_workflow` interface with memory and resume handling

---

## Issue 12: Document safety boundaries, limitations, and final submission guidance

**Type:** AFK

### What to build

Add final notebook documentation that explains how to use the system safely, what the prototype intentionally does not do, and how the assignment should be submitted. The documentation should make the security model, GitHub permissions, human responsibilities, and prototype limitations explicit.

The completed slice should make the notebook ready to package as the final submission artifact.

### Acceptance criteria

- [ ] The notebook documents that API keys must be stored in environment variables or Colab Secrets.
- [ ] The notebook documents required GitHub token permissions.
- [ ] The notebook documents that the workflow never pushes to the default branch and never merges PRs.
- [ ] The notebook documents that the final merge decision belongs to the human user.
- [ ] The notebook documents known limitations such as no web UI, no webhook handling, no auto-merge, and no production-grade sandboxing.
- [ ] The notebook includes guidance for running the project in Google Colab.
- [ ] The notebook includes guidance for packaging the `.ipynb` file, and optional supporting files, into the final ZIP submission.
- [ ] The notebook includes final notes explaining that this is a balanced prototype for the assignment.

### Blocked by

- Issue 10: Expose the end-to-end `execute_workflow` interface with memory and resume handling
- Issue 11: Add five reproducible notebook test cases against one owned demo repository
