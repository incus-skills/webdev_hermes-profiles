# P-05 critique — affordance contracts and the intent layer

**Source:** `sources/penpot-session/penpot_hermes.md`, lines 621–746.  
**Source artifact SHA at review:** `bdddff4292a515703d41251a912a06ef3aed9149`.  
**Status:** Interpretive critique of a compiled source section; not a reconstructed turn or proof of implementation.

## What this section proposes

The section moves from pipeline decomposition toward an affordance-centric design. Payload, Penpot, and scraper ingress each expose a capability surface and a proposed swap boundary. The Hermes/Kanban/profile layer is framed as an intent-and-structure layer that should depend on contracts rather than implementation details. It proposes keeping contracts in a repository-level `contracts/` directory and having an architect consume them while a critic validates the running system against them.

The intended benefit is reduced coupling: replacing one implementation should require changing an adapter or its contract, not rewriting the whole task pipeline.

## Distinctions to preserve

1. **Affordance vs. implementation:** a capability available to the system is not identical to the component that currently supplies it.
2. **Contract vs. adapter:** a contract states required behaviour or shape; an adapter maps a concrete implementation onto that requirement.
3. **Intent vs. execution:** the project/task layer can state what is required without owning every mechanism that realizes it.
4. **Stable requirement vs. vendor interface:** a stable domain-facing requirement need not have the same shape as a particular provider's API or MCP tools.
5. **Replaceability vs. name compatibility:** two implementations exposing similarly named operations are not thereby semantically interchangeable.

These distinctions are the strongest contribution of the section. They give the later profile design something more durable than a list of tools or a fixed vendor stack.

## Observer-position critique

The source says, in effect, that contracts are data shapes and MCP tool names, and suggests that a replacement can preserve the same tool names. This is useful as an implementation tactic, but it is too weak as the general definition of a contract.

- **Names do not establish semantics.** Two tools named `find` can differ in filtering, pagination, authorization, error behaviour, consistency, or side effects.
- **Shapes do not establish invariants.** Matching a JSON or table schema does not prove that merge semantics, identity rules, ordering, null handling, or lifecycle constraints are equivalent.
- **The proposed swap boundaries are uneven.** A CMS API, a design-tool operation, and a scraper staging table are different kinds of affordance. They may need different contract forms and conformance tests rather than one uniform “tool names + data shapes” model.
- **The contract may leak implementation.** If a contract directly codifies a provider-specific endpoint or MCP method, it may preserve the dependency the abstraction was intended to remove. That can be justified, but it should be named as an adapter-facing contract rather than a provider-neutral requirement.
- **Replaceability has a cost boundary.** A substitute may satisfy the domain-facing contract while requiring a new adapter, migration, capability downgrade, or operational change. “The profiles do not change” is a design goal, not an automatic consequence.

The claim that a provider's Local API is the “ultimate contract boundary” also needs narrowing: in-process invocation can reduce transport overhead, but it does not by itself create a provider-neutral contract. Likewise, the section's specific Payload versions, feature status, and tool availability are time-sensitive source-era claims; they should be independently checked before becoming operational guidance.

## What the model should carry forward

Treat an affordance contract as a testable statement of **required capability, inputs, outputs, semantics, failure modes, permissions, and relevant invariants**. Keep provider-specific details in an adapter contract or implementation binding. Require conformance evidence before declaring a replacement compatible.

The intent layer should depend on the narrowest useful capability contract, not necessarily on raw provider tool names. Where exact provider tools are intentionally part of the requirement, record that coupling explicitly.

## Questions left open

- Which contracts are domain-level and which are adapter/provider-level?
- What evidence is sufficient to claim two implementations conform?
- How are capability absence, degraded substitutes, and non-equivalent replacements represented?
- Who owns contract versioning and migration when a capability changes?
- How are security and authorization semantics represented alongside functional affordances?

## Provisional conclusion

P-05 contributes the idea of explicit affordance boundaries and a project-level intent layer. The critique does not reject that direction; it separates the useful abstraction from the stronger, unsupported inference that stable names and shapes alone make implementations swappable.