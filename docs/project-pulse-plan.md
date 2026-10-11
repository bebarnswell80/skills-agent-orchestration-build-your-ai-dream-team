# Project Pulse Dashboard: Implementation Plan

## Summary

Mona's Project Pulse dashboard will be a small static web application that loads
project information from JSON and renders a responsive set of project cards. The
visible page title must be **Project Pulse**. The implementation will use
`app/index.html` as the entry point, `app/project-data.json` as the data source,
and `app/styles.css` for the visual design. A VS Code launch configuration will
serve the app locally so JSON can be fetched over HTTP rather than opened from
the file system.

## Repository Findings and Constraints

- The `app/` directory exists but is currently empty.
- `.vscode/tasks.json` exists and **must not be changed**.
- The only files to create or modify for this work are:
  - `app/index.html`
  - `app/styles.css`
  - `app/project-data.json`
  - `.vscode/launch.json`
- `project-data.json` must have a top-level object shape:

  ```json
  {
    "projects": []
  }
  ```

- Every project object must include string-compatible values for:
  - `name`
  - `owner`
  - `status`
  - `recentActivity`
  - `priority`
- `summary` is strongly recommended for each project and should be displayed
  when present.
- `index.html` must:
  - display the title `Project Pulse`;
  - link `styles.css`;
  - load `project-data.json`;
  - create data-driven elements using the `project-card` class.
- The stylesheet must include `.dashboard` and `.project-card` selectors, plus
  `border-radius` and `box-shadow` styling.
- `.vscode/launch.json` must be strict JSON with no comments. It must define a
  configuration named `Run Project Pulse Dashboard`, run
  `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`, and
  open `http://localhost:%s/index.html`.
- No git staging, commits, or pushes are part of this work.

## Roles and File Ownership

| Role | Exclusive ownership / responsibility |
| --- | --- |
| Orchestrator | Establish the JSON schema and CSS-hook contract before implementation, coordinate handoffs, and oversee integration validation. |
| Designer | **Exclusively owns** `app/styles.css`. Defines the responsive visual system and accessibility-aware presentation for dashboard and card states. |
| Coder | Owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. Implements data loading, safe rendering, UI states, example data, and the local launch configuration. |

The Designer must not edit Coder-owned files. The Coder must not edit
`app/styles.css`. No role may change `.vscode/tasks.json`.

## Contract Established by the Orchestrator

Before parallel work begins, the Orchestrator will publish a short stable
contract:

1. **Data schema:** the top-level value is `{ "projects": [...] }`; each
   project has `name`, `owner`, `status`, `recentActivity`, and `priority`, with
   optional/recommended `summary`.
2. **Rendering hooks:** the HTML contains a dashboard container using
   `.dashboard`; each rendered project is an article or equivalent card using
   `.project-card`.
3. **State hooks:** agree on class names or semantic attributes for loading,
   empty, error, status, and priority presentation before styling begins.
4. **Accessibility hooks:** status and priority must be communicated in text,
   not color alone; the dashboard state should be announced or otherwise
   understandable to assistive technology.

Once published, the schema and class contract are treated as stable unless the
Orchestrator coordinates a change with both owners.

## Ordered Implementation Steps

1. **Orchestrator: define and publish the contract.** Confirm the data shape,
   required fields, optional `summary`, CSS class hooks, and loading/empty/error
   state hooks.
2. **Coder: create `app/project-data.json`.** Add valid sample projects under
   the `projects` array. Use varied names, owners, statuses, priorities,
   summaries, and activity text to exercise the UI.
3. **Coder: create `.vscode/launch.json`.** Add a strict-JSON configuration
   named `Run Project Pulse Dashboard` that starts
   `python3 -m http.server 5500` from `${workspaceFolder}/app` and opens
   `http://localhost:%s/index.html`.
