# Critique chunk O-01 — Converting a one-shot prompt into a project structure

**Status:** Initial critique; provisional.  
**Observer position:** Persistent project state, task decomposition, and capability composition.  
**Source chunk:** `sources/penpot-session/penpot_hermes_oneshot-conversion.md`, lines 1–118.  
**Comparative anchor:** [Zephyr Hermes profiles](https://github.com/incus-skills/zephyr_hermes-profiles), used only as a subsequently introduced homologue.  
**Important distinction:** This is a different comparison from the Penpot design-to-code pipeline. The one-shot conversion explores how a large instruction can become a persistent project structure; it must not be merged into P-01 or P-02 as if they were the same episode.

## 1. Reconstruction — what the source proposes

The source starts from an explicitly supplied one-shot web-application prompt and asks how to adapt it into a project using the tooling identified so far. Its initial mapping distributes the prompt's sections across a task tree and artifacts: stack constraints become a contract, domain rules become specifications and tests, design becomes a Penpot artifact, implementation becomes code, and quality requirements become verification work.

It proposes:
- A Kanban task dependency tree.
- A selection of skills, a profile-delegation plugin, and a Penpot MCP/skill ecosystem.
- Separate builder and auditor contexts with different skill assignments.
- Contracts and named outputs to make the original prompt's requirements persistent and testable.

The source itself includes a FIXME noting that the conversion eventually approaches a project with AI ingress and documentation policy, but does so less formally than an established approach. This is a visible unresolved concern in the source, not a problem invented by the present critique.

The source also makes strong claims about profile-specific skills and avoiding context pollution. Those claims should be treated as hypotheses to evaluate against the clarified composability objective, not as validated results.

## 2. Challenge — does task decomposition require profile decomposition?

The conversion has a useful distinction between the original prompt and persistent project artifacts. A one-shot instruction holds many constraints in the active prompt; a project-oriented approach externalizes some of them into contracts, specifications, tests, and task dependencies.

But the mapping sometimes joins two different operations:
1. Subdividing work into tasks.
2. Assigning those tasks to different profiles or isolated contexts.

The first does not logically require the second. Tasks can be sequential or dependent while being executed in the same context; one context composition can be reused by multiple tasks; and one task may need multiple skills or integrations together.

The Zephyr homologue provides a concrete comparative structure for task routing, lifecycle checkpoints, and shared skills. It does not prove that this particular profile count or routing arrangement is appropriate for web development.

The challenge is therefore:
- Which requirements need persistent artifacts, independent of profile topology?
- Which task dependencies are real prerequisites?
- Which assignments are only labels inherited from a role-based metaphor?
- Can shared contracts and repository state provide continuity without a role-to-role handoff?
- Does the auditor need an isolated context for independent evaluation, or is a separately specified verification pass sufficient?

## 3. Response — preserve the one-shot experiment's distinct contribution

The one-shot conversion explores a different axis from the Penpot pipeline: **where project constraints and progress live after the initial prompt**.

Its strongest reusable hypothesis is not that every prompt section should become a worker role. It is that requirements should be made persistent, addressable, and verifiable through project artifacts and explicit task dependencies.

The critique therefore retains:
- Contracts as durable constraints.
- Named deliverables and tests.
- A task graph expressing genuine prerequisites.
- Project state that survives session boundaries.
- A review process with explicit criteria.

It does not automatically retain:
- One separate profile per task category.
- Mandatory delegation solely to access a different skill.
- The assumption that isolation is the default remedy for instruction conflicts.
- The assumption that a task's owner and its context composition are the same design decision.

## 4. Critique — new cognitive structure

Separate the **project continuity plane** from the **capability composition plane**.

### Project continuity plane

Contains the repository, contracts, design artifacts, tests, task state, decisions, and durable evidence of progress. It answers: what is the project required to do, what has changed, and what remains?

### Capability composition plane

Contains reusable skills, plugins, MCP integrations, instructions, and context presets. It answers: which capabilities should be available to perform or verify this task?

### Task dependency plane

Contains task inputs, outputs, prerequisites, and completion conditions. It answers: what work must happen, and in what dependency order?

These planes interact but should not be collapsed into a role hierarchy. For example, a task may require a contract from the continuity plane, select a context composition from the capability plane, and wait on another task in the dependency plane without requiring a new profile identity.

### Design rule

**Persist project knowledge in project artifacts; package capabilities for reuse; subdivide work by dependency; choose execution context separately.**

This allows a new Hermes instance to resume from explicit project state without requiring an elaborate chain of role handoffs to reconstruct what happened.

## 5. Comparison with the one-shot input

A fair comparison should not ask only whether the converted project has more files or tasks. It should test what the conversion preserves and what it adds.

| Dimension | One-shot prompt | Project-oriented conversion |
|---|---|---|
| Constraints | Primarily carried in the supplied prompt | Can be externalized into durable contracts |
| Work structure | Often implicit in the prompt's ordering | Can be expressed as task dependencies |
| Progress | Depends on the active session and available artifacts | Can be represented in repository/task state |
| Verification | Requirements may be described together with implementation | Can be expressed as named tests and completion conditions |
| Context cost | Large initial context may be required | Relevant artifacts and capabilities can be selected per task |
| Overhead | Prompt can be dense but coordination is implicit | More artifacts and explicit coordination are introduced |

Neither side is universally superior. The project approach trades initial prompt density for durable state and explicit structure; the benefit depends on whether continuity, verification, and task boundaries repay the additional artifacts and coordination.

## 6. Validation obligations

Use the same representative one-shot brief to compare:
- A one-context implementation with appropriate skills and durable project artifacts.
- A decomposed task workflow with context compositions selected per task.
- If useful, a role-separated workflow as a third comparator.

Hold the project requirements and acceptance tests constant. Compare:
- Requirement preservation.
- Missing or contradictory constraints.
- Context volume and repeated instructions.
- Number and cost of context transitions.
- Correctness and test coverage.
- Ability to resume from project artifacts in a fresh session.
- Whether a task or profile boundary actually improves a measured outcome.

The existing source's specific skill, plugin, MCP, and CLI instructions need current compatibility and security validation before being copied into an operational profile. This critique does not validate those external tools.

## 7. Unresolved questions

- Which parts of the original one-shot prompt are available in full and can be used as a fixed experimental input?
- Which contracts are genuinely needed for continuity, and which are artifacts introduced by the proposed workflow?
- Which current Hermes features can load capabilities dynamically or per task?
- How much coordination overhead does the proposed Kanban/delegation arrangement add?
- Can the same verification independence be achieved without a separate auditor context?
- How should the project bootstrap procedure distinguish confirmed state from suggestions or inferred progress?

## 8. Disposition

**Provisional finding:** The one-shot conversion's most reusable contribution is the externalization of constraints, dependencies, and progress into durable project artifacts. Its role-based task assignments are one possible execution arrangement, not an inherent requirement of projectization.

**Next step:** Compare O-01 and P-02 to identify shared principles and genuine differences, then critique the remaining source chunks before defining the operational profile architecture.
