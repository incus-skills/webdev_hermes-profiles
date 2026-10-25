# Critique chunk O-02 — Task outputs, contracts, and verification

**Status:** Initial critique; provisional.  
**Observer position:** Project continuity and verifiable constraints, independent of profile/worker topology.  
**Source chunk:** `sources/penpot-session/penpot_hermes_oneshot-conversion.md`, lines 119–340.  
**Comparative anchor:** Zephyr's lifecycle/checkpoint patterns, introduced after the original one-shot conversion.

## 1. Reconstruction

This chunk gives concrete inputs and outputs for a sample GPU-rental web application. It decomposes the work into domain/data modelling, Penpot design, business logic and tests, frontend implementation, and end-to-end quality assurance. It proposes machine-readable stack/domain/design contracts and maps domain rules to named tests.

It also explicitly compares one-shot and project-oriented approaches. The project form externalizes some constraints into contracts, uses a design file as a structured artifact, and names verification tasks. The source's proposed mapping associates these tasks with role-labelled profiles and a dependency chain.

The artifacts and test mappings are concrete design proposals. They do not establish that the sample application was implemented, that the contract schema was validated, or that the proposed Hermes tool invocations work with the target version.

## 2. Challenge

The main challenge is to distinguish **what must persist** from **who or which profile performs the work**.

Domain rules, design tokens, stack constraints, and test requirements need durable, addressable representations if they must survive across sessions or task boundaries. That does not require a separate profile for each artifact or task.

Likewise, a task dependency is not automatically a handoff between profiles. A frontend task may depend on both the Penpot file and domain functions, while the relevant context composition may contain the necessary skills and MCP access together.

The contracts also create a risk of false precision: machine-readable structure is useful only if its fields have defined semantics, validation rules, and a clear relationship to tests. A YAML list of rules does not by itself guarantee coverage or correctness.

## 3. Response

The most reusable part of this chunk is its attempt to externalize requirements into durable contracts and named tests. That supports continuity and independent verification without committing to a particular profile topology.

The task graph should express prerequisites and deliverables. Context selection should be a separate decision. Contracts should be validated mechanically where possible and should identify which rules have passing tests, which remain unimplemented, and which are explicitly deferred.

The original source's status labels in later sections (contracted, implemented, verified, waived) are a promising pattern, but their exact semantics and applicability need to be reviewed across the full source before adoption.

## 4. Critique — new cognitive structure

Treat each requirement as a traceable chain:

**Requirement → durable contract/decision → implementation artifact → named verification → status/evidence.**

Treat task dependencies separately:

**Input artifacts → task prerequisite → output artifacts → completion condition.**

Treat context composition separately again:

**Task needs → required skills/integrations/instructions → selected context.**

This provides three linked but distinct structures:
- Requirement/evidence graph.
- Task dependency graph.
- Capability/context composition graph.

A profile is not the owner of a requirement. A task's output and evidence should remain in the project state so another context can continue without reconstructing the entire history from a handoff summary.

## 5. Validation obligations

For a representative project:
1. Check every requirement has a stable identifier and an authoritative source.
2. Check each contract field has defined meaning and a schema or other validation procedure.
3. Map each testable rule to one or more named tests.
4. Distinguish tests written, tests executed, and tests passing.
5. Verify that the task graph names its input and output artifacts and real prerequisites.
6. Start a fresh Hermes session and verify that project state can be reconstructed from repository artifacts without relying on a role-specific memory.
7. Compare a composed-context workflow with a multi-context workflow while holding contracts and acceptance tests constant.

## 6. Unresolved questions

- Which contract files and fields are essential for the sample project versus illustrative detail?
- How should conflicting or superseded requirements be represented?
- Which status transitions are validated by automation?
- What is the minimum useful set of artifacts for continuity without excessive documentation overhead?
- Which test claims can be mechanically verified, and which require human or model judgement?

## 7. Disposition

**Provisional finding:** The one-shot conversion usefully turns implicit prompt constraints into durable, testable project state. That contribution is independent of the role-based routing arrangement used in the example.

**Next step:** Compare the contract and task structures here with P-04's backend/source-of-truth discussion and the lifecycle patterns in Zephyr. Preserve unresolved questions about documentation overhead and contract semantics.