4. **Designer: create `app/styles.css`.** Implement the agreed `.dashboard`,
   `.project-card`, and state hooks. Include responsive layout behavior,
   `border-radius`, `box-shadow`, focus-visible treatment, readable contrast,
   and text labels or other non-color-only indicators.
5. **Coder: create `app/index.html`.** Add the visible `Project Pulse` title,
   link `styles.css`, provide the dashboard container, fetch
   `project-data.json`, validate/normalize incoming records, and render one
   `.project-card` per valid project using the agreed hooks.
6. **Coder: implement resilient UI states.** Show a loading state before the
   fetch settles, an empty state when `projects` is empty or no valid records
   remain, and a failed-data state for fetch, parsing, shape, or validation
   failures.
7. **Orchestrator and owners: integrate and validate all four files.** Run the
   launch configuration and complete the validation expectations below without
   changing ownership boundaries.

## Explicit File Assignments

### `app/index.html` — Coder

- Provide the semantic document structure and visible `Project Pulse` heading.
- Link `styles.css`.
- Include the `.dashboard` render target and state messaging hooks defined by
  the contract.
- Fetch `project-data.json` over HTTP.
- Check `response.ok`, parse JSON, confirm the top-level `projects` array, and
  handle malformed or incomplete records predictably.
- Render cards from data rather than hard-coded project markup.
- Use safe text rendering such as `textContent`; do not interpolate untrusted
  data through `innerHTML`.
- Provide reasonable fallbacks for unknown/missing status and priority values.

### `app/styles.css` — Designer

- Exclusively owned by the Designer.
- Define `.dashboard` and `.project-card` styling.
- Use `border-radius` and `box-shadow` as part of the card presentation.
- Make the layout usable across narrow and wide viewports.
- Style loading, empty, error, status, and priority hooks agreed in the
  contract.
- Preserve legibility, contrast, visible keyboard focus, and non-color-only
  status/priority communication.

### `app/project-data.json` — Coder

- Exclusively created and maintained by the Coder.
- Use a valid top-level `{ "projects": [...] }` object.
- Supply multiple valid sample project records.
- Include all required fields in every record and recommend/include `summary`
  to exercise the card summary content.
- Use realistic variation, including longer content, to test wrapping and
  card-height behavior.

### `.vscode/launch.json` — Coder

- Exclusively created and maintained by the Coder.
- Must be strict JSON with no comments.
- Must be named `Run Project Pulse Dashboard`.
- Must run `python3 -m http.server 5500`.
- Must set `cwd` to `${workspaceFolder}/app`.
- Must open `http://localhost:%s/index.html`.
- Must not alter `.vscode/tasks.json`.

## Designer Responsibilities

- Translate the agreed CSS-hook contract into a clear, responsive dashboard.
- Design cards that accommodate short and long names, owners, activity text,
  and summaries without overlap or clipped critical information.
- Ensure status and priority have textual labels and are not distinguishable by
  color alone.
- Provide visible focus treatment for keyboard users and maintain readable
  contrast for all states.
- Avoid assuming a fixed number of cards or fixed data values.

## Coder Responsibilities

- Build the HTML, JSON data, and launch configuration only.
- Preserve the exact JSON schema and required project fields.
- Render cards entirely from fetched data and avoid hard-coded project cards.
- Make loading, empty, and error states explicit and deterministic.
- Treat fetched values as untrusted text and render them safely.
- Normalize absent or unrecognized status/priority values to explicit fallback
  text rather than failing the page.

## Dependencies

- The Coder's final data-driven HTML depends on the stable JSON schema and CSS
  class/state contract.
- The Designer's stylesheet depends on the agreed `.dashboard`,
  `.project-card`, and state hooks, not on the final HTML implementation.
- Browser fetching depends on serving `app/` through HTTP; it should not rely
  on opening `index.html` directly from `file://`.
- End-to-end validation depends on all four assigned files being present.

## Parallel-Work Decisions

