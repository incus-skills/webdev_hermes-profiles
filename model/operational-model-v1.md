# Provisional operational model v1

**Status:** Working model derived from critique P-01–P-06, O-01–O-05, and cross-chunk comparison v4. Not a final Hermes profile catalogue, not a claim that the example stack has been deployed, and not a retroactive reconstruction of missing conversation turns.

## 1. Purpose

Translate the source critique into a testable operating model for a Hermes web-development profile collection. The model must support context-size management and task subdivision without treating profiles as a virtual workforce.

The model distinguishes five analytical graphs:

1. Capability/resource graph.
2. Context-composition graph.
3. Task-dependency graph.
4. Project-continuity graph.
5. Runtime/resource graph.

Validation crosses all five. These are lenses on one system, not five required services, files, agents, or profiles.

## 2. Design invariant

**Keep capabilities independently addressable; compose them into task contexts according to demonstrated needs; express real work dependencies without assuming they require separate profiles; define contracts by observable semantics and constraints; bind contracts to explicit implementations; persist project state and correction history in versioned artifacts; tie verification claims to revision-specific evidence; and enforce execution isolation through actual runtime permissions rather than profile labels.**

## 3. Operating layers

### A. Reusable capabilities

A capability is a bounded procedure or ability that can be reused: for example, reconstruct project state, inspect a design artifact, implement a change, run a test suite, or audit a rendered result.

Each capability record should state:
- stable identifier and purpose;
- trigger/when useful;
- required instructions, skills, plugins, MCPs, tools, and references;
- required inputs and produced outputs;
- permissions and possible side effects;
- relevant contracts and quality invariants;
- evidence of completion;
- known overlaps, conflicts, and limitations.

A capability does not imply a dedicated profile.

### B. Context composition

A context preset selects the instructions and capabilities likely to be useful for a class of task. It is a loading/configuration convenience, not a worker identity.

Choose the smallest sufficient composition that can satisfy the task and its validation obligations. Add capabilities when the task requires them. Split contexts only when there is a concrete reason, such as context-budget pressure, conflicting instructions, an independence requirement, a permission boundary, or a genuinely separate task lifecycle.

Do not assume that a profile change creates execution isolation. Verify runtime boundaries independently.

### C. Task decomposition

A task is a unit of work with:
- a goal and acceptance conditions;
- inputs and prerequisites;
- expected outputs;
- verification requirements;
- permitted side effects;
- status and evidence.

Create separate tasks where dependencies, parallelism, review needs, or context constraints justify them. Do not create a task or handoff solely to mirror a role name. Multiple tasks may use the same context preset; one task may need several capabilities.

### D. Project continuity

Project-specific truth belongs in the project repository or another explicitly designated authoritative store. Collection-level files should contain reusable guidance, not copies of a particular project's live requirements or status.

Maintain separate representations for:
- **Declared state:** what requirements, contracts, task records, and decisions say.
- **Observed state:** what the current repository or runtime appears to contain or do.
- **Verification evidence:** which checks ran against which revision and their results.
- **Observer assessment:** the interpretation, unresolved contradictions, and proposed next steps.

An observer report must not silently collapse these categories. Tie it to the revision and evidence snapshot it examined. Record checks that could not run.

### E. Contracts and implementation bindings

A contract describes required observable semantics, not merely method names or data shapes. Where relevant, record inputs, outputs, invariants, errors, side effects, authorization, and compatibility constraints.

Keep domain-level requirements distinct from provider/adapter-specific contracts. Record the current implementation binding. Treat substitution as a conformance claim that requires evidence.

### F. Runtime and access

Record actual execution targets, process/container boundaries, filesystem and persistence mounts, endpoints, network reachability, credential references/scopes, allowed consumers, and permitted side effects.

A capability being available in a context does not mean every action is authorized. A profile being separate does not prove that its process, credentials, or files are isolated. Isolation must be established and tested in the runtime/resource layer.

## 4. Minimal candidate context compositions

These are **candidate presets for evaluation**, not finalized Hermes profile files.

### C0 — Project reconstruction

**Purpose:** Establish a grounded view of project state before consequential work.

**Capabilities:** Read governing instructions; identify repository revision; inspect contracts, task records, recent changes, and prior observer reports; run permitted read-only checks; distinguish declared/observed/verified state; record conflicts and uncertainty.

**Default permissions:** Read-only. Any mutating action requires explicit task authorization.

**Output:** Revision-bound state assessment, relevant artifacts/evidence, unresolved issues, and candidate next actions clearly distinguished from approved work.

**Can be combined with:** Any task context where the task is small and reconstruction overhead is acceptable. It need not be a persistent standalone profile.

### C1 — Implementation

**Purpose:** Make a bounded change against explicit requirements.

