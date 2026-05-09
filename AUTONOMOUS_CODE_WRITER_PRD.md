# PRD: Autonomous Code Writer Multi-Agent GitHub Workflow

## Problem Statement

The user needs to complete an individual project assignment for the "AI Agents and Workflows for Developers" course. The assignment requires a Python Jupyter notebook in Google Colab that demonstrates a multi-agent workflow using LangGraph and LangChain.

The user wants the project to be more than a simple demo. The desired system is an autonomous coding assistant that can work with a GitHub repository owned by the user, analyze the project, suggest useful feature ideas, create a GitHub issue after approval, implement the change on a safe branch, create a pull request, review that pull request, and leave the final merge decision to the human user.

The key challenge is balancing realism with assignment reliability. A fully autonomous coding system that can safely modify any repository is broad and risky, while a simple mock workflow may not demonstrate the user's intended project idea. The project therefore needs a controlled but real GitHub-based workflow that satisfies the assignment requirements and remains understandable to graders.

## Solution

Build an Autonomous Code Writer notebook that implements a LangGraph and LangChain-powered multi-agent workflow for GitHub repository development.

The user provides a GitHub repository URL and a high-level request, such as asking the system to analyze the project and suggest a new feature. The system clones and analyzes the repository, proposes a useful feature, pauses for human approval, creates a GitHub issue, creates a new branch, implements the feature, runs best-effort verification, pauses again for human approval, creates a pull request, reviews the generated pull request diff, posts a GitHub review comment, and returns the final issue and pull request URLs.

The system will use real GitHub actions only. It will not use mock GitHub behavior in the main workflow. To keep the workflow safe, the system will never push directly to the default branch and will never merge pull requests. All implementation work happens on a new branch, and critical actions require human approval.

The notebook will be designed as a balanced prototype. It will be realistic enough to demonstrate a true GitHub automation workflow, but structured clearly enough for an assignment grader to understand the architecture, tools, state transitions, memory, human-in-the-loop interruptions, and test cases.

## User Stories

1. As a student, I want to submit a Jupyter notebook that clearly satisfies the LangGraph and LangChain assignment requirements, so that I can complete the course project successfully.

2. As a student, I want the project scenario to be based on a realistic autonomous coding assistant, so that the submission demonstrates practical value beyond a toy example.

3. As a repository owner, I want to provide a GitHub repository URL, so that the system can analyze a real project I own.

4. As a repository owner, I want the system to clone and inspect the repository, so that feature suggestions are based on the actual project structure.

5. As a repository owner, I want the system to identify the repository language, framework, package manager, and likely test commands, so that future actions are appropriate for the project.

6. As a repository owner, I want the system to summarize the repository architecture, so that I can understand what the agent discovered before it proposes changes.

7. As a repository owner, I want the system to suggest useful feature ideas, so that I can choose improvements without manually inspecting the whole project.

8. As a repository owner, I want feature suggestions to be conservative and realistic, so that the system does not propose risky or oversized changes.

9. As a repository owner, I want the system to prefer small and medium features, so that implementation is feasible inside a notebook workflow.

10. As a repository owner, I want the system to explain the value of each proposed feature, so that I can decide whether it is worth implementing.

11. As a repository owner, I want the system to estimate implementation scope and risk, so that I know what kind of repository changes may happen.

12. As a repository owner, I want the workflow to pause before creating a GitHub issue, so that I remain in control of repository write actions.

13. As a repository owner, I want to approve a proposed feature idea, so that the system can proceed with issue creation only after my consent.

14. As a repository owner, I want to reject a proposed feature idea, so that the workflow can stop without creating unwanted GitHub artifacts.

15. As a repository owner, I want to request a revision to the feature idea, so that the agent can adapt the proposal to my feedback.

16. As a repository owner, I want the system to create a GitHub issue for the approved feature, so that the implementation is tied to a traceable work item.

17. As a repository owner, I want the issue body to include the feature goal, implementation notes, risks, and acceptance criteria, so that the work is documented clearly.

18. As a repository owner, I want the system to create a new branch for the implementation, so that the default branch remains safe.

19. As a repository owner, I want the branch name to reference the issue, so that the branch can be traced back to the approved work item.

