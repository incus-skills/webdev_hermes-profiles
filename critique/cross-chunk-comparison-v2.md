# Cross-chunk comparison v2 — resource, context, task, and continuity structures

**Status:** Provisional synthesis after P-01, P-02, P-03, and O-01, O-02. Not a final development model.

## Current distinctions

The critique so far supports keeping five concerns separate:

1. **Capability/resource graph:** reusable skills, plugins, MCPs, toolsets, shared references, and their dependencies/permissions.
2. **Context-composition graph:** which instructions and capabilities are active together for a class of task.
3. **Task-dependency graph:** tasks, prerequisites, inputs, outputs, and completion conditions.
4. **Project-continuity graph:** requirements, contracts, decisions, source artifacts, implementation state, tests, and evidence.
5. **Runtime/resource graph:** execution backends, network endpoints, filesystems, credentials/secret references, model providers, and access policy.

Validation cuts across all five: it tests whether the represented relationships and the resulting operational system behave as intended.

## Evidence from the chunks

| Chunk | Distinct contribution to the current model | Main caution |
|---|---|---|
| P-01 | Shared Penpot artifact can connect design, implementation, and review without necessarily requiring separate contexts | Pipeline stages are not proof of profile boundaries |
| P-02 | Skills can be reusable while context composition and task subdivision remain independent | The source's profile configurations are proposed, not demonstrated |
| P-03 | Multiple contexts can refer to shared integrations; runtime and inference are distinct from context topology | Source-era technical and pricing claims need current validation |
| O-01 | A one-shot brief can be converted into persistent project structure | Task decomposition does not require profile decomposition |
| O-02 | Requirements can be linked to contracts, implementation artifacts, named tests, and evidence | Structured files alone do not guarantee valid semantics or coverage |

## Provisional invariant

**Keep capabilities independently addressable; compose them according to task needs; express genuine work dependencies in a task graph; persist requirements and progress in project artifacts; share integrations through explicit resource/access contracts; isolate execution only when a concrete benefit justifies the context and handoff cost.**

This is a working synthesis, not yet a settled architecture. It must be challenged by the remaining source chunks and checked against actual Hermes behavior.

## Tensions to preserve

- Co-loading reduces routing overhead but can increase context size and instruction conflicts.
- A separate review context may improve independence, but the benefit must be tested against a well-defined review pass in one context.
- Persistent contracts support continuity but can become documentation overhead or drift from implementation.
- Shared integrations reduce duplication but require explicit access boundaries and secrets management.
- A clear task graph improves dependency visibility but should not become an unnecessary delegation graph.
- The Zephyr homologue provides concrete patterns, not a universal template.

## Next chunks

Continue with P-04 (backend profile, data flow, source of truth) and O-03 (Forgejo project shape). The next comparison should focus on durable state and task lifecycle, not profile count.
