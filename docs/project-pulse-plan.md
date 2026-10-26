# Project Pulse Dashboard Implementation Plan

## Summary

Build a lightweight static dashboard that helps contributors see project owners, status, recent activity, priority or risk, and a short contributor-friendly summary. Follow the brief’s required project data fields and use the repo’s expected file paths. There is no existing frontend framework or app dependency to preserve; keep the implementation static and avoid adding a build system.

The Orchestrator should coordinate the Planner, Designer, and Coder, keep their file ownership separate, and verify the integrated dashboard and preview configuration. The Designer owns visual and accessibility decisions and the stylesheet. The Coder owns the HTML, JSON data, and VS Code launch configuration.

## Ordered implementation steps

### 1. Confirm scope and interfaces

- **Owner:** Orchestrator, with Planner guidance.
- **Files:** No implementation files.
- Read `.github/project-pulse-brief.md` and this plan.
- Confirm the project schema, page content, CSS hooks, and launch requirements before parallel implementation.
- The required project fields are `name`, `owner`, `status`, `recentActivity`, and `priority`. The brief also asks for a short summary but does not list `summary` as a required field; resolve this by including an optional `summary` property per project and rendering it, without weakening support for the required fields.
- Agree that the HTML will use `.dashboard` and `.project-card` hooks, and that the page will load `app/project-data.json`.

### 2. Produce design direction and project data

These tasks can run **in parallel** after Step 1 because the required data schema is fixed and the Designer and Coder own different files.

- **Designer — `app/styles.css`:** Define a polished, readable, responsive card-based dashboard style. Plan clear information hierarchy for project name, owner, status, activity, priority/risk, and summary. Include visible status badges, adequate contrast, readable spacing, and the required `.dashboard` and `.project-card` selectors. Use `border-radius` and `box-shadow` for card styling. Provide the expected HTML hooks to the Coder; do not edit `app/index.html`.
- **Coder — `app/project-data.json`:** Create a valid JSON object with a top-level `projects` array. Include realistic, contributor-friendly entries with all five required properties and, if agreed in Step 1, `summary`. Keep values non-empty and consistent with the status and priority treatments the Designer plans. Do not edit the stylesheet.

### 3. Implement the page and launch configuration

- **Owner:** Coder.
- **Files:** `app/index.html` and `.vscode/launch.json`.
- **Dependencies:** Step 1’s agreed interfaces; Step 2’s Designer HTML hooks and project data schema. This step must follow the design handoff so markup and CSS agree.
- In `app/index.html`, use the exact page title **Project Pulse**, reference `styles.css`, and load `project-data.json`. Render visible project cards from the project data, with the `project-card` class on each card. Show every required field, and display `summary` if included.
- Make the page accessible: use semantic structure and headings, meaningful labels, and status/priority distinctions that do not depend on color alone. Handle data-loading failure with a clear message rather than a blank dashboard.
- In `.vscode/launch.json`, add the **Run Project Pulse Dashboard** configuration. Follow the repository’s Step 3 requirement: serve with `python3 -m http.server 5500`, use `${workspaceFolder}/app` as the working directory, and open `http://localhost:%s/index.html` when the server is ready. Use strict JSON with no comments. Ensure the URL targets `index.html`, not the directory root.
- Confirm the selected VS Code launch type supports the chosen process-start and `serverReadyAction` settings; VS Code documents that launch properties depend on the selected debugger type and that `serverReadyAction` matches server output to open a URI.

### 4. Integrate and validate

- **Owner:** Orchestrator, with Designer and Coder reviewing only their assigned files.
- **Files:** All four implementation files.
- **Dependencies:** Steps 2 and 3 complete.
- Check that the HTML selectors and markup match the Designer’s stylesheet, that every required data field is rendered, and that the launch target serves the dashboard.
- Resolve defects in the owning agent’s files; avoid overlapping edits by assigning any requested change explicitly.

## File assignments and responsibilities

