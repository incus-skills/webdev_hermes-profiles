# O-05 critique — corrections, overlap, and profile assignment

**Source:** `sources/penpot-session/penpot_hermes_oneshot-conversion.md`, lines 653–845.  
**Source artifact SHA at review:** `577e8cb920128fc77dc7dffb64212dfda3825faa`.  
**Status:** Interpretive critique of a compiled source section; not a reconstructed turn or proof of implementation.

## What this section contains

O-05 is not simply a final architecture summary. It combines a proposed design/data/CMS/ingress stack, clarifications about Penpot token synchronization and the role of MCP servers versus skills, a database recommendation, explicit corrections to resource references, an overlap analysis among design-quality skills, and a proposed builder/auditor assignment.

It should therefore be read as a **correction-and-synthesis layer**: some statements amend earlier proposals, some add distinctions, and some are new recommendations. Their placement in the compiled document does not prove original conversational chronology or user acceptance.

## Corrections and distinctions to retain

### 1. Skills and MCPs occupy different functional layers

The source's distinction is useful: an MCP server exposes callable capabilities; a skill supplies instructions, procedures, and quality criteria for using capabilities. This should not be treated as a strict universal taxonomy of every plugin or tool integration, but it prevents a common category error: installing a capability does not itself encode the policy for its use, and a policy document does not provide the capability it describes.

A profile may compose both. That does not require each capability or skill to belong exclusively to one role.

### 2. Development-time and runtime data paths are distinct

The source distinguishes Payload MCP tooling for development-time scaffolding/validation from runtime access through REST or an in-process Local API. That boundary matters operationally: a tool that can generate or validate schema code should not automatically be treated as the production data channel. Before operational adoption, each MCP's actual tools, permissions, maintenance status, and behavior need current verification.

### 3. Data-ingress patterns are alternatives with different consequences

Same-process Local API calls, separate-service REST calls, and queued ingestion are not interchangeable implementation details. They imply different deployment coupling, failure handling, retry behavior, authentication, throughput, and consistency. The correct choice depends on load, trust boundary, recovery semantics, and where the scraper runs; the source's table is a decision surface, not a universal ranking.

### 4. Quality-skill overlap is a composition problem

The source identifies overlapping responsibilities among generic art-direction guidance, Penpot-specific automated checks, and post-build auditing. Its practical insight is that multiple skills can cover the same quality dimension at different points or with different evidence access.

However, “different triggers” do not by themselves prove there is no duplication. The same task may activate several instructions simultaneously, and overlapping rules can create redundant passes or conflicting thresholds. The right unit of analysis is the **quality check**: its trigger, evidence access, invariant, output, and authority—not just the skill name.

### 5. Corrections to links are provenance, not timeless truth

The section records corrections to resource references. These should remain in the historical critique because they show the source's own repair process. The current availability, official status, contents, and installation instructions for those resources require fresh verification before they are copied into a live setup. The corrections should not be silently elevated into permanently valid operational facts.

## Profile-assignment critique

The proposed builder/auditor split is a plausible starting configuration, but the evidence does not establish that these need to be separate profiles in every task.

A more precise formulation is:

- The **builder task context** needs the capabilities and instructions required to implement the requested change.
- The **review task context** needs the acceptance criteria, relevant source artifacts, implementation diff, and verification evidence.
- A separate review context may be useful when independence is valuable, but independence should be an explicit requirement and should be tested. A single context can also run a review pass if the task is small or context budget is constrained.
- Capability packages can be independently addressable and composed into either context; their names should not hard-code a permanent organizational role unless that restriction has a concrete purpose.

This retains the useful distinction between implementation and evaluation without turning profiles into a virtual workforce. A profile is a context/capability configuration, not evidence of a separate process, identity, security boundary, or autonomous worker.

## Overlap-analysis unit

For each potentially overlapping skill or tool, record:

| Field | Diagnostic question |
|---|---|
| Trigger | Under what task conditions does it activate? |
| Evidence access | Can it inspect code, design objects, rendered output, tests, or only instructions? |
| Quality invariant | What property is it checking or enforcing? |
| Method | Is the check heuristic, deterministic, visual, structural, or test-based? |
| Output | Does it emit a score, patch, finding, test result, or recommendation? |
| Authority | Can it modify artifacts, block progression, or only advise? |
| Redundancy/conflict | Does another capability check the same invariant with equivalent evidence? |
| Composition rule | Should both run, should one be conditional, or should one be omitted? |

This makes overlap measurable rather than inferred from similar labels.

## Technical claims and temporal scope

The architecture section includes versions, MCP tool counts, package recommendations, database compatibility statements, issue references, and current-status claims. They belong to the source-era record. They are not validated here and should not be used as current installation or production guidance without a separate current-source pass.

Likewise, the source's suggested profile assignment is a proposed configuration, not an empirical comparison of context quality, cost, defect detection, or routing overhead.

## Questions left open

- Which quality checks are genuinely distinct, and which duplicate the same invariant?
- Which checks require an independent observer position to be credible?
- Which tool integrations are needed at design time, build time, runtime, and verification time?
- What evidence justifies choosing Local API, REST, or a queue for ingestion?
- Which corrections are historically important but need current verification before use?
- How will the profile configuration be evaluated against a simpler co-loaded context?

## Provisional conclusion

O-05 adds useful distinctions and repairs, but should not be treated as a canonical lock merely because it occupies the final section of a compiled artifact. Its durable contribution is the method: distinguish capability from instruction, development-time from runtime access, and genuine quality-check complementarity from duplicated skill coverage. Profile boundaries remain hypotheses to test against task and evidence needs.