20. As a repository owner, I want the system to avoid pushing to the default branch, so that the repository cannot be damaged by direct automated changes.

21. As a repository owner, I want the implementation agent to follow existing project conventions, so that the generated code feels consistent with the repository.

22. As a repository owner, I want the implementation agent to make focused changes, so that the pull request is reviewable.

23. As a repository owner, I want the implementation agent to avoid unrelated refactors, so that the final pull request stays aligned with the approved issue.

24. As a repository owner, I want the implementation agent to add or update tests when feasible, so that the change has some verification coverage.

25. As a repository owner, I want the implementation agent to prefer documentation, examples, tests, or small code changes when the repository structure is unclear, so that it avoids risky edits.

26. As a repository owner, I want the system to run best-effort verification, so that obvious test or lint failures can be detected before opening a pull request.

27. As a repository owner, I want verification output to be captured, so that I can inspect what commands were run and whether they passed.

28. As a repository owner, I want missing tests or unavailable commands to be reported clearly, so that the absence of verification is not hidden.

29. As a repository owner, I want the workflow to pause before creating a pull request, so that I can review the implementation summary and test results first.

30. As a repository owner, I want to approve PR creation, so that the system only publishes implementation work when I am satisfied.

31. As a repository owner, I want to request implementation revisions, so that the agent can update the branch before opening the pull request.

32. As a repository owner, I want to reject PR creation, so that the system can stop without publishing unwanted changes.

33. As a repository owner, I want the system to create a pull request after approval, so that I can review and merge the work using normal GitHub processes.

34. As a repository owner, I want the pull request body to include a summary, linked issue, verification results, and agent notes, so that the PR is understandable.

35. As a repository owner, I want the system to review the generated pull request diff, so that obvious issues can be surfaced before human merge review.

36. As a repository owner, I want the review agent to prioritize bugs, regressions, security risks, missing tests, and maintainability concerns, so that the review is useful.

37. As a repository owner, I want the review agent to say when no blocking issues are found, so that the review result is explicit.

38. As a repository owner, I want the review agent to post a GitHub PR comment, so that the review is preserved with the pull request.

39. As a repository owner, I want the system to return the issue URL, branch name, PR URL, and review comment URL, so that I can continue reviewing in GitHub.

40. As a repository owner, I want the system to never merge the pull request automatically, so that final production changes remain under human control.

41. As a repository owner, I want API keys to be loaded from environment variables or Colab Secrets only, so that credentials are not submitted in the notebook.

42. As a repository owner, I want the system to avoid printing secrets, so that logs and notebook output remain safe.

43. As a student, I want the workflow to include at least two agents, so that the assignment's multi-agent architecture requirement is satisfied.

44. As a student, I want the workflow to include multiple distinct tools, so that the assignment's tool usage requirement is satisfied.

45. As a student, I want the workflow state to be explicit and visible, so that graders can see how LangGraph passes information between agents.

46. As a student, I want the workflow to use conversational memory, so that the assignment's memory requirement is satisfied.

47. As a student, I want the workflow to use Human-in-the-Loop interruptions, so that the assignment's HITL requirement is satisfied.

48. As a student, I want the notebook to expose an execute_workflow function that accepts a string request, so that it matches the assignment specification.

49. As a student, I want at least five test cases, so that the assignment's testing requirement is satisfied.

50. As a student, I want at least one test case to demonstrate approval, so that the happy path is visible.

51. As a student, I want at least one test case to demonstrate revision feedback, so that resume behavior after interruption is visible.

52. As a student, I want the notebook to be readable and grader-friendly, so that the implementation can be evaluated without excessive complexity.

53. As a student, I want the architecture to remain close to a realistic product, so that the project can be extended after the course.

54. As a future developer, I want the workflow modules to be separated by responsibility, so that individual agents and tools can be improved independently.

55. As a future developer, I want tool wrappers to have clear interfaces, so that GitHub, repository, and verification behavior can be tested or replaced later.

56. As a future developer, I want the implementation agent to be constrained by safety rules, so that future extensions do not accidentally increase risk.

57. As a future developer, I want review output to be structured, so that it could later be converted into inline comments or status checks.

58. As a future developer, I want the system to support small general repositories first, so that the prototype is not locked to only one language.

