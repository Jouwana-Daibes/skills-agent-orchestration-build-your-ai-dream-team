# Project Pulse Implementation Plan

## Repository findings and implementation approach

- This is a small, static Project Pulse frontend. The `app/` directory currently has no files.
- There is no framework or build system to extend, and no runtime packages are needed. Use browser-native HTML, CSS, and JavaScript, with the project data in JSON.
- Keep the implementation focused on the four assigned files below. Do not add a package manager, build pipeline, or other tooling unless a concrete requirement emerges.

## Scope and file ownership

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Provide the page structure, exact document title `Project Pulse`, CSS and JSON references, and card rendering. Handle loading, fetch/parse failure, and empty-data states safely. |
| `app/styles.css` | Designer | Create the polished, responsive dashboard, including accessible status and priority treatments and the `.dashboard` and `.project-card` hooks. Define appropriate border-radius and box-shadow styling. |
| `app/project-data.json` | Coder | Provide a top-level `projects` array. Each project includes `name`, `owner`, `status`, `recentActivity`, and `priority`, with illustrative sample records unless authoritative project data is supplied. |
| `.vscode/launch.json` | Coder | Add a strict-JSON VS Code launch configuration named `Run Project Pulse Dashboard`, with `cwd` `${workspaceFolder}/app`, command `python3 -m http.server 5500`, and a `serverReadyAction` that opens `http://localhost:%s/index.html`. |

The launch configuration should use a terminal launch type compatible with the `command` field (for example, `node-terminal`) and launch request. Its server-ready pattern should capture the port from Python's HTTP-server startup message so the configured URI format opens the page rather than a directory listing.

### Roles

- **Orchestrator:** Coordinate the work, keep the file boundaries clear, define and settle the UI/data contract, and verify the integrated result.
- **Designer:** Own only `app/styles.css`; implement the visual system, responsive behavior, and accessible visual treatments.
- **Coder:** Own `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`; implement rendering, sample data, and the runnable launch setup.

Avoid overlapping file ownership. The Designer and Coder should agree on the dashboard/card hooks and the data contract before either relies on those details.

## Contract questions and assumptions

1. **Summary content:** The brief has a summary-display goal, but `summary` is not in its mandatory project-field list. Resolve whether a summary is required. Recommended contract: add `summary` as an optional field, or make it required if the dashboard must display a summary for every project. Do not silently treat it as mandatory without agreement.
2. **Authoritative data:** Confirm whether real project and owner data is available. Until then, use clearly illustrative sample data and do not present it as authoritative.
3. **Labels:** Confirm the accepted status and priority taxonomies, including their display labels. Until confirmed, handle labels as data rather than assuming a closed set of CSS classes.
4. **Runtime environment:** Confirm that Python 3, VS Code, and the relevant terminal launch support are available in the target environment. The launch configuration assumes they are.

## Ordered phases and dependencies

### Phase 1 — Settle the shared contract

The Orchestrator confirms the required fields, resolves (or explicitly defers) the summary question, establishes sample-data expectations, and aligns the HTML/CSS hooks with the Designer and Coder. Agree that the JSON document has a top-level `projects` array and that every record has the five required fields.

**Dependency:** This phase must finish before parallel implementation begins. The HTML renderer depends on the agreed schema and style hooks.

### Phase 2 — Parallel implementation

Once the contract is settled, split work by file ownership:

- The **Designer** builds responsive `app/styles.css`, including `.dashboard`, `.project-card`, readable spacing and typography, border-radius and box-shadow, and accessible status/priority treatments.
- The **Coder** builds `app/project-data.json` and `.vscode/launch.json`. Use illustrative sample records unless authoritative data was provided. Keep the launch file valid strict JSON and configure the specified command, working directory, name, and browser target.

These CSS, data, and launch tasks can proceed in parallel after Phase 1. The Coder can begin the HTML skeleton in parallel only against the settled schema and agreed CSS hooks; final rendering integration depends on the data and styling contract.

### Phase 3 — Complete HTML rendering and integrate

The Coder completes `app/index.html`: set the exact title `Project Pulse`, load the stylesheet and JSON, render project cards containing all required fields, and provide clear loading, error, and empty states. Use safe text rendering, not raw HTML interpolation of project values. The Orchestrator checks that the integrated markup and classes match the Designer's CSS and the agreed data schema.

**Dependency:** HTML rendering depends on Phase 1's contract; integration depends on the assigned files being available. Do not begin runtime launch validation until all four files are complete.

### Phase 4 — Validate the integrated dashboard

Run the checks below, fix integration issues within the assigned files, and have the Orchestrator verify the final page and launch workflow. If launch prerequisites are unavailable, record that limitation rather than treating static checks as proof of a successful launch.

## Edge cases and implementation guidance

- Handle JSON fetch failures and invalid JSON with a visible, understandable error state; do not leave the page blank or expose an unhandled exception as the only feedback.
- Treat a missing, empty, or non-array `projects` value as empty or invalid data and show a suitable empty/error state. Handle incomplete project records without crashing or producing misleading blank labels.
- Support unexpected status and priority labels with readable fallback styling and text. Do not rely on color alone to communicate either value.
- Render project-provided values as text (for example, with text nodes or `textContent`), never as raw HTML. This protects against markup in data being interpreted as page content.
- Keep sample-data accuracy explicit: sample records are illustrative, not verified project facts.
- The launch workflow may be blocked by a missing Python 3 interpreter, unavailable VS Code launch support, a port conflict on `5500`, or environment restrictions on opening a browser. Verify these prerequisites when possible and report limitations.

## Validation expectations

Validate the completed work at both the file/contract level and in a browser:

1. **File presence:** Confirm `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` all exist.
2. **Data contract:** Parse `app/project-data.json`; verify it is an object with a top-level `projects` array and that every supplied project has `name`, `owner`, `status`, `recentActivity`, and `priority`. Check the chosen summary-field behavior against the settled contract.
3. **Launch configuration:** Parse `.vscode/launch.json` as strict JSON (no comments or trailing commas). Confirm the configuration name is `Run Project Pulse Dashboard`, its `cwd` is `${workspaceFolder}/app`, its command is `python3 -m http.server 5500`, and its server-ready browser target is `http://localhost:%s/index.html`.
4. **HTML integration:** Confirm the document title is exactly `Project Pulse`, and that the page references the CSS and JSON. Verify project cards render the required fields and that loading, fetch/parse error, and empty states are implemented.
5. **CSS and responsive behavior:** Confirm `.dashboard` and `.project-card` are present, status/priority treatments have legible fallbacks, and the layout adapts to narrow and wide viewports. Check readable contrast, spacing, and typography.
6. **Safe and accessible presentation:** Verify data values are rendered as text, not interpreted as HTML. Check keyboard navigation, readable labels, and non-color cues for status and priority; ensure the visual design remains understandable without relying only on color.
7. **Manual launch:** Where the environment permits, run the VS Code launch configuration, confirm the server becomes ready and opens `index.html` at the expected URL, and verify the dashboard—not a directory listing—appears. Check the browser console and exercise representative data, empty-data, and failure behavior where practical.

The exercise workflow's required-file, phrase, and JSON checks are useful contract checks, but they do not replace usability, accessibility, responsive-layout, or actual launch validation.
