# Critique chunk P-02 — Skill installation, profile composition, and task activation

**Status:** Initial critique; provisional.  
**Observer position:** Capability composition and context-size economics.  
**Source chunk:** `sources/penpot-session/penpot_hermes.md`, lines 148–334.  
**Comparative anchor:** [Zephyr Hermes profiles](https://github.com/incus-skills/zephyr_hermes-profiles), particularly its shared-resource relationships.  
**Precondition:** The Zephyr homologue became available after the original Penpot exploration. This critique does not retroactively attribute that reference to the source session.

## 1. Reconstruction — what the source proposes

The chunk describes installing reusable skills, creating designer/builder/critic profiles with profile-specific instructions and always-loaded skills, routing a sequence of tasks through an orchestrator or Kanban, and pinning additional skills for individual tasks when needed.

It also explicitly acknowledges an alternative: using one profile with multiple skills and a constitution that routes behaviour by task type, potentially with fewer processes to manage.

That alternative is significant evidence. The source does not present strict profile separation as the only possible design; it records a lower-separation option.

The proposed profile topology associates:
- Design direction with a design-focused instruction set and the Impeccable skill.
- Frontend implementation with Taste and browser-check capabilities.
- Critique with both skill sets and inspection tools.
- Orchestration with task creation, dependency ordering, and routing rather than implementation.

The source also proposes that task-specific skill pinning can extend a profile's default set.

**Evidence limit:** These are proposals documented in the Markdown artifact. The artifact does not establish which configurations were actually installed, measured, or validated. It is not a turn-level transcript, so the order and acceptance status of individual ideas cannot be fully recovered.

## 2. Challenge — distinguish resource modularity from execution separation

The current observer position challenges the assumption that a skill's independent identity requires a separate profile or execution context.

The Zephyr homologue demonstrates a topology in which several profiles use shared skills and a shared MCP relationship. This supports the idea that reusable capabilities can be referenced by multiple context compositions. It does not establish that every Zephyr profile boundary is necessary or that its routing model should be copied.

For each proposed profile boundary, ask:

1. Is the separation needed to limit active context, or is it inherited from the label “designer”, “builder”, or “critic”?
2. Could the required skills and toolsets coexist in one context without creating conflicting instructions?
3. Can a single context perform a bounded sequence—design, implement, inspect—while loading only the necessary capabilities for each phase?
4. Does an isolated review context measurably improve independence or quality, or does it mainly add routing and state-transfer overhead?
5. Can task dependencies be represented without assigning every task to a distinct profile?
6. What durable state must cross a task boundary, and can it be carried by shared project artifacts rather than duplicated handoff prose?

The challenge is not “never separate profiles”. It is “justify every execution boundary by an operational benefit”.

## 3. Response — reconcile the source's own alternative with current constraints

The source's one-profile/multiple-skills alternative should be retained as a real candidate design, not treated as a footnote.

The user has clarified that profiles compartmentalize associated skills and behaviours for context-size management and task subdivision, not to instantiate a virtual workforce. Skills, plugins, MCPs, and shared resources may be composed in the same context. Strict role separation can inflate routing and handoff instructions.

Under those constraints:
- A profile can be a reusable context preset, not a worker identity.
- A skill can be separately reusable without requiring a separate profile.
- An orchestrated task sequence does not imply a separate profile per task.
- A review procedure can be distinct without necessarily requiring an isolated execution context.
- Task-specific skill pinning is a possible composition mechanism, subject to actual Hermes support and configuration validation.

## 4. Critique — new cognitive structure

Model the proposal as two graphs rather than one role topology.

### Capability availability graph

Nodes represent skills, plugins, MCP integrations, toolsets, shared resources, and behavioural instructions. Edges express relationships such as requires, uses, conflicts with, duplicates, or composes with.

### Context-composition graph

Nodes represent viable sets of capabilities that can be made active together. Edges express loads, adds, removes, or switches composition.

A separate task graph expresses dependencies and deliverables. It should not be conflated with either graph.

This three-graph model makes the following distinctions explicit:

- **Reusable resource:** independently addressable, versionable, and shareable.
- **Context composition:** a selected set of resources and instructions for a task class.
- **Task subdivision:** decomposition based on dependencies, context constraints, parallelism, or verification.
- **Execution isolation:** a separate runtime/context, justified only when its benefit outweighs the handoff and continuity costs.

### Design rule

**Separate capability packaging from profile composition, and separate profile composition from task decomposition.**

This is the key structure emerging from the critique of P-02.

## 5. Validation obligations

Compare at least two implementations of the same representative task:

- **Composition A:** one context with the minimum sufficient skills and integrations available, with behaviour switching explicitly described where needed.
- **Composition B:** multiple profiles or contexts, with the same underlying capabilities and equivalent project state.

Measure or inspect:
- Active instruction/context volume where measurable.
- Duplicated instructions and handoff text.
- Number of context transitions and information that must be re-transmitted.
- Task correctness and ability to use required tools.
- Review quality and independence where review is involved.
- Recovery from a failed step or changed requirement.

Do not infer that Composition A is better simply because it has fewer profiles; it may accumulate conflicting instructions or too much context. Do not infer that Composition B is better because its boundaries appear tidy.

## 6. Unresolved questions

- What does the target Hermes version actually support for per-task skill activation, profile-specific configuration, and shared skill paths?
- Which skills in the source overlap in purpose or instructions?
- What are the real context and tool availability costs of each composition?
- Is an independent critic context required by the quality target, or is a separate review pass sufficient?
- Which task boundaries are imposed by genuine dependencies rather than the role taxonomy?

These require current implementation checks or experiments before operationalizing the architecture.

## 7. Disposition

**Provisional finding:** The source already contains a lower-separation alternative. The critique strengthens that alternative into a general design principle: reusable capabilities, context composition, task decomposition, and execution isolation are separate dimensions.

**Next step:** Compare this finding with O-01's conversion of a one-shot prompt into contracts and a task graph. Do not yet decide the final number or names of web-development profiles.
