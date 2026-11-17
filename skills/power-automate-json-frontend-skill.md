---
description: Inspects and edits supplied files from an extracted Power
  Automate cloud-flow export to add, change, or modularize HTML, CSS,
  and JavaScript frontend output. Works from definition.json and any
  separately supplied supporting export files. Supports legacy exports
  and solution-derived flow definitions when the relevant files are
  supplied. Does not manipulate ZIP archives or use the Power Automate
  web designer.
name: power-automate-json-frontend
---

# Power Automate Modular Front-End Architecture (JSON Edition)

## Overview

A standardized method for building and maintaining HTML, CSS, and
JavaScript inside a Power Automate cloud flow. It prioritizes
maintainability, debuggability, preservation of existing flow behavior,
and clear documentation.

**Environment:** Power Automate Online (Cloud Flows) only. No Power
Automate Desktop, Power Apps canvas, local scripts, or direct
manipulation of the Power Automate web designer.

**Use when:** Building, fixing, or restructuring HTML, CSS, or
JavaScript inside a cloud flow, including HTML emails, approval content,
embedded forms, Adaptive Cards, and other flow outputs that contain
frontend markup or presentation logic.

**Do not use for:** Pure data flows with no frontend output,
general-purpose flow redevelopment, or tasks that only involve editing
the Power Automate web designer.

## Operating constraints and input model

The AI cannot extract, inspect, create, or repackage ZIP archives. The
user exports the flow and supplies the extracted files individually.

The user may provide: - `definition.json` (the primary workflow
definition). - Connection metadata, such as `connections.json`, where
included. - Manifest and export metadata files. - Other JSON or
supporting files included in the extracted export. - Existing HTML, CSS,
JavaScript, text, or documentation files relevant to the flow.

Do not ask the user to upload a ZIP. Do not claim to have opened,
extracted, rebuilt, or validated a ZIP package.

### File inventory

1.  Inventory every supplied file before editing.
2.  Identify the primary flow definition and classify all other files by
    purpose.
3.  Determine, from supplied evidence, whether the definition appears to
    come from a legacy flow export or a solution-derived export. Do not
    assume a fixed folder structure or file naming convention.
4.  Identify references and relationships between supplied files.
5.  Distinguish files required for reconstruction/import from files that
    are informational or unrelated.
6.  If a required file appears to be missing, explain exactly what is
    missing and why it matters. Ask for it only if the task cannot
    safely proceed.
7.  Never assume that `definition.json` is the only file relevant to
    import integrity.

The AI works only with files actually supplied. It must not invent
missing files, metadata, connection references, IDs, or package
structure.

## How we work

1.  **Input:** The user supplies `definition.json` and any available
    supporting files from the extracted export. Existing frontend source
    files may also be supplied.
2.  **Inventory:** Inspect all supplied files and identify their roles,
    relationships, and any missing information that blocks safe editing.
3.  **Map:** Inspect the trigger, actions, scopes, branches,
    expressions, variables, dependencies, connections, and frontend
    output destination.
4.  **Plan:** Identify the exact changes required. If an essential
    detail is unclear, ask before editing. Do not guess field names,
    data sources, scope placement, output destination, or connection
    purpose.
5.  **Patch:** Modify only the required JSON nodes and supporting files.
    Preserve unrelated structures, metadata, ordering, and formatting as
    far as practical.
6.  **Validate:** Perform static and structural checks and compare the
    result with the original.
7.  **Deliver:** Return the updated files, source artifacts, notes, and
    a clear change report. The user reconstructs the export and imports
    it.

Do not rebuild the flow from scratch unless the user explicitly requests
it.

## Core architectural principles

### 1. Strict separation of concerns

HTML (structure), CSS (theming and styling), and JavaScript (behavior
and data manipulation) are independent streams during development. Do
not lump all three into one source block.

Preferred architecture when it fits the existing flow:

-   One parent scope wraps the frontend assembly:
    `Scope - Front-End Assembly`.
-   Three child streams may run concurrently:
    -   HTML: `Scope - HTML Structure` (HTML structure only; no CSS or
        JS).
    -   CSS: `Scope - CSS Theming & Styling` (design tokens, theme,
        layout, component styles, badges, and pills).
    -   JS: `Scope - JS Actions & Logic` (data parsing, event listeners,
        DOM manipulation, and interactive functions).
-   A final consolidation action combines the streams:
    `Compose - Final Output`.

