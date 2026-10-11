# Agent team

Mona's Project Pulse dashboard will be built by a custom agent team orchestrated through the GitHub Copilot CLI in a Codespace. The Orchestrator coordinates the specialists, keeps their file scopes and dependencies clear, and verifies that their integrated work fits together.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Converts the request into phases, coordinates the specialist agents, manages sequencing and non-overlapping file scopes, and verifies the integrated outcome. | [`.github/agents/orchestrator.agent.md`](../.github/agents/orchestrator.agent.md) |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and relevant documentation, identifies requirements and risks, then produces an implementation plan with file assignments, dependencies, edge cases, and validation expectations. | [`.github/agents/planner.agent.md`](../.github/agents/planner.agent.md) |
| Coder | GPT-5.5 (copilot) | Implements the assigned application logic and support configuration with explicit errors, predictable structure, and testable behavior. | [`.github/agents/coder.agent.md`](../.github/agents/coder.agent.md) |
| Designer | Gemini 3.1 Pro (copilot) | Shapes the dashboard's UI/UX, accessibility, information hierarchy, responsive behavior, and polished Project Pulse visual design. | [`.github/agents/designer.agent.md`](../.github/agents/designer.agent.md) |

The intended workflow is Planner first, followed by the Orchestrator assigning Coder and Designer work in parallel only when their file scopes and dependencies permit it. The learner retains control of all git operations through Copilot CLI prompts.