**Capabilities:** Read relevant contracts and source; edit files; use applicable language/framework skills; invoke required integrations; run targeted checks; update task/evidence records as authorized.

**Required inputs:** Task statement, acceptance conditions, current revision, relevant contracts, allowed side effects.

**Output:** Change set, explanation of contract impact, test/check results, and remaining gaps.

**Boundary rule:** Do not load unrelated capability packages by default. Add design, accessibility, data-ingress, or infrastructure capabilities only when the task needs them.

### C2 — Verification

**Purpose:** Assess a change against acceptance criteria and evidence.

**Capabilities:** Inspect the diff, contracts, implementation, rendered output where relevant, tests and CI results; run permitted checks; report findings with severity and evidence.

**Default permissions:** Read-only or narrowly scoped test execution. Modification is a separate authorized action.

**Independence option:** Run in the implementation context as a distinct review pass for low-risk tasks, or in a fresh context for tasks where independence is important. Do not assume the fresh context is better; evaluate it.

**Output:** Findings tied to files, requirements, revision, and evidence; explicit untested conditions; pass/block/recommendation separated from merge authority.

### Conditional composition — Design-sensitive work

For tasks involving visual or interaction fidelity, add design-artifact inspection, design tokens, browser/rendered-output inspection, accessibility checks, and visual comparison as needed. This is a capability overlay, not automatically a separate “designer” profile.

### Conditional composition — Integration/runtime work

For changes to services, deployment, MCPs, CI, credentials, containers, or network topology, add the relevant integration and runtime capabilities. Use an actual least-privilege execution boundary where required. A separate context is optional; runtime isolation is decided independently.

## 5. Default task lifecycle

1. **Reconstruct:** identify the current revision, governing requirements, and known state.
2. **Bound:** define goal, inputs, outputs, acceptance conditions, permissions, and side effects.
3. **Compose:** select the smallest sufficient capability set and context preset.
4. **Execute:** perform work and preserve the change set.
5. **Verify:** evaluate the change against contract-linked checks and task acceptance conditions.
6. **Reconcile:** resolve or record conflicts among declared state, observed state, and evidence.
7. **Persist:** update only the authoritative project artifacts affected by the task; attach revision-bound evidence.
8. **Handoff only when needed:** pass references to durable artifacts, not a duplicate narrative of the whole project.

This lifecycle is a reusable procedure, not a requirement for eight agents or eight separate tasks.

## 6. Decision rules for adding boundaries

Add a separate context when at least one of these is demonstrated or explicitly required:
- active context is too large or instruction conflicts measurably impair performance;
- independent review is a stated quality requirement;
- access permissions differ and the runtime can enforce the distinction;
- a task has a real independent lifecycle or can proceed in parallel;
- the tool/runtime requires a separate process or environment.

Prefer co-loading or a bounded in-context procedure when:
- the capabilities share the same evidence and permissions;
- the task is small and sequential;
- the handoff would mostly restate repository state;
- separation is based only on role labels;
- no measured benefit offsets the routing and reconciliation cost.

A separate context is not a substitute for a security boundary. A security boundary is not a reason to duplicate capability instructions unnecessarily.

## 7. Required evidence and state transitions

Avoid free-floating status labels. At minimum distinguish:
- planned;
- specified/contracted;
- implemented (artifact/revision identified);
- checked (named check executed, result recorded);
- verified (acceptance condition supported by adequate evidence);
- blocked;
- waived (with authority and rationale).

These labels are proposed semantics and must be mapped to the project's actual conventions before use. A status is not evidence. A verified claim should reference the revision, check/run, result, and the requirement it supports. Define invalidation when relevant code, contract, dependencies, configuration, or environment changes.

## 8. Known tensions and unresolved choices

- Co-loading saves routing overhead but can increase context size and instruction conflict.
- Separate review contexts may improve independence but add handoff cost and can lose implementation context.
- Provider-neutral contracts improve replaceability but may omit capabilities the project actually needs.
- Durable continuity artifacts improve reconstruction but can become stale or redundant.
- Shared integrations reduce duplication but require access controls and secret boundaries.
- Automated checks improve repeatability but cannot alone prove the acceptance criteria are sufficient.
- More formal structure can prevent drift but may be excessive for small tasks.
- The source-era technology recommendations need current verification before operational adoption.

## 9. Scope and provenance

This model is derived from compiled thematic Markdown artifacts and the critique records. It does not infer original turn chronology, user acceptance of every source proposal, or successful deployment of the example stack. The Zephyr homologue is comparative material introduced later, not a retroactive correctness test.

## 10. Next validation step

Use the accompanying capability inventory and evaluation matrix to test the candidate compositions against representative tasks. Do not finalize profile names, default skill bundles, or runtime boundaries until the target Hermes version and actual capability-loading behavior have been checked.
