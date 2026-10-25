# Cross-chunk comparison v3 — contracts as boundaries, evidence as continuity

**Status:** Provisional synthesis after P-01–P-05 and O-01–O-04. This is a critique-derived model, not a final profile architecture or implementation claim.

## What the new chunks add

P-05 and O-04 meet at the contract boundary, but approach it from different directions:

- **P-05** asks how the project can depend on capabilities without being coupled to their current implementations.
- **O-04** asks how a new session can determine what the project currently requires, what has been implemented, and what evidence supports that account.

A contract can help both, but it serves two distinct purposes: it defines a compatibility boundary, and it contributes to a reconstructable record of project state. These purposes overlap without being identical.

## Refined distinctions

| Concern | Question answered | Evidence or mechanism needed | Failure mode if collapsed |
|---|---|---|---|
| Domain requirement | What must remain true for the project? | Named invariant, acceptance condition, or requirement | Vendor details become mistaken for the requirement |
| Affordance contract | What capability must be available, with what semantics? | Inputs, outputs, behaviour, errors, permissions, invariants | Matching names or schemas are mistaken for compatibility |
| Adapter binding | Which implementation currently supplies the capability? | Versioned configuration and adapter mapping | A provider-specific dependency becomes invisible |
| Declared project state | What do the project artifacts say is planned or active? | Constitution, contract status, task records | Declared intent is mistaken for observed reality |
| Observed implementation state | What does the current repository/runtime appear to do? | Source inspection, execution, tests, runtime checks | Code presence is mistaken for correctness |
| Verification evidence | What supports the claim that a rule works? | Test/run identity, revision, result, coverage rationale | A stale “verified” label survives changes |
| Observer synthesis | What is the current assessment and what remains uncertain? | Dated report tied to revision and inputs | Interpretation silently replaces contradictory evidence |

## Refined invariant

**Keep capabilities independently addressable; define contracts in terms of observable semantics and constraints; bind them explicitly to implementations; compose capabilities according to task needs; express genuine work dependencies in a task graph; persist requirements and observed state in durable artifacts; tie verification claims to revision-specific evidence; and isolate execution only when a concrete benefit justifies the context and handoff cost.**

This refines the prior invariant by adding two protections: contracts need semantics beyond names/shapes, and continuity claims need evidence provenance beyond status labels.

## Tensions that remain live

- A provider-neutral contract improves replaceability but may abstract away capabilities the project genuinely needs.
- A provider-specific contract is precise but makes coupling explicit and may increase migration cost.
- Co-loading capabilities reduces routing overhead but increases context size and instruction-conflict risk.
- A separate review context may improve independence, but should be compared empirically with review in one context.
- Durable status and reports improve continuity but can drift unless tied to revisions and invalidation rules.
- Machine-checkable state enables repeatability, but adequacy of tests and authority in conflicts still require judgment.
- More contract structure can prevent accidental drift but can also become documentation overhead; only meaningful boundaries should be formalized.

## Observer procedure implied by the synthesis

A session-start observer should not simply read the latest report and accept it as truth. It should:

1. identify the current repository revision and relevant governing artifacts;
2. read declared requirements, contract status, task state, and prior reports;
3. run the available checks, recording skipped or failed checks;
4. compare declared, observed, and verified states without merging them prematurely;
5. record conflicts, missing provenance, and stale evidence explicitly;
6. produce a revision-bound assessment with proposed next actions clearly separated from authorized work.

This is a reconstruction protocol. It can be invoked by an orchestrator or any suitable context; it does not imply a dedicated agent workforce.

## Scope and confidence

This synthesis is derived from compiled thematic source artifacts. It does not reconstruct the original conversational turn order, establish that the proposed architecture was implemented, or validate source-era claims about specific vendor versions. The Zephyr homologue remains a later comparative source, not a retroactive correctness test.

## Next review

Proceed to P-06 and O-05 separately. P-06 should test the model against runtime boundaries, execution backends, and Git/CI continuity. O-05 should be treated as a correction-and-synthesis section: preserve its explicit corrections and unresolved overlaps rather than flattening it into a simple final answer.