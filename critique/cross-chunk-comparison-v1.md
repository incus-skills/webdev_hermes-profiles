# Cross-chunk comparison — P-01, P-02, and O-01

**Status:** Provisional synthesis of three critiques, not a final architecture.

## Why these chunks must remain distinct

- **P-01** explores a design-to-code workflow where a shared Penpot artifact mediates between design and implementation.
- **P-02** explores skill installation, profile composition, and task activation, including an explicit one-profile/multiple-skills alternative.
- **O-01** explores converting a dense one-shot prompt into persistent project artifacts and a task dependency structure.

They overlap in their use of profile labels and task pipelines, but they are not evidence of one continuous decision episode. The source files do not provide enough turn-level provenance to assert that they were developed in the sequence implied by their document order.

## Common structure emerging from critique

Across these three chunks, the same distinctions recur:

1. **Capabilities/resources:** skills, plugins, MCPs, toolsets, and reusable behaviours.
2. **Context composition:** the set of instructions and capabilities made available together for a task.
3. **Task decomposition:** work units and real dependencies between them.
4. **Project continuity:** durable constraints, artifacts, decisions, tests, and progress.
5. **Execution isolation:** a separate context or runtime, which requires its own justification.
6. **Validation:** evidence that a capability or workflow achieves its intended outcome.

The emerging structure is not a workforce chart. It is a set of interacting models for resources, contexts, tasks, project state, and validation.

## Current provisional invariant

**Keep capabilities independently addressable; compose them according to the task; persist project state in durable artifacts; subdivide work by genuine dependencies; isolate execution only when a concrete benefit justifies the context and handoff cost.**

This invariant is supported by the user's clarified design intent and is being developed through critique. It should be tested against the remaining chunks and the Zephyr homologue before being promoted to a final development model.

## Tensions to preserve

- Co-loading can reduce routing overhead but increase active context and instruction conflicts.
- Separate review contexts may improve independence but introduce handoff and state-transfer costs.
- Durable contracts and project records improve continuity but can become needless ceremony if they duplicate information without improving validation.
- Zephyr's explicit lifecycle and routing patterns are informative, but their suitability depends on the task structure and runtime constraints of web development.
- The one-shot conversion and the Penpot pipeline illuminate different aspects of the design problem and should not be collapsed into one experiment.

## Next chunks

Continue with P-03 (deployment and runtime boundaries), then O-02 (contracts and task outputs), retaining separate critique records. Only after the remaining chunks have been examined should we consolidate the cross-chunk model.