The three streams may run concurrently only when they are genuinely
independent. They must not write concurrently to the same variable or
depend on one another's outputs. Use separate outputs and explicit
dependencies.

Do not force this architecture onto an existing flow when doing so would
break dependencies, introduce concurrency hazards, or cause unnecessary
restructuring. Adapt the architecture to the flow's existing execution
model.

Final consolidation order, when applicable:

1.  `<style>` containing CSS.
2.  HTML markup.
3.  `<script>` containing JavaScript, only where the destination
    supports it.

The final consolidation action must run after every required stream has
completed. Its `runAfter` dependencies must reference the actual stream
actions or scopes.

### 2. Destination compatibility and security

Before writing frontend code, identify where the output will be
consumed. Do not assume all destinations support the same markup or
behavior.

Examples: - **HTML email:** Most email clients restrict or disable
JavaScript. CSS and layout support vary by client. - **Approvals:**
Rendering depends on the approval experience and client. - **Adaptive
Cards:** Use supported Adaptive Card schema and host capabilities.
Arbitrary HTML and JavaScript are not interchangeable with Adaptive Card
elements. - **SharePoint-hosted content:** Browser capabilities depend
on the hosting method, tenant policy, and security restrictions. -
**Teams messages:** Supported formatting and interaction depend on the
message type and Teams surface.

If the requested destination cannot support a requested feature, explain
the limitation and propose a compatible implementation. Do not silently
substitute a different output type.

Treat dynamic data as untrusted. Apply context-appropriate encoding to
values inserted into HTML, attributes, URLs, CSS, or JavaScript. Do not
concatenate untrusted values into executable script or markup without
suitable handling. Avoid unsafe URL schemes and inline event attributes
where they are not required and supported.

### 3. Modularity and complexity thresholds

The 20,000-character threshold is a maintainability guideline, not a
universal Power Automate platform limit.

Use these guidelines for each source component or stream:

  -----------------------------------------------------------------------
  Source character count              Guidance
  ----------------------------------- -----------------------------------
  Under 15,000                        Normal

  15,000--20,000                      Warn and assess complexity

  Over 20,000                         Evaluate splitting

  Over 30,000                         Splitting strongly recommended
                                      unless there is a documented reason
                                      not to
  -----------------------------------------------------------------------

Count source characters before inserting them into JSON. Also report the
serialized JSON string size where useful; JSON escaping can increase the
stored size. Do not confuse source size with serialized size.

When a stream is too large or difficult to maintain, split it into
sequential sub-scopes with descriptive names and separate outputs.

Examples:

-   HTML: `Scope - HTML - Header & Nav` →
    `Scope - HTML - Main Data Grid` → `Scope - HTML - Footer & Modals`
-   CSS: `Scope - CSS - Theme & Design Tokens` → `Scope - CSS - Layout`
    → `Scope - CSS - Components`
-   JS: `Scope - JS - State Initialization` →
    `Scope - JS - Data Processing & Lookups` →
    `Scope - JS - Event Handlers`

Sub-steps within a stream run in sequence. The stream's final output
combines the outputs of its sub-steps.

Apply the same principle to any complex section, not only HTML, CSS, or
JS. Split for maintainability and debuggability, not merely to satisfy
an arbitrary number.

### 4. Naming conventions

Apply clear naming to new flows, triggers, scopes, actions, and
variables when those items are being created. Do not rename existing
actions unless explicitly asked, because expressions and `runAfter`
references may depend on their internal names.

-   Flow name: `[Purpose] - [Trigger/Source] - [Output]`
    -   Example: `Weekly Report - SharePoint - HTML Email`
-   Scope: `Scope - [Layer] - [Section]`
    -   Examples: `Scope - Front-End Assembly`,
        `Scope - HTML Structure`, `Scope - CSS Theming & Styling`
-   Action: `[Action Type] - [Layer] - [What It Does]`
    -   Examples: `Compose - HTML - Table Rows`,
        `Compose - CSS - Badges`, `Compose - JS - Event Handlers`
-   Variable: `var[Layer][Section]`
    -   Examples: `varHTMLHeader`, `varCSSBadges`, `varFinalHTML`,
        `varJSInit`

Use the action's actual internal name in expressions and dependency
references. Display names and internal action keys are not necessarily
interchangeable.

### 5. Flow dependency map

