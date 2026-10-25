# P-06 critique — runtime boundaries and workflow continuity

**Source:** `sources/penpot-session/penpot_hermes.md`, lines 747–903.  
**Source artifact SHA at review:** `bdddff4292a515703d41251a912a06ef3aed9149`.  
**Status:** Interpretive critique of a compiled source section; not a reconstructed turn or proof of implementation.

## What this section proposes

P-06 extends the affordance-contract idea to Forgejo and the execution environment. Hermes runs with a local terminal inside an Incus system container; Git carries the codebase; Forgejo's API carries issue and pull-request workflow; Forgejo Actions runs mechanical checks; and the critic supplies judgment and a merge recommendation. Profiles are adapted to work with Git, files, browser automation, Penpot, and Payload.

The section's intended architecture separates the place where code is executed from the durable location where project artifacts are versioned. It also proposes a portable boundary: change the execution host or Forgejo-like service without rewriting all profiles.

## Distinctions to preserve

1. **Execution boundary vs. agent context:** a container, VM, process, or host constrains execution; a Hermes profile changes available instructions and capabilities. Neither implies the other.
2. **Ephemeral compute vs. durable state:** a disposable environment is safe only when every state element that matters is persisted or reproducible.
3. **Source-of-truth repository vs. working copy:** the remote repository preserves versioned artifacts; a local checkout is a mutable execution workspace.
4. **Mechanical verification vs. evaluative judgment:** CI can execute defined checks; a critic may interpret evidence and assess requirements not fully captured by automated tests.
5. **Protocol compatibility vs. workflow equivalence:** Git compatibility or partial REST/API similarity does not prove equivalent issue semantics, permissions, webhooks, Actions behavior, or governance.
6. **Tool availability vs. authorized use:** a profile having a token or terminal command does not mean every action should be permitted in every task.

## Observer-position critique

### 1. The container is an execution boundary, not a complete safety proof

Running Hermes inside an Incus system container can avoid unnecessary nested-container complexity. It does not, by itself, establish that the environment is appropriately isolated. Effective boundaries also depend on host configuration, device mounts, privileges, network access, filesystem mounts, credentials, and the container's trust level. The correct conclusion is conditional: use the container as the intended boundary, then verify its actual permissions and exposure.

The source also calls the container ephemeral while proposing persistence for `~/.hermes/`. These can coexist, but only if the persistence model is explicit: which paths are durable, which are reproducible, which contain secrets, and what recovery procedure restores them.

### 2. The runtime map contains unresolved endpoint ambiguity

The source's network sketch assigns `localhost:3000` to Penpot MCP and also to Payload MCP / Next.js. Unless these are separate network namespaces, distinct bind addresses, or different paths behind a proxy, this is ambiguous and may be an actual port collision. It should remain an unresolved source issue until the intended topology is specified, not be silently repaired in the critique.

### 3. Service substitution is more than changing a hostname and token

Forgejo, Gitea, and Codeberg may share Git and API conventions, but operational equivalence depends on endpoint coverage, authentication scope, pagination, labels, pull-request behavior, webhook events, branch protection, runner semantics, artifact handling, and version differences. Actions compatibility is especially not guaranteed solely by shared syntax. Treat the service-specific integration as an adapter whose conformance must be tested.

Likewise, replacing Incus with a VM or bare metal changes more than provisioning if filesystem paths, network names, service discovery, privilege assumptions, or persistent volumes are coupled to the environment. A portability claim should identify the invariants preserved and the bindings that change.

### 4. The CI/critic split needs explicit evidence exchange

The useful distinction is that automation runs reproducible checks while a critic interprets their relevance. But “both satisfied” requires an actual gate protocol: which check results are mandatory, which review findings block, how results are tied to a commit, how waivers work, and what invalidates prior approval. A critic score is not a substitute for an acceptance contract, and a passing test suite is not proof of visual or domain correctness.

The source's workflow examples use action names and versions that may be platform- or time-dependent. Treat them as historical design examples, not current operational instructions without verification.

### 5. Credentials and capabilities need least-privilege boundaries

A shared `FORGEJO_TOKEN` environment variable is a convenient sketch, not a complete secret-management design. The system should specify which contexts can read credentials, token scopes, rotation, log redaction, branch/merge permissions, and whether untrusted code or pull-request jobs can access secrets. Shared integrations are useful, but resource sharing must not erase access control.

### 6. Profile configuration does not prove execution isolation

A builder profile with terminal, file, browser, and MCP access is a capability bundle. It does not establish that these capabilities execute in separate security domains. Conversely, separate profiles do not automatically provide independent credentials, filesystems, or processes. Execution isolation must be described and verified at the runtime/resource layer.

## What the model should carry forward

Represent the runtime/resource graph explicitly:

- execution target and trust boundary;
- filesystem and persistence mounts;
- service endpoints and network namespaces;
- credential references, scopes, and allowed consumers;
- Git remote, branch, commit, and review state;
- CI runner identity, workflow version, result, and artifact references;
- permitted side effects and merge authority.

Bind each review result to a specific commit and relevant evidence. Keep the critic's interpretation separate from the mechanical result, and keep both separate from the authorization to merge.

## Questions left open

- What exactly persists when the container is destroyed, and how is it recovered?
- Which paths and credentials are exposed to each profile and CI job?
- What is the intended service topology where multiple services are assigned the same localhost port?
- Which Forgejo/Gitea/Codeberg capabilities are part of the required contract, and which are optional?
- What commit-specific evidence is required before a PR can be approved or merged?
- Which runtime changes require a contract update versus only a provisioning change?

## Provisional conclusion

P-06 contributes a useful distinction between agent workflow, source control, CI verification, and execution substrate. The critique narrows its portability and safety claims: a boundary is real only when the actual permissions, persistence, endpoints, and evidence flow are explicit and tested.