After the Orchestrator publishes the schema/CSS-hook contract:

- The Designer may implement `app/styles.css` in parallel with the Coder's
  work on `app/project-data.json` and `.vscode/launch.json`.
- The data and launch work can proceed independently because the launch
  configuration does not consume the data contents, and the schema is already
  fixed.
- The Designer should style the agreed hooks without waiting for final project
  records.

## Sequential-Work Decisions

- The contract must be established first; work should not start from assumed
  schema or class names.
- The Coder's final `app/index.html` rendering implementation follows the
  stable schema and class contract.
- Integration validation follows completion of `index.html`, `styles.css`,
  `project-data.json`, and `.vscode/launch.json`.
- Any contract change after parallel work begins must be coordinated by the
  Orchestrator before either owner updates their file.

## Risks and Edge Cases

- **Fetch-over-file protocol:** Fetching JSON from an HTML file opened with
  `file://` can fail due to browser security behavior. Use the prescribed
  HTTP launch configuration.
- **Launch working directory:** If `cwd` is not `${workspaceFolder}/app`,
  `project-data.json` may not resolve and the server may expose the wrong
  directory. Verify the launch configuration exactly.
- **Malformed or invalid data:** Invalid JSON, a missing `projects` array,
  non-object records, or missing required fields must lead to an understandable
  failed-data or skipped-record outcome instead of an uncaught exception.
- **Varied or long content:** Long project names, owner names, activity
  strings, and summaries must wrap without breaking the layout.
- **Status/priority fallbacks:** Unknown, missing, or blank values must receive
  explicit fallback labels such as `Status unavailable` or `Priority
  unavailable`; the UI must not rely on an assumed finite set of values.
- **Non-color-only communication:** Status and priority must remain
  understandable through text labels, not only badge color.
- **Safe text rendering:** Project data must be inserted with safe DOM APIs
  such as `textContent`, avoiding `innerHTML` and preventing data values from
  being interpreted as markup.
- **Empty results:** A valid `{ "projects": [] }` response and a response in
  which all records are rejected must present a useful empty state.
- **Network/server failure:** A missing data file, non-OK HTTP response, or
  unavailable local server must show a clear error state rather than a blank
  dashboard.

## Validation Expectations

Integration validation must confirm all of the following:

1. `app/project-data.json` parses as JSON and has a top-level `projects` array.
2. Every sample project contains the required fields (`name`, `owner`,
   `status`, `recentActivity`, and `priority`), with `summary` included as
   recommended.
3. `app/index.html` contains the visible `Project Pulse` title, links
   `styles.css`, and loads `project-data.json`.
4. The stylesheet contains `.dashboard` and `.project-card`, as well as
   `border-radius` and `box-shadow`.
5. The app produces multiple rendered `.project-card` elements from the JSON
   data when previewed.
6. The `Run Project Pulse Dashboard` VS Code configuration starts the specified
   server from the required `app` working directory and opens
   `http://localhost:%s/index.html`, rather than a directory listing.
7. The dashboard remains readable and usable at narrow and wide viewport
   widths.
8. Keyboard navigation has visible focus, and status/priority meanings remain
   available without color perception.
9. Loading, empty-data, failed-fetch, invalid-data, unknown-status, and
   unknown-priority paths present clear, non-crashing feedback.
10. Project-provided text is rendered as text rather than executable markup.

## Open Questions

1. Which sample status and priority vocabulary should the initial data use
   (for example, `On track`, `At risk`, `Blocked`; `High`, `Medium`, `Low`)?
2. Should invalid individual records be skipped while valid records render, or
   should any invalid record place the dashboard in a full failed-data state?
3. What exact wording should loading, empty, and failed-data messages use?
4. Should recent activity be plain text only, or should a future iteration
   include timestamps after a date-format contract is defined?
5. Is a card click action or project detail view expected later? If so, reserve
   semantic structure without inventing navigation in this initial dashboard.