Before editing, create an internal structural map of the existing flow.
Provide a concise summary to the user when useful.

Inspect: - Trigger and trigger conditions. - Scope nesting. - Actions
and internal action keys. - Display names, where available. - `runAfter`
dependencies and status conditions. - Conditions, switches, loops, and
parallel branches. - Variables and initialization points. - Expressions
that reference other actions or variables. - Outputs consumed by later
actions. - Connection references and connector metadata. - Frontend
generation and delivery actions.

A simplified map may look like:

``` text
TRIGGER
  |
  v
Scope - Initialization
  |
  v
Scope - Data Collection
  +---- SharePoint Get Items
  +---- Excel Get Rows
  |
  v
Scope - Front-End Assembly
  +---- HTML stream
  +---- CSS stream
  +---- JS stream
  |
  v
Compose - Final Output
  |
  v
Send Email
```

This is an explanatory map, not a replacement for the actual flow
definition. Do not infer missing actions or dependencies from an
example.

### 6. Safe patching, preservation, and rollback

Patch, do not reformat or rebuild.

1.  Keep the original supplied files unchanged as the baseline.
2.  Identify the exact JSON paths and supporting files that need
    modification.
3.  Record actions added, modified, or removed.
4.  Preserve unrelated JSON structures, action properties, metadata,
    ordering, and formatting as far as practical.
5.  Do not replace an entire action when only one input, expression, or
    property needs changing.
6.  Do not remove unknown properties simply because their purpose is
    unclear.
7.  Do not rename existing action keys or variables unless explicitly
    requested and all references can be safely updated.
8.  Compare the completed files against the original files.
9.  Make the smallest viable change set.

If a change requires modifying a supporting file or cross-file
reference, explain why and preserve consistency across the supplied
files.

### 7. Expressions, serialization, and escaping

Power Automate definitions contain multiple interpretation layers. Treat
them separately:

-   JSON syntax and escaping.
-   Power Automate workflow expressions.
-   Dynamic content references and interpolation.
-   HTML markup and entity encoding.
-   CSS syntax and escaping.
-   JavaScript syntax, string literals, and data serialization.

Preserve expressions such as:

``` text
@{outputs('Get_items')?['body/value']}
```

as Power Automate expressions where intended. Do not accidentally
convert expressions into literal text or escape them in a way that
changes their meaning.

When embedding source into JSON: - Use a proper JSON serializer or
equivalent safe serialization method. - Preserve intended line breaks
and quotation marks. - Check backslashes, apostrophes, quotation marks,
Unicode, and control characters. - Avoid double-encoding HTML
entities. - Do not apply blanket escaping to an entire expression or
markup block. - Validate the resulting JSON after serialization.

Check expressions independently from frontend rendering. JSON parsing
alone does not prove that a Power Automate expression is valid or
evaluates as intended.

### 8. Flow notes: one file per flow, required

Every modified flow gets one documentation `.txt` file explaining the
flow itself, not every individual action.

Use this layout:

``` text
Flow: [Flow name]
Purpose: What the flow does and why it exists
Trigger: What starts the flow and what data it receives
Data sources: Which connections the flow uses and what each provides (names only, no connection IDs)
Front-end output: What the HTML/CSS/JS produces and where it is delivered
Stream structure: How the HTML, CSS, and JS streams are organized and consolidated
Sub-steps: Any stream split into smaller sections, and why
Inputs and outputs: Variables or values the flow expects and returns
Known limits: Character counts near thresholds, destination restrictions, and assumptions to verify
Validation status: What was checked and what still requires an actual import or run
```

Keep entries short and plain. Notes must describe the flow as actually
built. Update them whenever the flow changes. Do not document proposed
architecture as though it has already been implemented.

## Standard frontend execution pipeline

Use this pipeline when it fits the flow and destination:

1.  **Initialization:** Set up required variables and state, respecting
    existing flow logic.
2.  **Frontend parent scope:** Add or use `Scope - Front-End Assembly`
    where appropriate.
3.  **Parallel streams:** Run independent HTML, CSS, and JS streams
    concurrently only when safe.
4.  **Complexity check:** Split large or difficult streams into
    sequential sub-steps.
5.  **Final consolidation:** Combine the streams and required values in
    `Compose - Final Output`.
6.  **Delivery:** Pass the output to the existing email, approval,
    SharePoint, Teams, or other destination action, subject to its
    compatibility constraints.

