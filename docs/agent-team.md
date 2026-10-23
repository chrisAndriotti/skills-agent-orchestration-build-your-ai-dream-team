# Agent team

For Mona's Project Pulse dashboard, I will use a custom four-agent team orchestrated through GitHub Copilot CLI in a Codespace.

- Planner — Model: Claude Opus 5.5 (copilot). Responsibility: research the repository, identify dependencies and edge cases, and produce an ordered implementation plan without writing code. Definition: `.github/agents/planner.agent.md`.
- Designer — Model: Gemini 3.7 Flash (copilot). Responsibility: shape the dashboard's UX and visual design, including accessibility, information hierarchy, layout, and responsive styling for the Project Pulse interface. Definition: `.github/agents/designer.agent.md`.
- Coder — Model: GPT-5.5 (copilot). Responsibility: implement the actual code and logic, follow the assigned file scope, and create any runnable app support needed for the dashboard. Definition: `.github/agents/coder.agent.md`.
- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsibility: coordinate the Planner, Designer, and Coder, break work into phases, delegate tasks, manage dependencies, and verify the final integration. Definition: `.github/agents/orchestrator.agent.md`.

These definitions live under the repository's `.github/agents/` folder, and the work will be coordinated via GitHub Copilot CLI running in a Codespace.
