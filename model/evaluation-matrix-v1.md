# Context-composition evaluation matrix v1

**Status:** Proposed validation plan for the provisional operational model. No experiment results are implied.

## 1. Purpose

Test whether candidate context compositions improve task performance enough to justify their context, routing, handoff, and maintenance costs. Hold requirements, contracts, task inputs, and acceptance criteria as constant as possible when comparing compositions.

Do not use profile count as a proxy for quality. Do not infer independence from a profile name.

## 2. Candidate compositions

| ID | Composition | Intended use | Main question |
|---|---|---|---|
| CMP-A | Single task context with the minimum sufficient capability set; explicit in-context review pass | Small/ordinary changes | Can one context complete and self-review without excessive context or instruction conflict? |
| CMP-B | Implementation context followed by a fresh verification context; same contracts, diff, revision, and evidence | Higher-risk or independence-sensitive changes | Does fresh-context review find meaningful additional defects that offset handoff cost? |
| CMP-C | Implementation context plus conditional design/browser/accessibility capabilities, then verification | Design-sensitive changes | Do specialized evidence sources improve fidelity or defect detection beyond generic code review? |
| CMP-D | Integration/runtime context with explicit resource permissions and runtime boundary checks | Service, deployment, CI, MCP, credential, or network changes | Are access and side-effect boundaries explicit and effective, independent of profile layout? |
| CMP-E | Fresh-session reconstruction from repository artifacts and available checks only | Continuity/bootstrap | Can the new session accurately reconstruct state without relying on previous conversation memory? |

These are experimental configurations, not a required five-profile set. CMP-C and CMP-D may be compositions layered onto CMP-A or CMP-B.

## 3. Representative test cases

### T-01 — Small implementation change

Give the same bounded code task and acceptance criteria to CMP-A and an appropriate multi-context alternative.

Check: correctness, contract adherence, test results, review findings, context/routing overhead, and missing assumptions.

### T-02 — Higher-risk change

Compare an in-context review pass with CMP-B's fresh verification context.

Give both reviewers the same diff, contract, relevant requirements, and revision-bound evidence. Record which defects each detects, severity, false positives, and whether reviewer access differs.

### T-03 — Design-sensitive change

Compare generic review with CMP-C, where design artifact and rendered/browser evidence are available.

Check: visual fidelity, interaction behavior, accessibility findings, duplicate findings, reproducibility, and tool/runtime failures.

### T-04 — Integration substitution

Replace or simulate replacing an implementation behind a defined affordance contract.

Check: contract conformance, error behavior, authorization, side effects, consistency, adapter changes, and whether unrelated consumers need edits.

Do not declare equivalence from matching names or schema alone.

### T-05 — Fresh-session recovery

Start a new session with repository artifacts and the documented bootstrap procedure, but without conversational continuity.

Require it to identify current revision, governing requirements, declared state, observed state, verification evidence, missing checks, conflicts, and next permissible actions.

Compare every claim to repository/runtime evidence. Missing evidence must be reported as unknown, not guessed.

### T-06 — Runtime boundary check

In the intended test environment, inspect filesystem mounts, persistence, process/container privileges, network access, endpoint bindings, credential scope, log redaction, and permitted side effects.

Attempt only authorized, safe test actions. The goal is to verify the declared boundary, not to infer it from a profile name.

### T-07 — Quality-check overlap

Run candidate quality checks against the same artifact and record their triggers, evidence access, invariants, methods, outputs, authority, duplicate findings, and conflicts.

Use results to choose co-loading, conditional invocation, substitution, or omission.

## 4. Measures

Record at least:

- task acceptance pass/fail and requirement coverage;
- defects detected, grouped by severity and whether independently reproducible;
- false positives and unresolved findings;
- verification conditions not exercised;
- duplicated findings and overlapping checks;
- context/token use where the runtime exposes reliable measurements;
- number of context transitions and handoff items;
- elapsed execution/review time where comparable;
- recovery accuracy for declared/observed/verified project state;
- unauthorized or unintended side effects;
- maintenance burden: duplicate instructions, profile-specific exceptions, stale artifacts.

Avoid combining all measures into one score unless the weighting is defined before the experiment. Keep qualitative observations and failure cases alongside numeric measurements.

## 5. Evidence protocol

For each trial record:
1. task identifier and fixed task specification;
2. composition and resource versions;
3. repository revision and relevant contract versions;
4. available tool permissions and runtime configuration;
5. exact acceptance criteria and checks;
6. results, logs, artifacts, and review findings;
7. deviations, unavailable tools, and skipped checks;
8. costs/overhead and interpretation;
9. unresolved confounders.

Where possible, compare repeated trials. If a result depends on a different model, tool version, context budget, or evidence set, record that as a confounder rather than attributing the difference to profile composition alone.

## 6. Decision rules

Prefer the simpler composition when it meets the acceptance criteria and no material quality, safety, or continuity advantage is demonstrated by added boundaries.

Keep a separate verification context when:
- independence is explicitly required, or
- repeated comparable trials show material additional defect detection or reduced confirmation bias that justifies the handoff cost.

Add an isolated runtime boundary only when a concrete access, persistence, network, process, or trust requirement needs it. Do not use profile separation as a substitute for enforcing that boundary.

Make a capability conditional when it provides value only for identifiable task types and its always-loaded cost or instruction overlap is material.

Remove or merge a check when it provides no distinct invariant/evidence benefit and creates redundant cost, unless it is retained intentionally as independent confirmation.

These are decision rules for the experiment, not claims about outcomes.

## 7. Exit criteria for final profile derivation

A later profile proposal should not be considered ready until:

- each proposed profile/context preset maps to a concrete task need;
- every capability is reusable independently of any role label where technically possible;
- required resources, permissions, and runtime assumptions are documented;
- real task dependencies are distinct from context-selection choices;
- continuity can be reconstructed from versioned artifacts and revision-bound evidence;
- overlapping checks are mapped and composition rules are explicit;
- source-era technology recommendations have been checked against the target runtime/version;
- unresolved design choices and failed experiments remain visible.

## 8. Open decisions

- Which Hermes version and loading mechanisms are the target?
- Which capabilities can be expressed as skills versus profile instructions versus repository-local guidance?
- What are the actual context-size and tool-loading costs?
- Which tasks require independent review by policy rather than by measured benefit?
- What evidence format will the project use to tie status to revisions and test runs?
- What is the minimum project bootstrap artifact set that supports reconstruction without excessive ceremony?