59. As a future developer, I want the system to detect common project metadata, so that language-specific behavior can be expanded later.

60. As a future developer, I want final workflow output to be returned as structured state, so that it can later be integrated into a UI or service.

## Implementation Decisions

- The product will be implemented as a Python Jupyter notebook intended to run in Google Colab.

- The notebook will use LangGraph for the stateful workflow graph and LangChain for model and tool integration.

- The OpenAI model provider will be used through LangChain's OpenAI chat model integration.

- The default model will be `gpt-5.4-mini-2026-03-17`, with the model name stored in one variable so it can be changed easily.

- The system will require `OPENAI_API_KEY` and `GITHUB_TOKEN` to be loaded from environment variables or Colab Secrets.

- The system will not hardcode API keys or tokens.

- The system will use real GitHub operations only for the main workflow.

- The system will be designed for repositories owned by the user or repositories where the user's token has appropriate permissions.

- The system will target small general repositories first, rather than attempting production-grade arbitrary repository automation.

- The system will support medium feature changes, while still preferring conservative and reviewable edits.

- The workflow will never push directly to the repository default branch.

- The workflow will never merge pull requests.

- The workflow will always create a new branch for implementation work.

- The workflow will require human approval before creating a GitHub issue.

- The workflow will require human approval before creating a pull request.

- The workflow will allow human feedback to revise the feature idea before issue creation.

- The workflow will allow human feedback to revise the implementation before pull request creation.

- The workflow will use a state object that stores the user request, repository information, repository summary, feature proposal, issue details, branch name, implementation plan, changed files, diff, verification results, pull request details, review summary, and conversation messages.

- The state object will use a typed schema so the flow of data between nodes is clear.

- The graph will use a memory checkpointer so the workflow can preserve conversation and resume after interruptions.

- The graph will contain distinct nodes for request parsing, repository analysis, feature suggestion, human feature review, feature revision, issue creation, branch creation, implementation, verification, PR preparation, human PR review, implementation revision, PR creation, PR review, review comment posting, and final response generation.

- The Repository Analyst Agent will inspect the target repository and produce a technical summary.

- The Feature Strategist Agent will generate feature ideas and prepare the selected feature proposal.

- The GitHub Coordinator Agent will perform GitHub write actions after the graph state confirms approval.

- The Implementation Agent will make focused code changes on the new branch.

- The PR Review Agent will review the generated pull request diff and produce a structured review.

- The GitHub Coordinator Agent and PR Review Agent will work together to post the final review comment on the pull request.

- Repository analysis will include file listing, metadata detection, framework detection, test command detection, and important file summaries.

- Feature proposals will include a title, body, expected implementation scope, possible changed areas, risk level, and acceptance criteria.

- GitHub issue creation will happen only after feature approval.

- Branch names will include the issue number and a short slug based on the feature title.

- Implementation will be tied to the approved GitHub issue.

- Implementation will avoid unrelated refactors and broad dependency changes.

- Implementation will avoid secrets, credential files, authentication rewrites, database migrations, deployment changes, and CI/CD changes unless explicitly requested.

- Verification will be best-effort and based on detected test or lint commands.

- Verification failure will not automatically prevent PR creation, but it will be shown to the user before PR approval and included in the PR body.

- Pull request creation will happen only after implementation approval.

- The PR body will include a summary, linked issue, verification details, and notes that the workflow used human approval.

- The PR review will inspect the diff and produce findings, risks, verification notes, and a recommendation.

- The review will be posted as a GitHub pull request comment.

- The final output will include the issue URL, branch name, pull request URL, review summary, and review comment URL when available.

- The core workflow interface will be an `execute_workflow` function that accepts one string user request.

- The `execute_workflow` function will initialize the graph, start execution, handle graph interruptions, collect human input, resume execution, and return the final state.

- The notebook will include five separate test cases that call the core workflow function with different user requests.

- The five test cases will use the same owned demo repository with separate branches and issues to keep the demonstration reliable.

- At least one test case will show a direct approval path.

- At least one test case will show feature revision after human feedback.

- At least one test case will show implementation revision before PR creation.

- The project will be documented in markdown cells before the code to make the scenario and architecture clear to graders.