Do not add redundant scopes or actions merely to match this template.
Preserve the existing design when it is already sound.

## Output files and artifact rules

The user supplies extracted export files individually. The AI does not
manipulate the ZIP archive.

Return: - The complete updated `definition.json`. - Any other supplied
export file that genuinely required modification. - Standalone source
artifacts for frontend content. - One notes `.txt` file per modified
flow. - A concise change and validation report.

### Source artifact versus runtime artifact

Keep these distinct:

-   **Source artifact:** Readable HTML, CSS, or JS in its own
    downloadable `.txt` file, where applicable.
-   **Runtime artifact:** The corresponding content inserted into the
    appropriate Compose action, variable, or other flow action in the
    updated JSON.
-   **Final output:** The assembled content the flow actually delivers
    at runtime.

The source `.txt` file is for review and maintenance. It does not
replace the runtime content in the flow definition.

For HTML output, use a separate `.txt` file, never `.html`, and state
exactly which Compose action or variable receives it.

Any CSS or JS intended to be pasted or maintained separately also gets
its own `.txt` file. If CSS or JS is embedded in the runtime output,
document where it is assembled.

Do not produce a canvas, preview app, or interactive UI unless the user
separately requests one.

### Supporting export files

-   Return unchanged supporting files only when they are needed for the
    user to reconstruct the supplied export or when the user requests a
    complete set.
-   Do not regenerate manifest, connection, or package metadata without
    a demonstrated need.
-   If a supporting file must change, explain the reason and validate
    relevant cross-file references.
-   Do not claim the result is a complete import package: the user is
    responsible for reconstructing the export and importing it.

## Validation before returning

Perform the checks that are possible with the supplied files and clearly
distinguish static validation from tenant-dependent runtime testing.

### Static validation (required)

-   Updated JSON parses without errors.
-   Every `runAfter` target exists in the correct scope.
-   Every referenced action key exists where required.
-   New scopes and actions follow naming conventions.
-   Existing connection references and IDs are unchanged unless
    explicitly authorized.
-   Expressions and dynamic content references are preserved.
-   Variables are initialized before use, where determinable.
-   Consolidation references resolve to actions that exist.
-   HTML, CSS, and JS source blocks are syntactically reviewed.
-   Character counts are reported for each source block.
-   Blocks near or over thresholds are flagged.

### Structural validation (required)

-   Scope and branch nesting is valid.
-   Execution dependencies are coherent.
-   Parallel branches do not create unsafe shared-variable writes.
-   `runAfter` conditions and status values are preserved or
    intentionally changed.
-   Output actions consume the intended content.
-   Supplied cross-file references remain consistent.
-   Untouched sections have not been unnecessarily rewritten.

### Runtime validation (tenant-dependent)

Only report runtime validation if the flow was actually imported and run
in the user's Power Automate environment.

Runtime checks may include: - Import success. - Connection binding and
authentication. - Trigger behavior. - Representative test data. - Run
history and action outputs. - Actual HTML rendering in the destination
client.

Never claim that a flow was imported, executed, or rendered successfully
based only on static JSON inspection. Clearly list anything the user
must verify.

## Ask before guessing

Stop and ask a concise clarification when an essential detail is
unclear, including: - Which scope a block belongs in. - Which data
source or field supplies a value. - Whether a section should be split. -
Where the HTML output is delivered. - Whether JavaScript is expected to
execute in the destination. - What a connection reference represents. -
Whether a change is intended to affect existing flow behavior.

Do not ask questions whose answers are already present in the supplied
files. If uncertainty is non-blocking, state the uncertainty and avoid
changing the affected area.

## Reporting

Finish with a concise report containing:

1.  **Files produced:** Exact filenames and purpose.
2.  **Actions added or changed:** Include scope and action names, and
    identify removed actions if any.
3.  **Character counts:** Source character counts for each HTML, CSS,
    and JS block; include serialized sizes when useful.
4.  **Splitting:** Identify streams split into sub-steps and explain
    why.
5.  **Preservation:** Confirm whether connection references, IDs, and
    unrelated actions were preserved.
6.  **Validation:** Separate static/structural checks completed from
    runtime checks not performed.
7.  **User steps:** State which files the user needs to put back into
    the extracted export and any import or runtime checks they must
    perform.

Do not overstate validation. Do not claim ZIP handling, package
reconstruction, import success, or runtime success unless those actions
were actually performed.
