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
   **Status:** ✅ Completed
   **Blocked by:** 7

   ### What was built

   Added the second Human-in-the-Loop slice and PR creation path. After implementation and verification, the workflow now:

   1. **Pauses at Gate 2** (`implementation_summary_node`) — displays the proposed PR title, implementation plan, changed files, verification results, and diff summary. Collects `approve`, `reject`, or `revise <feedback>` input.
   2. **Routes Gate 2 decisions** (`route_gate2_decision`) — returns `'approve'`, `'reject'`, or `'revise'` based on the human decision stored in state.
   3. **Pushes the branch** (`push_branch`) — pushes the local implementation branch to the remote using token-based authentication.
   4. **Creates a GitHub pull request** (`create_pull_request`) — creates a PR with the prepared title and body, linking back to the approved issue. Handles duplicate PR gracefully by returning the existing open PR.
   5. **Stores PR metadata** (`create_pr_node`) — stores the PR URL and PR number in workflow state.
   6. **Rejection path** (`reject_pr_node`) — stops the workflow cleanly without pushing or creating a PR.
   7. **Safety guarantees** — the workflow never merges the pull request automatically; merge authority remains with the human.

   ### Acceptance criteria

   - [x] The workflow pauses before pushing the implementation branch and creating a pull request.
   - [x] The approval prompt includes changed files, diff summary, verification results, and proposed PR metadata.
   - [x] Human input of `approve` commits changes, pushes the feature branch, and creates the pull request.
   - [x] Human input of `reject` stops the workflow before PR creation.
   - [x] Human input starting with `revise` routes back to implementation using the feedback.
   - [x] The pull request links the approved GitHub issue.
   - [x] The PR URL and PR number are stored in workflow state.
   - [x] The workflow never merges the created pull request.

   ### Blocked by

   - Issue 7: Run best-effort verification and prepare implementation for PR approval

---

## Issue 9: Review the created pull request and post a GitHub review comment

**Type:** AFK
**Status:** ✅ Completed

### What was built

Added the PR review slice. After the pull request is created, the PR Review Agent inspects the generated diff, identifies risks or findings, produces a structured review summary, and posts that review as a GitHub pull request comment.

The completed slice makes the system behave like a self-reviewing autonomous coding assistant while leaving final merge control to the human.

### Acceptance criteria

- [x] The workflow retrieves or uses the created pull request diff for review.
- [x] The PR Review Agent reviews for correctness, regressions, security risks, missing tests, and maintainability concerns.
- [x] The review output lists findings first, or explicitly says no blocking issues were found.
- [x] The review includes residual risks and verification notes.
- [x] The review is posted as a GitHub pull request comment.
- [x] The review summary and comment URL are stored in workflow state when available.
- [x] The notebook final output includes the PR review result.

### Blocked by

- Issue 8: Push the approved branch and create a traceable pull request

---

## Issue 10: Expose the end-to-end `execute_workflow` interface with memory and resume handling

**Type:** AFK
**Status:** ✅ Completed

### What was built

Connected all implemented slices into the required public workflow function. The notebook now defines `execute_workflow(user_request: str)` which initializes the compiled LangGraph workflow, uses a memory checkpointer, handles interruptions, collects human feedback, resumes execution, and returns structured final state.

The completed slice is the primary interface used by all notebook test cases.

### Acceptance criteria

- [x] The notebook defines `execute_workflow(user_request: str)`.
- [x] The function initializes or uses the compiled LangGraph workflow.
- [x] The workflow uses a memory checkpointer to preserve state across interruptions.
- [x] The function detects graph interruptions and displays the approval prompt clearly.
- [x] The function collects human feedback and resumes the graph from the interruption.
- [x] The function supports multiple interruption cycles for revision flows.
- [x] The returned final state includes repository summary, issue URL, branch name, PR URL, verification results, review summary, and review comment URL where applicable.
- [x] The notebook output makes the agent interactions and state progress visible.

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
