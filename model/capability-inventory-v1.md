# Capability inventory v1

**Status:** Provisional inventory extracted from critique P-01–P-06 and O-01–O-05. This describes capability candidates, not installed skills, confirmed Hermes features, or mandatory profiles.

## How to read this inventory

- **Capability** describes a reusable behaviour or ability.
- **Resources** are possible means of providing it (skills, plugins, MCPs, toolsets, APIs, files, runtime services).
- **Context** describes when the capability may be composed with others.
- **Evidence** describes what would show that the capability did its job.
- **Boundary** identifies the actual reason to isolate it, if any.

The resource names and integrations below are drawn from the source examples. Their current availability and compatibility have not been verified.

| ID | Capability | Inputs | Outputs / evidence | Typical composition | Boundary condition |
|---|---|---|---|---|---|
| CAP-01 | Project-state reconstruction | Repository revision; governing instructions; contracts; task/status records; prior reports | Revision-bound state assessment; unresolved conflicts; missing evidence | Session/bootstrap; before consequential work | Read-only access should be the default; no dedicated profile required |
| CAP-02 | Requirement and contract tracing | Requirement; acceptance conditions; domain/design/stack/affordance contracts | Trace from requirement to implementation artifact and named check | Implementation and verification | Separate only if contract ownership or access differs materially |
| CAP-03 | Task decomposition and dependency planning | Goal; prerequisites; project state; deliverable constraints | Task graph with inputs, outputs, blockers, completion conditions | Planning or orchestration procedure | Split tasks for genuine dependencies, parallelism, context limits, or verification |
| CAP-04 | Design-artifact inspection | Penpot/design file; design tokens; relevant component specification | Extracted design constraints, tokens, component details, traceable observations | Design-sensitive implementation and review | Penpot integration/access boundary, not “designer” identity, determines constraints |
| CAP-05 | Design-artifact modification | Explicit design change; current design state; design rules | Updated design artifact or documented proposed change | Design-sensitive task | Separate when permissions, tool constraints, or independent design approval require it |
| CAP-06 | Implementation and code editing | Task contract; repository state; dependencies; design/domain requirements | Code/config change; diff; affected contract list | Implementation | Context split only for meaningful context, task, or permission boundaries |
| CAP-07 | Integration operation | Integration contract; endpoint/resource binding; permissions; task input | API/MCP/plugin operation result and traceable side effects | Any task requiring external capability | Access, credentials, side effects, and runtime policy govern; do not assume tool availability implies authorization |
| CAP-08 | Visual/browser inspection | Running application; route/state; expected design/interaction behavior | Screenshot or observations, reproducible steps, visual/interaction findings | Design-sensitive verification | May require browser/runtime access; separate context only if useful or required |
| CAP-09 | Accessibility and design-quality audit | Acceptance criteria; DOM/rendered state; design guidance; relevant code | Findings tied to invariant and evidence; optional prioritized recommendations | Implementation or verification | Overlap should be evaluated by actual checks, evidence access, and authority |
| CAP-10 | Automated test and CI verification | Revision; test selection; runtime; configuration | Test command/run, revision, exit status, logs/artifact references | Implementation and verification | CI environment is a runtime boundary; secrets and untrusted code need explicit rules |
| CAP-11 | Change review and critique | Diff; contracts; requirement trace; test evidence; rendered output where relevant | Findings with severity, evidence, confidence, and unresolved questions | Verification; optional independent review | Fresh context only when independence benefit justifies handoff and context loss |
| CAP-12 | Repository/Git workflow | Branch, diff, commit, review policy, remote, authorization | Versioned change, commit identity, PR state, review/evidence links | Implementation, verification, release | Merge authority and branch permissions are not implied by review capability |
| CAP-13 | Runtime/deployment operation | Deployment contract; target; endpoints; filesystem/persistence; credentials; policy | Reproducible configuration/change, execution logs, boundary checks | Integration/runtime task | Actual process/container/host permissions determine isolation |
| CAP-14 | Data-ingress and CMS interaction | Domain model; ingestion contract; source data; validation and retry rules | Validated data changes, error/retry record, consistency evidence | Data/backend/integration task | Local API, REST, and queue choices require explicit failure/security/consistency semantics |
| CAP-15 | Continuity/evidence reporting | Current revision; requirements; check results; conflicts; changes | Observer report with declared/observed/verified state kept distinct | After reconstruction, verification, or handoff | Report must identify evidence and revision; never treat the report as ultimate source truth |

## Cross-cutting resource relationships

These relationships should be represented explicitly where applicable:

- A capability **uses** a resource; the resource does not become owned by the capability.
- A context **composes** capabilities and instructions.
- A task **requires** capabilities and resources; it does not require a distinct profile unless justified.
- A contract **constrains** an implementation binding.
- A check **produces evidence for** an acceptance condition.
- An observer report **summarizes** evidence and declared state but does not replace them.
- A runtime policy **authorizes or denies** actions independently of context composition.
- Two quality capabilities may **overlap**, **complement**, or **conflict**; decide from trigger, evidence access, invariant, method, output, and authority—not their names alone.

## Candidate quality-check record

For every concrete check identified during later implementation, record:

| Field | Meaning |
|---|---|
| Check ID | Stable identifier |
| Trigger | Task conditions that cause it to run |
| Evidence access | Source, design, DOM, rendered output, test logs, or other inputs |
| Invariant | The property the check evaluates |
| Method | Deterministic, test-based, structural, visual, heuristic, or manual/model judgement |
| Output | Finding, score, patch, test result, recommendation |
| Authority | Informational, blocking, or authorized to modify |
| Evidence reference | Revision, run, artifact, location, or reproducible steps |
| Overlap | Other checks that inspect the same invariant |
| Composition rule | Always compose, conditional, substitute, or omit |

## Confidence and limitations

The inventory is an analytical extraction from the source critique. It does not claim these capabilities exist as individual installable skills, that a given MCP or plugin currently provides the described behavior, or that the target Hermes runtime supports a particular dynamic loading mechanism. Those are implementation checks for a later phase.