- The notebook will include a requirements checklist mapping assignment requirements to implemented components.

- The notebook will include clear security notes explaining that API keys are loaded from environment variables or Colab Secrets.

- The notebook will include limitations and future work notes explaining that the prototype is safe branch-based automation, not a fully production-ready autonomous coding platform.

## Testing Decisions

- Tests should validate the externally visible workflow behavior rather than internal implementation details.

- Good tests for this project demonstrate that the workflow can analyze a repository, generate a feature proposal, pause for human approval, create a GitHub issue, implement a branch change, run verification, pause for PR approval, create a pull request, review the pull request, and post a review comment.

- Good tests should show both approval and revision paths through the Human-in-the-Loop interruption points.

- The required five test cases will be represented as notebook cells or clearly separated calls to the core workflow function.

- The test cases will use one owned demo repository to avoid spreading issues and pull requests across multiple projects.

- Each test case will use a different user request so the feature suggestion and implementation path are meaningfully different.

- The first test case will demonstrate the happy path where the user approves the feature and approves PR creation.

- The second test case will demonstrate feature revision where the user asks the agent to revise the proposal before approving issue creation.

- The third test case will demonstrate implementation revision where the user asks the agent to update the implementation before approving PR creation.

- The fourth test case will demonstrate a documentation-backed or developer-experience improvement.

- The fifth test case will demonstrate a maintainability or validation improvement.

- The repository analysis module should be tested by observing that it returns detected stack information, important files, and likely test commands.

- The GitHub tool module should be tested through real issue creation, branch push, PR creation, and PR comment posting in the owned demo repository.

- The Human-in-the-Loop routing module should be tested through approve, revise, and reject-style inputs.

- The verification module should be tested by using a demo repository with at least one simple test or lint command when possible.

- The PR review module should be tested by confirming that it produces structured review output and posts it to the pull request.

- The workflow state should be inspected after execution to confirm that key outputs such as issue URL, PR URL, changed files, test results, and review summary were captured.

- The notebook should not include tests that require secrets to be printed or committed.

- The notebook should not include destructive tests that push to the default branch or merge pull requests.

- The notebook should document expected human responses for each test case so graders can reproduce the Human-in-the-Loop behavior.

## Out of Scope

- Automatically merging pull requests is out of scope.

- Pushing directly to the default branch is out of scope.

- Production-grade sandbox isolation is out of scope.

- Long-running background agents are out of scope.

- GitHub webhook handling is out of scope.

- Multi-repository orchestration is out of scope.

- Automatic deployment or release creation is out of scope.

- Large-scale refactoring of arbitrary repositories is out of scope.

- Database migrations are out of scope unless explicitly requested in a future version.

- Dependency upgrade automation is out of scope unless explicitly requested in a future version.

- Security-sensitive changes such as authentication rewrites are out of scope for the assignment version.

- Full inline GitHub code review comments are out of scope for the initial version; the review will be posted as a structured PR comment.

- Mock GitHub mode is out of scope because the selected project direction is real GitHub only.

- A separate web application UI is out of scope; the notebook interface is sufficient for this assignment.

## Further Notes

- The recommended demo setup is a small repository owned by the user with simple code and tests.

- The GitHub token should have permission to read and write contents, issues, and pull requests for the target repository.

- If using a fine-grained GitHub token, the recommended permissions are contents read/write, issues read/write, pull requests read/write, and metadata read-only.

- If using a classic GitHub token, the repository scope must be sufficient for issue, branch, commit, PR, and comment operations.

- The workflow should include visible notebook output at each major step so that the grader can see the multi-agent interaction.

- The final notebook should include an architecture diagram in markdown showing the flow from user request to repository analysis, feature strategy, human approval, issue creation, implementation, verification, PR approval, PR creation, PR review, review comment, and final result.

- The final notebook should explicitly state that the human remains responsible for final merge decisions.

- The final notebook should explicitly state that the system is a balanced prototype and not a production autonomous coding platform.

- The final notebook should include a clear mapping from assignment requirements to implementation components.

- The final workflow is intentionally constrained to safe branch-based automation so it can demonstrate real GitHub behavior without giving the agent destructive control over repositories.
