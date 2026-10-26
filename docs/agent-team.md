# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate this custom agent team for Mona's Project Pulse dashboard:

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| Orchestrator | Claude Opus 5.5 (copilot) | Breaks down the dashboard work, delegates to specialists with clear file scopes, coordinates phases, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository, dependencies, documentation, and edge cases, then provides an implementation plan with sequencing and validation expectations. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Shapes the dashboard's user experience, accessibility, information hierarchy, responsive behavior, and visual styling. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements assigned application code and runnable-app support, following repository patterns and validating the changes. | `.github/agents/coder.agent.md` |
