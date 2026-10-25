# Critique chunk P-04 — Data model, contracts, and source-of-truth boundaries

**Status:** Initial critique; provisional.  
**Observer position:** Shared project state, contract derivation, and task dependency—not role ownership.  
**Source chunk:** `sources/penpot-session/penpot_hermes.md`, lines 453–620.  
**Comparative anchor:** Zephyr lifecycle checkpoints and shared resources, introduced after the original exploration.

## 1. Reconstruction

The chunk extends the design-to-frontend pipeline to full-stack development. It proposes a schema/API architect function, backend implementation with database access, frontend implementation against the design and API contract, and end-to-end verification. It identifies a source-of-truth problem among the design file, database schema, and API contract, and proposes a central derivation step to keep these representations aligned.

The proposal makes several durable artifacts explicit: schema, API contract, data-flow description, migrations, test data, and task dependencies. It also assigns these activities to role-labelled profiles and proposes that changes in the design trigger downstream regeneration.

**Evidence limit:** This is an architectural proposal in the source document, not proof that a central architect profile is the only or best way to maintain consistency. The source does not demonstrate an automated change-propagation mechanism merely by describing a Kanban dependency chain.

## 2. Challenge

The core issue is real: a full-stack application may contain multiple representations of related domain concepts, and drift between them can break integration. But the proposed “single point of derivation” combines a useful consistency principle with a specific role-based implementation.

Ask instead:
- What is the authoritative representation for each kind of fact?
- Which artifacts are derived from which other artifacts?
- Which changes invalidate downstream artifacts or tests?
- Which invariants can be checked automatically?
- Is a distinct architect context required, or can a bounded schema/API design task use the same capability composition as implementation?
- Does the dependency system actually detect changed upstream artifacts and re-open downstream tasks, or does the proposal assume this behavior?

A single human- or model-labelled role does not itself create a source of truth. Authority must be encoded in the project artifacts and validated relationships.

## 3. Response

Retain the need for explicit authority and derivation, but represent it as a **contract and provenance graph**, not as ownership by a profile.

For each artifact, record:
- Its purpose and scope.
- Which facts it is authoritative for.
- Its source inputs and derived outputs.
- Its validation procedure.
- Which downstream artifacts/tests may be invalidated by changes.
- Its current status and provenance.

A schema/API design capability can be independently reusable. It need not be permanently assigned to an isolated architect profile.

## 4. Critique — new cognitive structure

Use a **consistency graph** linking:
- Product requirements and domain rules.
- Design artifact and user-facing fields.
- Data model/schema.
- API contract.
- Implementation and migrations.
- Tests and validation evidence.

Edges should mean `derived-from`, `constrained-by`, `implements`, `verified-by`, or `invalidates-on-change`. Do not use a vague “owns” relationship where the actual requirement is authority or derivation.

The task graph schedules the work needed to produce and update these artifacts. The context-composition graph selects the skills and integrations needed for each task. A separate profile is optional.

### Design rule

**Put authority in explicit artifacts and validation relationships, not in a role name.**

The project must be able to tell a fresh context which contract is in force, what it governs, what has been verified, and what needs reevaluation after a change.

## 5. Validation obligations

1. Select a representative domain concept (for example, a reservation) and trace it through requirement, UI, schema, API, implementation, and tests.
2. Change an upstream field or rule and identify every artifact and test that should be invalidated.
3. Verify whether the workflow actually surfaces those invalidations.
4. Confirm that no downstream implementation silently invents a divergent field or rule.
5. Start a fresh context and ask it to identify the authoritative artifacts and current status using repository records alone.
6. Test whether the same result can be achieved with a composed context rather than an architect-specific profile.

## 6. Unresolved questions

- Which representation is authoritative for domain rules versus UI labels and layout?
- Is the Penpot design a source of product requirements, a visual artifact, or both? Which fields may legitimately be inferred from it?
- How are changes propagated through the task system in practice?
- What level of schema/API generation is mechanical versus interpretive?
- Which consistency checks can be automated?
- What is the minimum viable contract/provenance metadata that provides reliable continuity without excess ceremony?

## 7. Disposition

**Provisional finding:** The source-of-truth problem should be handled as an explicit graph of authority, derivation, and verification. The architect profile is one possible execution arrangement, not the source of authority itself.

**Next step:** Compare with O-03's repository layout and with the later observer/continuity material. Determine which state belongs in the project repository and which belongs in the Hermes collection.