| File | Owner | Assignment |
|---|---|---|
| `app/index.html` | Coder | Accessible Project Pulse page; links stylesheet and data; renders project cards and their required fields. |
| `app/styles.css` | Designer | Responsive visual design; clear information hierarchy, status badges, readable spacing, `.dashboard` and `.project-card` selectors, rounded corners and shadows. |
| `app/project-data.json` | Coder | Valid JSON with top-level `projects` array and required project fields; optional `summary` to satisfy the brief’s contributor-friendly summary request. |
| `.vscode/launch.json` | Coder | Strict JSON; **Run Project Pulse Dashboard**; serve `app/` using the specified Python command; open `index.html`. |

**Designer responsibility:** Decide and implement the visual design and responsive/accessibility styling in `app/styles.css`; communicate the required markup hooks to the Coder. Do not edit HTML, data, or launch configuration.

**Coder responsibility:** Implement `app/index.html`, `app/project-data.json`, and `.vscode/launch.json` to the agreed design and schema; integrate the stylesheet and data; report validation results and any launch risks. Do not edit the Designer-owned stylesheet unless the Orchestrator explicitly reassigns it.

**Orchestrator responsibility:** Confirm interfaces, delegate by file ownership, enforce dependencies and non-overlapping changes, then review the integrated result.

## Dependencies and parallel work

- **Sequential:** Agree on schema and CSS/HTML hooks (Step 1) before parallel tasks begin.
- **Parallel:** Designer can work on `app/styles.css` while Coder prepares `app/project-data.json`; their file scopes do not overlap.
- **Sequential:** Coder completes `app/index.html` after receiving the Designer’s markup-hook direction and using the agreed data schema.
- **Parallel opportunity:** Coder may prepare `.vscode/launch.json` alongside HTML once the fixed launch requirements are confirmed; it has no source-file overlap, but its end-to-end check must wait for the app files.
- **Sequential:** Integrated review and manual launch test must follow completion of all four files.

## Edge cases and risks

- Missing, empty, or malformed project data; empty `projects` array; missing required fields; and failed JSON fetch should not produce a silent blank page.
- Unexpected status or priority values should remain readable and distinguishable; use text labels in addition to color.
- Project names, activity, or summaries may be long; layout should wrap without clipping or breaking cards.
- A local server is required for loading JSON reliably; opening the HTML directly from disk may fail to fetch data.
- Port `5500` may already be occupied. Report this clearly and stop any preview server after testing.
- `serverReadyAction` depends on matching server output and a valid capture group. Verify the selected launch type’s supported configuration and the actual output pattern.
- Avoid adding dependencies or extra files unless the Orchestrator approves them; the exercise expects the specified static app files.

## Validation expectations

1. **File presence:** Confirm `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` exist.
2. **Automated exercise checks:** Run `bash scripts/validate-exercise.sh` for repository-level checks. The workflow for this exercise also checks the plan’s expected keywords and assignments; the Step 3 workflow checks required app files, CSS hooks, data fields, launch name, and JSON validity.
3. **JSON syntax:** Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`.
4. **Content and integration:** Verify the page title is exactly `Project Pulse`; the page references both `styles.css` and `project-data.json`; cards use `project-card`; required fields are visibly rendered; and CSS includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
5. **Manual preview:** In VS Code Run and Debug, select **Run Project Pulse Dashboard**. Confirm the browser opens `http://localhost:5500/index.html`, shows the dashboard rather than a directory listing, and displays the project cards. Check a narrow viewport and keyboard/accessibility basics. Stop the server afterward.

## Open questions

- Should `summary` be included in every project object? This plan recommends yes, because the brief requests a short summary but omits it from the required schema.
- Which status and priority vocabulary should the team use? Agree on a small, consistent set before styling badges.
- Does the Codespace’s VS Code setup support the chosen launch type and browser-opening action without installing another extension? Verify before considering the launch configuration complete.
