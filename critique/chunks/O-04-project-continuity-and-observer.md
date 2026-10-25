# O-04 critique — project continuity and the observer

**Source:** `sources/penpot-session/penpot_hermes_oneshot-conversion.md`, lines 424–652.  
**Source artifact SHA at review:** `577e8cb920128fc77dc7dffb64212dfda3825faa`.  
**Status:** Interpretive critique of a compiled source section; not a reconstructed turn or proof of implementation.

## What this section proposes

O-04 proposes a repository-based continuity mechanism built from three main artifacts:

- a project constitution that records direction, phases, standing rules, and boundaries;
- contracts with status markers distinguishing planned, contracted, implemented, verified, or waived work;
- a reusable project-observer procedure that reads the constitution and contracts, runs tests, inspects task tracking and recent review reports, then writes a dated state assessment.

The session bootstrap rule makes the observer report a prerequisite to assigning work. The central claim is that project state should live in durable artifacts rather than in an agent's conversational memory, so a fresh session can reconstruct its position.

## Distinctions to preserve

1. **Durable state vs. session context:** the repository can carry forward evidence and decisions without requiring the next session to inherit the previous model context.
2. **Specification vs. implementation vs. verification:** these are distinct states and should not be collapsed into a single “done” label.
3. **Observer report vs. source evidence:** the report is a derived view of the project, not the ultimate authority for every fact it summarizes.
4. **Standing rule vs. task instruction:** the constitution establishes persistent constraints; a task still needs its own inputs, outputs, and completion conditions.
5. **Reconstruction vs. memory:** a fresh session should rebuild its working understanding from versioned artifacts and current checks, not assume that an earlier summary remains accurate.

These distinctions align strongly with the larger continuity problem: preserve enough provenance and state for a new observer to reconstruct why a conclusion is currently warranted.

## Observer-position critique

The proposal is directionally strong, but several statements turn useful conventions into stronger guarantees than the mechanism supports.

- **“The contract wins” is not always a safe universal rule.** A contract can be stale, internally inconsistent, superseded, or inconsistent with a user-approved change. A discrepancy should be recorded and resolved through an explicit authority/reconciliation rule; code should not automatically be treated as the only bug.
- **Status labels are not self-verifying.** A `verified` marker can become stale after code, tests, dependencies, or configuration change. Verification needs a link to evidence (such as a test identifier, run, revision, or report) and a rule for invalidating old evidence.
- **“The answer is mechanical, not interpretive” overstates the case.** Counting labels is mechanical once status data is trustworthy. Deciding whether the labels are accurate, whether tests cover the intended invariant, and whether a discrepancy is material requires judgment and provenance.
- **The observer's inputs can disagree.** Constitution, contracts, tests, Kanban, source code, and critic reports can each lag or conflict. The procedure needs to preserve disagreement rather than silently force a single story.
- **A latest report is not necessarily a current report.** Recency alone does not establish that the repository state has not changed since the report was written. Reports should identify the revision and evidence snapshot they observed.
- **The report can become another stale artifact.** The bootstrap rule should distinguish a report generated from current checks from a historical report reused as context. If tests cannot run, the observer should record the limitation rather than present an old assessment as current.
- **“Fully oriented in under a minute” is a hypothesis, not an established property.** Repository size, test duration, unavailable services, permissions, and incomplete artifacts can change the cost substantially.

The example status arithmetic is useful as a sketch, but its values are illustrative source content, not independently verified project state. This critique does not treat the Silicon Exchange phase or status counts as live facts.

## A stronger reconstruction record

A useful observer report should minimally record:

- observer timestamp and repository revision/commit;
- artifacts and paths read, with their revisions where practical;
- commands/tests run, exit status, and evidence location;
- checks not run and why;
- contract state as declared, separate from state inferred from implementation/tests;
- discrepancies and unresolved authority conflicts;
- evidence freshness or invalidation conditions;
- suggested next actions, clearly marked as suggestions rather than approved assignments.

The report should preserve separate fields for **declared state**, **observed state**, **evidence**, and **interpretation**. This prevents a summary from laundering uncertainty into a single status.

## Continuity is a reconstruction protocol, not just a file layout

The repository-based design is portable across agent frameworks, but portability depends on readable conventions and executable checks, not merely on Markdown/YAML formats. A new agent must know how to locate artifacts, interpret status values, execute validation, identify unavailable evidence, and report contradictions. The protocol is therefore the combination of artifacts, conventions, commands, authority rules, and evidence links.

The observer skill is best understood as a reusable capability that can be composed into different contexts. It need not imply a dedicated “observer agent” or a mandatory workforce role. Its invocation is a task/session condition; its output is durable project evidence.

## Questions left open

- What source has authority when constitution, contracts, code, and user direction conflict?
- Which events invalidate a `verified` status?
- How is the exact revision/evidence snapshot of a report represented?
- What happens when tests, Kanban, or integrations are unavailable?
- Which observations must be machine-checked, and which require explicit human/model judgment?
- How are corrections made without rewriting the historical record of what the prior observer saw?

## Provisional conclusion

O-04's central contribution is a practical continuity protocol: reconstruct project state from versioned artifacts and current evidence before acting. The critique strengthens that contribution by distinguishing stored labels from verified facts, derived reports from source evidence, and current reconstruction from inherited summaries.