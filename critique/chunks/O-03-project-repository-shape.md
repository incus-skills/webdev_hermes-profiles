# Critique chunk O-03 — Repository shape as durable project state

**Status:** Initial critique; provisional.  
**Observer position:** Project continuity and affordance boundaries, separated from profile topology.  
**Source chunk:** `sources/penpot-session/penpot_hermes_oneshot-conversion.md`, lines 341–423.  
**Comparative anchor:** Zephyr's repository/profile separation and lifecycle artifacts, introduced after the original conversion.

## 1. Reconstruction

The chunk proposes a Forgejo repository structure containing contracts, human-readable domain rules, data types and seed data, domain functions and tests, application routes, components, exported Penpot tokens, CI workflow, and package metadata. It contrasts the original dense one-shot prompt with a shorter orchestrator brief plus contracts and task bodies.

The proposed project structure makes many requirements and outputs persistent in version control. The source claims that each piece can be owned by a role, verified by a gate, and swapped independently.

**Evidence limit:** The tree is a proposed example. It does not prove that every file is necessary, that all components are truly swappable, or that the example has been built and validated.

## 2. Challenge

The repository is a useful place for durable project state, but “one file per concern” can become excessive ceremony if artifacts duplicate one another or have no validation process. Conversely, an overly compressed project brief may fail to preserve important decisions.

The claim that each piece is “owned by one role” should be challenged under the current model. File responsibility can be expressed through authoritative sources, dependency links, and validation obligations without making a worker-like profile the owner.

The affordance-boundary idea is more durable than the role assignment: a stable interface allows one implementation to be replaced without forcing unrelated code to change. But this is true only if consumers actually depend on the interface rather than reaching around it.

## 3. Response

Retain the repository as the primary durable store for project-specific contracts, code, tests, design exports, decisions, and status that must survive sessions. Keep reusable Hermes capabilities and collection-level setup in the Hermes collection. Avoid duplicating the same authoritative content in both places; use references or generated views where appropriate.

Task records should point to durable artifacts and named completion conditions. Profile selection should be handled separately from file layout.

## 4. Critique — new cognitive structure

Separate **collection state** from **project state**:

### Collection state
Reusable skills, profile/context presets, integration requirements, capability composition guidance, reusable validation procedures, and instructions for bootstrapping a project.

### Project state
The actual repository, requirements, contracts, design files, code, tests, decisions, current implementation status, deviations, and evidence.

### Session state
Transient reasoning and current task focus. Important conclusions must be promoted to project artifacts or collection-level guidance when they need to survive the session; session state alone is not an authoritative store.

These three scopes should have explicit boundaries and links. A fresh session should bootstrap by reading project state and the applicable collection guidance, not by assuming that its predecessor's context survives.

### Design rule

**Store durable project truth in the project repository; store reusable capability and context-management procedures in the collection; treat session context as transient unless explicitly externalized.**

## 5. Validation obligations

1. Give a fresh Hermes session only the project repository and collection bootstrap instructions.
2. Ask it to report active contracts, planned but absent contracts, current implementation status, known gaps, and next valid actions.
3. Verify every claim against repository artifacts and test evidence.
4. Check whether duplicate documents disagree or whether a single authoritative source is clearly identified.
5. Change one implementation behind an affordance boundary and test whether unrelated consumers remain unchanged.
6. Measure whether each proposed artifact contributes to continuity, validation, or independent reuse; remove or merge artifacts that provide no distinct value.

## 6. Unresolved questions

- Which repository files are essential to the sample project and which are illustrative?
- What is the canonical source for each requirement and status value?
- Should project bootstrap be a skill, a profile instruction, a repository-local instruction, or a combination? The choice should follow actual loading behavior and reuse needs.
- How are collection version and project-specific compatibility declared?
- How should a new session represent uncertain status without treating absence of evidence as proof that a feature is absent?

## 7. Disposition

**Provisional finding:** The repository layout is best understood as a proposal for durable project state and affordance boundaries, not as a map of profile ownership. Collection guidance should enable a fresh context to reconstruct project state from these artifacts.

**Next step:** Examine P-05 and O-04, focusing on contracts, observer behavior, bootstrap, and status reconstruction.
