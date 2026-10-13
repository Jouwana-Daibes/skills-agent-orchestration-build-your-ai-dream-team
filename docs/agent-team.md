# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard, orchestrated with GitHub Copilot CLI in a Codespace:

- **Orchestrator** — **Claude Opus 4.7 (copilot)**. Coordinates the specialists, splits work into phases with explicit file ownership, and integrates and verifies the result. Definition: `.github/agents/orchestrator.agent.md`.
- **Planner** — **Claude Opus 4.7 (copilot)**. Researches the repository and relevant documentation, then produces an implementation plan with steps, dependencies, risks, and validation expectations. Definition: `.github/agents/planner.agent.md`.
- **Designer** — **Gemini 3.1 Pro (copilot)**. Shapes the dashboard's user experience, accessibility, information hierarchy, responsive layout, and visual styling, including clear project cards and status and priority treatments. Definition: `.github/agents/designer.agent.md`.
- **Coder** — **GPT-5.5 (copilot)**. Implements the assigned application logic and support files with explicit errors and testable behavior; validates changes and can configure the Project Pulse app launch when assigned. Definition: `.github/agents/coder.agent.md`.
