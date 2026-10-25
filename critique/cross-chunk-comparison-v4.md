# Cross-chunk comparison v4 — runtime boundaries, correction history, and composition

**Status:** Provisional synthesis after P-01–P-06 and O-01–O-05. This is a critique-derived model, not a final profile architecture or implementation claim.

## What the final source pair adds

P-06 tests the emerging model against execution, persistence, source control, CI, service integration, and credentials. O-05 revisits the architecture through corrections, tool/skill distinctions, overlapping quality checks, and proposed profile assignments.

Together they make one separation especially important:

**A capability configuration is not an execution boundary, and a review role is not automatically an independent observer.**

Profiles determine which instructions and capabilities are available in a context. Actual isolation depends on runtime processes, filesystems, network namespaces, permissions, credentials, and execution policy. Review independence depends on the evidence and context available to the reviewer, not on a profile name alone.

## Consolidated model: five graphs plus validation

The five dimensions identified earlier remain distinct:

1. **Capability/resource graph:** skills, plugins, MCPs, toolsets, shared references, dependencies, and permissions.
2. **Context-composition graph:** which instructions and capabilities are available together for a task.
3. **Task-dependency graph:** work units, prerequisites, inputs, outputs, completion conditions, and blockers.
4. **Project-continuity graph:** requirements, contracts, decisions, artifacts, implementation state, test results, and provenance.
5. **Runtime/resource graph:** processes, execution targets, endpoints, filesystems, persistence, credentials, model providers, and access policy.

Validation crosses all five. It checks not just whether each graph is documented, but whether their relationships hold in practice.

These are analytical dimensions, not a requirement to create five files, five services, or five profiles.

## Refined invariant

**Keep capabilities independently addressable; compose them into task contexts according to demonstrated needs; express real work dependencies without assuming they require separate profiles; define contracts by observable semantics and constraints; bind contracts to explicit implementations; persist project state and correction history in versioned artifacts; tie verification claims to revision-specific evidence; and enforce execution isolation through actual runtime permissions rather than profile labels.**

## Cross-cutting findings

### 1. Contracts require semantics and conformance evidence

Stable names and data shapes can be part of a contract, but do not establish compatibility on their own. Contracts should specify relevant behavior, error modes, permissions, side effects, and invariants. Adapter bindings identify which implementation currently supplies the contract. Replacement is a tested compatibility claim, not a naming convention.

### 2. Continuity requires evidence provenance and disagreement handling

Declared status, observed implementation, test results, and observer interpretation should remain separately visible. A status such as `verified` needs a revision-bound evidence reference and an invalidation rule. When sources conflict, the observer should record the conflict and the authority/reconciliation question rather than automatically declaring one source correct.

### 3. Runtime isolation is independent of context composition

A profile may have terminal and MCP tools without isolating those tools from one another. A separate profile may share the same filesystem and credentials. The runtime/resource graph must state which actions are possible and authorized, what persists, and where secrets may flow.

### 4. CI and review are complementary only when their gates are defined

Mechanical checks establish results for specified tests at a particular revision. Human/model review evaluates whether those checks and artifacts satisfy broader requirements. The gate must specify required results, blocking findings, waivers, approval authority, and invalidation. A score alone is not a merge policy.

### 5. Skill overlap must be measured at the check level

Similar skill names may hide complementary evidence access, while different names may duplicate the same invariant. Record trigger, evidence access, invariant, method, output, authority, and composition rule for each check. Use that inventory to decide whether to co-load, conditionally invoke, or omit a capability.

### 6. Corrections are part of the provenance

O-05 contains repairs and clarifications. Preserve them as evidence of how the proposal changed, but do not treat source-era package, version, issue, or feature claims as current without revalidation. The compiled documents do not establish exact turn chronology, so the critique must not invent it.

## Tensions not resolved by synthesis

- Context co-loading saves routing and handoff overhead but can increase token use and instruction conflict.
- Separate review contexts may reduce confirmation bias but can also lose useful implementation context; the benefit must be tested.
- Provider-neutral contracts improve portability but can conceal required provider-specific behavior.
- Persistent state supports continuity but creates maintenance and stale-evidence risks.
- Shared services reduce duplicated configuration but increase the importance of least privilege and secret boundaries.
- Automated tests improve repeatability but cannot alone establish that the acceptance criteria are sufficient.
- More explicit architecture can prevent accidental coupling but can also over-formalize a small project.
- A profile catalogue can organize reusable capabilities without implying a team of agents or independent execution processes.

## A practical evaluation plan before final profile derivation

Do not derive a fixed profile set from source labels alone. Evaluate representative tasks against competing compositions:

1. **Small implementation task:** one context with builder capabilities, followed by a review pass.
2. **Higher-risk implementation task:** implementation and independent review contexts, with the reviewer given the same contract and revision-bound evidence.
3. **Design-sensitive task:** compare generic code-quality guidance with design-tool-specific checks using their actual evidence access.
4. **Integration-change task:** test an adapter substitution against the affordance contract and its conformance suite.
5. **Fresh-session recovery task:** reconstruct state from the repository without relying on prior conversation, and record missing/stale/conflicting evidence.
6. **Runtime-boundary task:** verify filesystem, network, credential, and persistence behavior in the intended execution environment.

Measure completion quality, missed defects, duplicated checks, context cost, handoff overhead, state-reconstruction accuracy, and unauthorized side effects. Keep the results task-specific; do not generalize from one task to a universal profile rule.

## Source and confidence limits

This synthesis covers the available compiled Markdown artifacts and their thematic ranges. It does not recover absent conversational turns, prove user acceptance of every proposal, validate vendor claims, or establish that any suggested architecture was deployed. The Zephyr homologue remains a later comparison, not a retroactive correctness oracle.

## Pass conclusion

The source review pass is complete across P-01–P-06 and O-01–O-05. The next phase should move from critique toward a provisional operational profile model, using the five graphs as analytical lenses and the evaluation plan as a check against unnecessary profile proliferation. Preserve unresolved questions as explicit design choices rather than silently resolving them in the profile files.