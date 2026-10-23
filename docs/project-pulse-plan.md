# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona’s Project Pulse as a small, dependency-free static dashboard. Contributors should be able to scan project names, owners, status, recent activity, priority/risk, and a short summary. The repository has no existing app source, package manifest, frontend framework, or app test suite; `app/` is empty. Existing VS Code tasks support the exercise terminal, not dashboard preview.

Use the repository brief and existing custom-agent roles as the source of truth. Keep the required HTML, CSS, JSON, and launch configuration; do not introduce a framework or package manager.

## Ordered implementation steps

### 1. Agree on the design and markup contract

- **Designer — `app/styles.css`:** Set the visual hierarchy and implement polished, responsive styling for a dashboard, project cards, status badges, and priority/risk indicators. Include the required `.dashboard` and `.project-card` selectors, clear focus treatment, readable contrast and spacing, and `border-radius` and `box-shadow`.
- **Designer + Coder — contract only:** Agree on the HTML hooks before integration (at minimum `.dashboard` and `.project-card`, plus any status/priority classes). Keep text labels visible so meaning does not rely on color alone.
- **Dependency:** This contract must precede HTML integration to avoid mismatched selectors.

### 2. Prepare data and preview configuration in parallel

These tasks can proceed alongside the Designer’s CSS work because their file scopes are separate and the brief defines the data fields and preview behavior.

- **Coder — `app/project-data.json`:** Create a top-level `projects` array. Each entry must include `name`, `owner`, `status`, `recentActivity`, and `priority`. Add a short `summary` field if useful to fulfill the contributor-friendly summary requirement. Use a small set of clearly illustrative sample projects; the brief does not supply real team data.
- **Coder — `.vscode/launch.json`:** Add strict JSON with a **Run Project Pulse Dashboard** configuration that runs `python3 -m http.server 5500`, uses `${workspaceFolder}/app` as `cwd`, and opens `http://localhost:%s/index.html` through `serverReadyAction`. Use a readiness pattern compatible with Python’s server output.
- **Coder — `.devcontainer/devcontainer.json` (conditional support):** Verify the Python Debugger (`debugpy`) extension is available for the launch configuration. The current explicit extension list does not include Python tooling. If it is not otherwise guaranteed in the Codespace, add `ms-python.python`, which installs the Python Debugger extension. Do not change `.vscode/tasks.json`; it is not needed for this launch flow.

### 3. Integrate the dashboard page

- **Coder — `app/index.html`:** Create an accessible page titled **Project Pulse**, reference `styles.css`, load `project-data.json`, and render one `.project-card` per project. Display the required fields and summary where present. Use the agreed Designer hooks and semantic headings/landmarks. Keep rendering code inline unless implementation reveals a concrete need for another file.
- **Dependencies:** Integrate after the data shape and Designer’s markup/style contract are agreed. The page must load over HTTP; opening it directly as a `file://` URL is not a reliable way to fetch JSON.

### 4. Review and validate the integrated result

- **Orchestrator:** Check file ownership, confirm the launch configuration opens the dashboard rather than a directory listing, review the visual result with Designer, and report any remaining assumptions or limitations.
- **Coder:** Fix implementation or launch issues within the assigned files; do not overlap with the Designer’s stylesheet without coordinating a handoff.

## Responsibilities and sequencing

- **Planner:** Research repository conventions and this brief; define assignments, dependencies, risks, and validation. Plan only—no implementation.
- **Designer:** Own `app/styles.css` and provide the visual/accessibility direction and markup-hook contract.
- **Coder:** Own `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`; conditionally update `.devcontainer/devcontainer.json` only to ensure the required debugger support.
- **Orchestrator:** Delegate by file scope, resolve the design/markup contract, integrate the work, and verify the result.

**Parallel:** After the hook and data contracts are agreed, Designer can implement CSS while Coder prepares the JSON and launch configuration. Their file scopes do not overlap.

**Sequential:** HTML integration follows the CSS/markup contract and JSON shape. Final browser and launch validation follows integration, since it tests the connected result rather than isolated files.

## Edge cases and risks

- Handle a failed JSON request, non-success response, or malformed JSON with a visible, accessible error message rather than a blank dashboard.
- Handle an empty `projects` array and missing optional summaries gracefully. Keep required fields present in sample data; avoid assuming status or priority values beyond those in the supplied data.
- Use text-safe DOM rendering (for example, `textContent`) for JSON values rather than inserting them as HTML.
- Port `5500` may already be occupied; stop any previous server session and report a clear startup failure if the launch cannot bind.
- If the expected VS Code debugger is unavailable, `serverReadyAction` will not run as intended; verify the extension before treating the launch setup as complete.
- The brief does not provide actual project names, owners, permitted status/priority values, or summaries. Treat sample values as illustrative pending confirmation.

## Validation expectations

1. Parse `app/project-data.json` and `.vscode/launch.json` with `python3 -m json.tool`.
2. Confirm the JSON contains a top-level `projects` array and each project has `name`, `owner`, `status`, `recentActivity`, and `priority`.
3. Confirm the page references both required assets, includes the exact title, and renders visible cards containing status, activity, and priority.
4. Confirm the stylesheet contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`; inspect responsive layout, text contrast, focus visibility, and non-color status cues.
5. Select **Run Project Pulse Dashboard** in VS Code and verify it serves from `app/`, opens `index.html`, and displays cards rather than a directory listing. Check the browser console and network panel for load or rendering errors; also verify empty-data and data-load-error behavior.
6. The Step 3 workflow checks required file names, phrases, and JSON parsing, but does not run the app. `scripts/validate-exercise.sh` checks template-level constraints, including that learner answer files are not tracked; it is not an app smoke test and may fail after learner outputs are added.

## References and open questions

- The repository brief is `.github/project-pulse-brief.md`; existing agent guidance is in `.github/agents/`.
- VS Code’s [launch configuration](https://code.visualstudio.com/docs/debugtest/debugging-configuration) and [Python debugging](https://code.visualstudio.com/docs/python/debugging) documentation support the workspace launch setup and explain the Python Debugger dependency. MDN’s [Fetch API guide](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) covers request/response handling.
- Confirm whether representative sample projects are acceptable, whether `summary` should be a data field, and whether the Codespace already guarantees Python Debugger availability.
