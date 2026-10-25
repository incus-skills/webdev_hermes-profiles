# Critique chunk P-01 — Penpot design-to-code capability boundaries

**Status:** Initial critique; provisional, source-grounded where marked.  
**Observer position:** Context composition and task-boundary economics, informed by the subsequently introduced Zephyr homologue.  
**Source chunk:** `sources/penpot-session/penpot_hermes.md`, lines 1–147.  
**Related comparison:** `sources/penpot-session/penpot_hermes_oneshot-conversion.md`, lines 1–118 (separate comparison case).  
**Homologue:** [incus-skills/zephyr_hermes-profiles](https://github.com/incus-skills/zephyr_hermes-profiles), especially its README sections on profile topology and shared resource relationships.

## 1. Reconstruction — what does P-01 contain?

### Source-supported observations

P-01 proposes a design-to-code pipeline in which:

- A designer works directly in the Penpot design file through an MCP.
- A builder reads structured design information from that file and produces code.
- A critic inspects the design and rendered output.
- The proposed configurations attach different skill combinations and toolsets to those named profiles.
- The document contrasts two Penpot MCP approaches with different runtime constraints, including a browser/plugin-mediated option and a self-hosted server option.
- The Penpot AI Kit is presented as an additional source of skills and workflows.

The core technical distinction is that the Penpot file can act as a structured shared artifact between design and implementation, rather than requiring all design intent to be transmitted as a prose specification.

### Structural interpretation

The proposal combines three different kinds of boundary:

1. **Capability boundaries:** design manipulation, code generation, and evaluation.
2. **Resource boundaries:** which skills, MCP tools, and toolsets are available.
3. **Execution/context boundaries:** whether those capabilities run in separate profiles and require a handoff.

The first two do not logically require the third. The source's proposed profile assignments are one possible realization, not proof that separate execution contexts are necessary for each capability.

### Evidence limit

This Markdown artifact is not a turn-level transcript. The section supports reconstruction of the proposal, but does not establish the exact prompt that introduced each distinction, whether every proposed configuration was executed, or which parts were explicitly accepted at that point in the original session.

## 2. Challenge — examine the boundaries from another observer position

**Challenge question:** Which of the proposed designer/builder/critic separations are necessary capability distinctions, and which are optional context boundaries?

The original exploration did not have the Zephyr collection as a homologue. Its role-labelled architecture should therefore not be judged as though that comparison had been available then.

The subsequent homologue provides a concrete example of both named profiles and shared resources: Zephyr describes several profiles that consume common skills, and a shared MCP relationship. This demonstrates that capability reuse can cross profile boundaries. It does not prove that Zephyr's own role separation is optimal or that web development should copy it.

The current design constraint adds a stronger challenge: Hermes profiles are for compartmentalizing associated skills and behaviours to manage context size and subdivide tasks, not for modelling a virtual workforce. Strict role separation can create routing, handoff, state-transfer, and reconciliation instructions where capabilities could instead coexist in one context.

Therefore ask, for each boundary:

- Is there a reusable capability here, or only a label for a stage of work?
- Must this capability be isolated, or can its skill and tools be composed with others in the same context?
- Can the Penpot file and shared resources carry the necessary state without a role-to-role handoff?
- Does a separate context provide a measurable benefit in context size, task clarity, isolation, or verification?
- Is the critic's independent evaluation a real isolation requirement, or can it be represented as a bounded review procedure in a composed context? This must be tested, not assumed either way.

## 3. Response — current clarified constraints

The user has clarified that:

- Profiles compartmentalize skills and behaviours for context-size management and task subdivision; they are not virtual workers.
- Skills, plugins, and MCPs should be composable through shared resources or inclusion in the same context.
- Strict role separation can inflate routing and handoff instructions.
- The Zephyr collection became available only after the original Penpot exploration and is a comparative homologue, not a retroactive correctness test.

These are constraints on the present critique and design process. They are not retroactive claims about the original session's available context.

## 4. Critique — new cognitive structure

The initial structure should not be a single pipeline of worker roles. Model the design space using three separate but related structures:

### A. Capability/resource graph

Represent capabilities and their dependencies independently of execution context:

- Capabilities: design-file inspection/editing, code generation, visual comparison, accessibility checks, tests.
- Resources: skills, MCP servers, plugins, browser/file/terminal toolsets, shared references, the Penpot file.
- Edges: uses, requires, composes with, reads from, writes to, validates.

A capability can be independently reusable without being assigned an isolated profile.

### B. Context compositions

Treat a profile as a reusable context composition or loading preset: a selected set of instructions, skills, behaviours, and tool availability appropriate to a class of tasks.

A profile boundary is justified when it reduces irrelevant context or provides another concrete operational benefit. It is not automatically a worker boundary, ownership boundary, or handoff boundary.

### C. Task decomposition graph

Represent task subdivision separately from profile composition. A task can be split because it has meaningful dependencies, a context budget limit, parallelizable work, or a verification boundary. Each resulting task can select the context composition it needs. Multiple tasks may use the same composition; one task may compose several capabilities without splitting into separate profiles.

### Resulting design rule

**Keep capabilities independently addressable; compose them in context as needed; subdivide tasks only when the task or execution constraints justify it.**

Handoffs should describe only information that must actually cross a context or task boundary. Shared artifacts and references should carry durable state where appropriate, rather than duplicating that state in role-specific instructions.

## 5. Design implications (not yet profile decisions)

1. Keep the Penpot design file as a shared structured artifact between design inspection, implementation, and review.
2. Model the Penpot MCP and other integrations as resources that may be available to more than one context composition, subject to actual configuration and security constraints.
3. Treat visual review as a reusable capability/procedure; decide later whether it warrants an isolated context based on evidence and validation.
4. Avoid separate designer/builder/critic profiles solely because the work can be named as three stages.
5. Evaluate at least two compositions during validation: one context with the necessary capabilities co-loaded, and a compartmentalized composition. Compare context overhead, instruction duplication, fidelity, task clarity, and review quality.

## 6. Unresolved questions

- What are the actual Hermes runtime costs and constraints for loading these skills and MCPs together?
- Which Penpot MCP option is currently maintained, compatible, and suitable for the target deployment? The source's recommendations need independent current validation before becoming operational guidance.
- Does visual review need isolation for independence or quality, or can an explicit review pass provide sufficient separation?
- Which of the proposed skills are distinct reusable capabilities versus overlapping packages of related instructions?
- What is the smallest sufficient composition for a design-to-code task in a fresh Hermes instance?

Do not convert these questions into settled architecture without tests or further evidence.

## 7. Disposition

**Provisional finding:** P-01's shared-artifact concept is structurally useful. Its role labels should be retained as descriptions of capability phases where helpful, but not treated as evidence that separate worker-like profiles are required.

**Next step:** Process P-02 and O-01 independently, then compare their capability activation and task-decomposition assumptions against this critique. Do not yet generalize P-01 into the complete web-development profile architecture.
