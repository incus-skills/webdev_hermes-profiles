# Critique chunk P-03 — Runtime, integrations, and deployment boundaries

**Status:** Initial critique; provisional.  
**Observer position:** Distinguish capability composition from infrastructure/runtime topology.  
**Source chunk:** `sources/penpot-session/penpot_hermes.md`, lines 335–452.  
**Comparative anchor:** Zephyr shared resources and profile topology, introduced after the original exploration.

## 1. Reconstruction

This chunk describes a proposed deployment for Penpot, MCP integration, Hermes runtime, terminal backends, model backends, and a single-host stack. It compares MCP options by transport and browser requirements, proposes a headless integration for one use case, and recommends Docker as a runtime backend for the proposed pipeline.

The chunk also draws a deployment diagram in which several profiles access the same Penpot MCP endpoint, while some profiles use browser automation. This is relevant to the current critique because it depicts shared integration resources across profile boundaries.

**Evidence limit:** The recommendations, versions, costs, and compatibility statements are source-era claims and have not been verified here as current. They must not be copied directly into an operational collection without current compatibility, security, and deployment checks. The Markdown does not establish that the complete stack was deployed and tested.

## 2. Challenge

The source describes several distinct boundaries that should not be conflated:

- **Infrastructure boundary:** containers, networks, databases, and deployment hosts.
- **Integration boundary:** MCP servers and the tools they expose.
- **Execution boundary:** local, Docker, SSH, or other terminal/runtime backends.
- **Inference boundary:** remote or local model providers.
- **Context boundary:** which instructions and capabilities are available in a given Hermes context.

Changing one boundary does not necessarily require changing the others. For example, several context compositions may use the same MCP endpoint; a task may use a containerized terminal without requiring a separate profile; and model selection is not itself a capability taxonomy.

Challenge the assumption that a profile-specific configuration implies profile-owned infrastructure. Also challenge the opposite assumption: a shared MCP endpoint is not automatically safe or suitable for every context. Credentials, permissions, network reachability, and concurrent use may require access boundaries.

## 3. Response

The user's composition model implies that infrastructure and integrations should be represented as reusable resources with explicit requirements and access conditions. Profiles may refer to these resources; they need not own or duplicate them.

A resource being shared should mean that its availability and access policy are intentionally managed—not that every context receives every credential or tool. Composition is bounded by least privilege, tool compatibility, context relevance, and runtime constraints.

## 4. Critique — new cognitive structure

Represent runtime as a **resource/dependency graph**, separate from both the capability graph and task graph.

Resource nodes may include:
- MCP endpoints and their exposed tools.
- Browser automation and terminal access.
- Filesystem/workspace access.
- Container/runtime backends.
- Model/inference providers.
- Shared project artifacts and credentials/secret references.

Edges should express `requires`, `connects-to`, `runs-in`, `reads/writes`, `authenticates-with`, and `permitted-for`.

A context composition can declare which resources it needs and which operations it is permitted to perform. This allows reuse without assuming universal access.

### Design rule

**Share resource definitions and integrations where useful; scope access and activation to the task's actual needs.**

This rule prevents two opposite errors: duplicating integrations just because profiles are separate, and exposing every integration to every context just because resources can be shared.

## 5. Validation obligations

Before operational use:
1. Verify the current supported Hermes configuration for the required MCP and runtime backends.
2. Verify each integration's maintenance status, transport, authentication, permission model, and security posture.
3. Test reachability from the intended runtime, including container DNS/network assumptions.
4. Check that the minimum required capabilities can use the integration without granting unrelated access.
5. Test failure behavior: unavailable MCP, expired credentials, browser not present, runtime unavailable, or model endpoint unreachable.
6. Record versions and tested combinations; do not treat source-era versions or pricing as current facts.

## 6. Unresolved questions

- Which MCP integration is currently supported and appropriate for the intended Penpot setup?
- Which capabilities actually require browser access versus direct MCP/API access?
- Which tool permissions should be shared, and which should be scoped?
- What is the minimum viable local or remote runtime for the first profile experiment?
- Which parts of this deployment are examples, and which are requirements of the web-development collection?

## 7. Disposition

**Provisional finding:** The source already shows multiple profiles sharing one integration endpoint. This supports separating integration/resource identity from context identity. The infrastructure choices themselves remain unverified examples, not collection defaults.

**Next step:** Examine P-04 and O-02 for how durable project state, contracts, and task dependencies interact with shared resources.
