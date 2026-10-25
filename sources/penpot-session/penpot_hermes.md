# Penpot, Payload, Hermes

## Where Penpot sits in the pipeline

```
orchestrator
  ├── designer   (Penpot MCP + impeccable)  → works IN the Penpot file
  ├── builder    (Penpot MCP + taste-skill) → reads from Penpot, outputs code
  └── critic     (Penpot MCP + both skills) → inspects the Penpot file directly
```

The key shift: the designer no longer outputs a _text spec_ — it **creates and edits the actual Penpot design file** via MCP. The builder then reads structured data (tokens, layout, components) from that file rather than interpreting prose.

## Setup: two MCP options

**Option A — Official `@penpot/mcp`** (simpler, needs a browser tab):

The agent writes JS that executes inside Penpot's plugin context via WebSocket. Requires a browser tab with the file open.

```bash
npx -y @penpot/mcp@latest
# Connect: http://localhost:4401/mcp
```

For a headless Hermes agent, you'd need a Playwright/Puppeteer step to keep the Penpot tab alive:

```yaml
# designer/config.yaml
toolsets:
  - mcp
  - browser # keeps the Penpot plugin tab open
mcp_servers:
  - name: penpot
    url: http://localhost:4401/mcp
```

**Option B — `ancrz/penpot-mcp-server`** (self-hosted, 68 tools, no browser needed):

Runs as a Docker container beside your Penpot instance. Exposes tools over streamable HTTP at `localhost:8787/mcp`. No WebSocket/browser dependency — cleaner for headless agents.

```yaml
# designer/config.yaml
toolsets:
  - mcp
  - file
mcp_servers:
  - name: penpot
    url: http://localhost:8787/mcp
```

## Updated profile configs

**Designer** (creates the design in Penpot):

```yaml
skills:
  always_load:
    - impeccable
    - penpot-mcp # ar27111994/penpot-mcp skill
mcp_servers:
  - name: penpot
    url: http://localhost:8787/mcp
```

```markdown
# SOUL.md (designer, key additions)

You work directly in the Penpot file via MCP.

- Create frames, components, and token definitions in the file
- Use the dice roll to pick a visual direction, then build it
- Extract and enforce your design tokens as Penpot native tokens
- Hand off by marking the file "ready" — the builder reads from it
```

**Builder** (reads Penpot → generates code):

```yaml
skills:
  always_load:
    - taste-skill
    - penpot-mcp
mcp_servers:
  - name: penpot
    url: http://localhost:8787/mcp
toolsets:
  - terminal
  - file
  - browser # for the screenshot verification loop
```

```markdown
# SOUL.md (builder, key additions)

Read the design from the Penpot file:

- Extract tokens (colors, type scale, spacing) via MCP
- Read layout structure and component hierarchy
- Generate code that matches the Penpot file exactly
- Verify with browser screenshot against the Penpot export
```

**Critic** (inspects both the Penpot file and the rendered output):

```yaml
skills:
  always_load:
    - impeccable
    - taste-skill
    - penpot-mcp
mcp_servers:
  - name: penpot
    url: http://localhost:8787/mcp
toolsets:
  - browser # screenshot the rendered output
  - file
```

```markdown
# SOUL.md (critic, key additions)

Two-pass review:

1. Read the Penpot file — check token consistency, component usage,
   layout structure. Flag any drift from the design system.
2. Screenshot the rendered code — compare against the Penpot export.
   Score on 7 axes. Block with specific notes if < threshold.
```

## The Penpot AI Kit as a bonus layer

The official [penpot-ai-kit](https://github.com/penpot/penpot-ai-kit) is a full layered package (13 skills, 7 orchestration workflows, policies, evals) built on Anthropic Agent Skills + MCP. You can drop its `skills/` directory into your Hermes skills path and get Penpot-specific capabilities (prototyping workflows, design system audits, token migration, drift detection) without writing them yourself:

```bash
git clone https://github.com/penpot/penpot-ai-kit ~/.hermes/skills/creative/penpot-ai-kit
```

Its `workflows/` directory has orchestration recipes (pipeline.json) that map almost 1:1 onto the kanban parent/child chain — you could use them as the template for your orchestrator's task decomposition.

## Practical constraint to plan for

The official MCP server's architecture (plugin → WebSocket → browser tab) means **someone or something needs to keep a Penpot file open in a browser**. For a fully headless Hermes setup, the `ancrz/penpot-mcp-server` Docker approach is the cleaner path — it talks directly to the Penpot backend API without the browser plugin intermediary. If you're already self-hosting Penpot (which you'd want for data sovereignty anyway), this is a one-container add.

---

The integration is straightforward because both taste skills are already `SKILL.md`-format and Hermes natively supports per-profile skill loading and per-task skill pinning. Here's the architecture:

## 1. Install the skills

Drop them into your skills directory (they're already in the right format):

```bash
mkdir -p ~/.hermes/skills/creative/
git clone https://github.com/pbakaus/impeccable ~/.hermes/skills/creative/impeccable
git clone https://github.com/leonxlnx/taste-skill ~/.hermes/skills/creative/taste-skill
```

Or use a skills tap if they publish one:

```bash
hermes skills tap add pbakaus/impeccable
hermes skills tap add leonxlnx/taste-skill
```

## 2. Create role-specific profiles

Each profile gets a `SOUL.md` defining its role, plus **always-load skills** in its `config.yaml`:

```bash
hermes profile create designer --clone
hermes profile create builder --clone
hermes profile create critic --clone
```

**`~/.hermes/profiles/designer/SOUL.md`:**

```markdown
# Soul

personality: Design director
tone: Opinionated, precise, allergic to generic output
---

You are the design lead. You own visual direction, layout decisions,
and the anti-slop gate. When building UI, you MUST apply the
impeccable skill's 4 modes and dice-based direction selection.
You produce design specs (not code) and hand off to the builder.

## Rules

- Always run the dice roll to pick a visual direction before starting
- Apply the 62 detector rules as a pre-flight check
- The brief outranks your own taste — if the user pinned an aesthetic, follow it
- Output: a structured design spec (layout, type scale, palette, section order)
```

**`~/.hermes/profiles/designer/config.yaml`** (key part):

```yaml
skills:
  always_load:
    - impeccable
```

**`~/.hermes/profiles/builder/SOUL.md`:**

```markdown
# Soul

personality: Frontend engineer with an eye
tone: Practical, visual, detail-oriented
---

You are the frontend builder. You take design specs and produce
production-ready code. You MUST apply the taste-skill's reference
library and browser check loop.

## Rules

- Pull real reference images from the taste-skill library, not placeholders
- After building, run the browser check: screenshot → compare → fix
- Enforce the no-slop house style as a hard gate before completing
- Output: working code in $HERMES_KANBAN_WORKSPACE
```

**`~/.hermes/profiles/builder/config.yaml`:**

```yaml
skills:
  always_load:
    - taste-skill
toolsets:
  - terminal
  - file
  - browser # for the screenshot loop
```

**`~/.hermes/profiles/critic/SOUL.md`:**

```markdown
# Soul

personality: Art director / QA
tone: Blunt, specific, evidence-based
---

You are the design critic. You review completed work against
impeccable's detector rules AND taste-skill's reference standards.
You do NOT fix things — you file specific, actionable feedback.

## Rules

- Score on 7 axes: hierarchy, composition, type, color, spacing, content, distinctiveness
- Flag any detector rule violations by name
- Compare against the reference direction the designer chose
- If score < threshold, block the task with specific revision notes
- If score ≥ threshold, complete with "approved"
```

**`~/.hermes/profiles/critic/config.yaml`:**

```yaml
skills:
  always_load:
    - impeccable
    - taste-skill
toolsets:
  - file
  - browser # to screenshot and inspect
```

## 3. The orchestrator routes the pipeline

Your orchestrator profile (or you, via CLI) creates a parent/child chain:

```python
# Orchestrator creates the pipeline
t_design = kanban_create(
    title="Design spec for landing page",
    assignee="designer",
    body="SaaS product, target: developers, tone: confident not loud"
)

t_build = kanban_create(
    title="Implement landing page from spec",
    assignee="builder",
    parents=[t_design],
    body="Use the design spec from parent handoff"
)

t_review = kanban_create(
    title="Design QA on landing page",
    assignee="critic",
    parents=[t_build],
    body="Score against impeccable detectors + taste-skill references. Block with notes if < 7/10."
)
```

The dependency chain means: designer completes → builder unblocks → builder completes → critic unblocks. If the critic blocks, the task goes back to the board and you (or the orchestrator) re-assign it to the builder with the critic's notes in the comment thread.

## 4. Per-task skill pinning (for one-offs)

If a task needs a skill the profile doesn't always load:

```python
kanban_create(
    title="Redesign the pricing table",
    assignee="builder",
    skills=["impeccable"],  # pin specifically for this task
    body="Pricing page, 3 tiers, annual/monthly toggle"
)
```

## 5. The practical shape

```
orchestrator (routes, doesn't build)
  ├── designer  (impeccable always-loaded) → outputs design spec
  ├── builder   (taste-skill always-loaded) → outputs code
  └── critic    (both always-loaded)       → scores or blocks
```

The key design choice: **the critic is a separate profile, not a step in the builder's loop.** This mirrors Penpot's Crew pattern (Critic is a distinct agent) and keeps the builder's context clean. The critic's blocking mechanism (`kanban_block(reason="...")`) is the feedback channel — the builder's next run sees the critic's notes in the task's comment thread via `kanban_show()`.

## What you'd want to tune

- **Model per profile** — the designer/critic can use your strongest reasoning model; the builder can use a model that's fast with tools.
- **Concurrency** — `hermes config set kanban.auto_decompose_per_tick 2` so you don't saturate your LLM endpoint if you're self-hosting.
- **The "split personality" alternative** — if 3 profiles feels heavy, one profile with both skills loaded and a `constitution.md` that routes based on task type works too (less clean, fewer processes to manage).

---

The full backend stack breaks into three layers: the **Penpot instance**, the **MCP server**, and the **Hermes agent runtime** (including model inference). Here's what's available for each:

## Penpot instance

The official Docker Compose stack is the path of least resistance — it's now **six containers** including a first-class MCP container:

| Container         | Image                       | Purpose                 |
| ----------------- | --------------------------- | ----------------------- |
| `penpot-frontend` | `penpotapp/frontend:latest` | Web UI (port 9001)      |
| `penpot-backend`  | `penpotapp/backend:latest`  | API + business logic    |
| `penpot-exporter` | `penpotapp/exporter:latest` | SVG/PNG export          |
| `penpot-mcp`      | `penpotapp/mcp:2.16`        | **Native MCP endpoint** |
| `penpot-postgres` | `postgres:15`               | Database                |
| `penpot-valkey`   | `valkey/valkey:8.1`         | WebSocket notifications |

Key point: **the MCP server is now an official Penpot container**, not a third-party add-on. It ships in the same compose file and talks to the backend over the internal Docker network. You just add `enable-mcp` to `PENPOT_FLAGS`.

**Deployment options ranked by ops burden:**

1. **Docker Compose** — single `docker compose up -d` on any Linux box. Recommended: Hetzner CX22 (2 vCPU, 4 GB RAM, ~€4/mo) handles the whole stack plus Hermes.
2. **Kubernetes / Helm** — official chart, better for HA or if you already run a cluster.
3. **Elestio** — managed, dedicated instance, no ops. Costs more but zero maintenance.
4. **TrueNAS** — if you're already running a NAS, there's an official app.

## MCP server (the bridge between Hermes and Penpot)

You now have three viable options:

| Option                          | Transport              | Browser needed?                 | Best for                                   |
| ------------------------------- | ---------------------- | ------------------------------- | ------------------------------------------ |
| **`penpotapp/mcp`** (official)  | WebSocket via plugin   | Yes (a Penpot tab must be open) | Interactive use, quick prototyping         |
| **`zcube/penpot-mcp-server`**   | HTTP/SSE (port 3000)   | **No**                          | Headless Hermes agents                     |
| **`sebathi/penpot-mcp-docker`** | Multi-port (4401–4403) | No                              | Version-pinned to specific Penpot releases |

For your Hermes pipeline, **`zcube/penpot-mcp-server`** is the cleanest headless option:

```yaml
# Add to your existing Penpot compose file
penpot-mcp-zcube:
  image: ghcr.io/zcube/penpot-mcp-server:latest
  restart: always
  environment:
    - TRANSPORT=http
    - HTTP_PORT=3000
    - PENPOT_API_URL=http://penpot-frontend:8080
    - PENPOT_ACCESS_TOKEN=${PENPOT_API_TOKEN}
  ports:
    - "3000:3000"
  networks:
    - penpot
```

Then point Hermes at `http://localhost:3000` in your profile configs. No browser, no WebSocket, no plugin tab to keep alive.

## Hermes agent runtime

**Terminal backends** (where the agent's shell commands execute):

| Backend          | Isolation                        | Best for                            |
| ---------------- | -------------------------------- | ----------------------------------- |
| `local`          | None                             | Dev machine, personal use           |
| `docker`         | Hardened container, dropped caps | **Recommended for production**      |
| `ssh`            | Remote host                      | Managing infra without local access |
| `modal`          | Serverless (Modal.com)           | Bursty workloads, no idle cost      |
| `daytona`        | Cloud sandboxes                  | Ephemeral build environments        |
| `vercel_sandbox` | Vercel edge                      | Frontend-specific tasks             |
| `singularity`    | HPC containers                   | GPU-heavy local inference           |

For your designer/builder/critic pipeline, **Docker** is the right call — the builder needs file system access and a browser (Playwright) for the screenshot loop, and Docker gives you isolation without the complexity of SSH or serverless.

**Model backends** (where inference happens):

| Backend                 | Cost                    | Latency | Best for                                 |
| ----------------------- | ----------------------- | ------- | ---------------------------------------- |
| **OpenRouter**          | Per-token (200+ models) | ~1–3s   | Flexibility, model switching per profile |
| **Anthropic API**       | Per-token               | ~1–2s   | Strongest reasoning (designer/critic)    |
| **Local vLLM / Ollama** | Free (your GPU)         | ~0.5–2s | Privacy, no egress, high volume          |
| **Nous Portal**         | Credit-based            | ~1–2s   | Native Hermes integration                |

Practical split for your three profiles:

- **Designer + Critic** → strongest reasoning model (Claude Sonnet via Anthropic, or a 70B local via vLLM)
- **Builder** → faster, tool-calling model (can be the same, or a cheaper one via OpenRouter)

## The full stack on one box

Everything fits on a **Hetzner CX22** (2 vCPU, 4 GB RAM, 40 GB SSD, ~€4/mo) or a **CX32** (4 vCPU, 8 GB, ~€8/mo) if you want headroom for the browser:

```
┌─────────────────────────────────────────────────┐
│  Hetzner CX22/CX32 (Ubuntu 24.04)              │
│                                                 │
│  Docker Compose:                                │
│  ├── penpot-frontend     (:9001)               │
│  ├── penpot-backend                           │
│  ├── penpot-exporter                          │
│  ├── penpot-mcp (zcube)    (:3000)            │
│  ├── penpot-postgres                          │
│  ├── penpot-valkey                            │
│  │                                            │
│  Hermes Agent (Docker backend):                │
│  ├── hermes-agent container                   │
│  │   ├── designer profile  ──→ MCP :3000     │
│  │   ├── builder profile   ──→ MCP :3000     │
│  │   │                     ──→ Playwright    │
│  │   └── critic profile    ──→ MCP :3000     │
│  │                         ──→ Playwright    │
│  │                                            │
│  Model inference:                              │
│  ├── OpenRouter API (external)                │
│  └── or local vLLM (needs GPU → CX32 + GPU)   │
└─────────────────────────────────────────────────┘
```

The one constraint: if you want **fully local inference** (no API calls), you need a GPU box (or a Modal/Daytona backend for the GPU workloads). For most people, hitting OpenRouter or Anthropic from a €4 VPS is the pragmatic choice — the model cost dwarfs the server cost anyway.

---

Exactly right — what we've built so far is a **design-to-frontend** pipeline. Real apps need a data layer, and the extension is straightforward because Hermes already supports MCP servers for live database access and skills for backend knowledge. The pipeline just gets more roles and a new "source of truth" in the middle.

## The extended pipeline

```
orchestrator
  ├── designer    (Penpot MCP + impeccable)     → visual design in Penpot
  ├── architect   (schema + API design)         → data model + API contract
  ├── backend     (DB MCP + backend skills)     → migrations, routes, business logic
  ├── builder     (Penpot MCP + taste-skill)    → frontend that calls the API
  └── critic      (both skills + browser)       → end-to-end QA
```

The critical addition is the **architect** role — it sits between design and implementation and produces the **data model + API contract** that both the backend and frontend derive from. Without it, the builder and backend engineer make independent assumptions about shape and you get integration drift.

## The architect profile

This is where the "source of truth" lives. It doesn't write code — it produces a **schema + API spec** (OpenAPI or just a structured doc) that downstream roles consume.

```yaml
# ~/.hermes/profiles/architect/config.yaml
skills:
  always_load:
    - supabase-postgres-best-practices # if using Supabase
    - api-scaffold # for OpenAPI generation
mcp_servers:
  - name: penpot
    url: http://localhost:3000/mcp # reads the design to infer data needs
  - name: supabase
    url: mcp.supabase.com # live DB access for schema validation
```

```markdown
# SOUL.md (architect)

You are the systems architect. You read the Penpot design and the
product brief, then produce:

1. A database schema (tables, relationships, indexes, RLS policies)
2. An API contract (endpoints, request/response shapes, auth requirements)
3. A data flow diagram (which UI screens read/write which tables)

You do NOT write implementation code. You produce the spec that
the backend and frontend roles build against.

## Rules

- Derive the schema from the design's data needs, not from a generic template
- Every form field in the Penpot file maps to a column
- Every list/table view maps to a query pattern (define indexes accordingly)
- RLS policies by default — assume multi-tenant
- Output: schema.sql + openapi.yaml in the kanban workspace
```

## Backend MCP servers (the "live database" layer)

This is what makes the backend role more than "write code in a file" — it can **query, migrate, and validate** against a real database:

| MCP Server                                                 | What it gives the agent                   | Transport                  |
| ---------------------------------------------------------- | ----------------------------------------- | -------------------------- |
| **Supabase MCP**                                           | Query, migrate, manage RLS, storage       | `mcp.supabase.com` (cloud) |
| **Postgres MCP** (`@modelcontextprotocol/server-postgres`) | Raw SQL, schema introspection, migrations | stdio (local)              |
| **Cloudflare D1 MCP**                                      | SQLite-based edge DB                      | HTTP                       |
| **Convex MCP**                                             | Reactive queries, vector search           | HTTP                       |

For a self-hosted setup (matching your Penpot self-hosting philosophy):

```yaml
# backend profile
mcp_servers:
  - name: postgres
    command: npx @modelcontextprotocol/server-postgres
    env:
      DATABASE_URL: postgres://user:pass@localhost:5432/appdb
```

The backend agent can then:

- Run `CREATE TABLE` migrations and verify they succeed
- Insert test data and query it back
- Check that RLS policies actually block unauthorized access
- Validate that the API contract matches the schema (no phantom columns)

## Backend builder profile

```yaml
# ~/.hermes/profiles/backend/config.yaml
skills:
  always_load:
    - supabase-postgres-best-practices
    - api-scaffold
mcp_servers:
  - name: postgres
    command: npx @modelcontextprotocol/server-postgres
    env:
      DATABASE_URL: postgres://user:pass@localhost:5432/appdb
toolsets:
  - terminal
  - file
```

```markdown
# SOUL.md (backend)

You are the backend engineer. You take the architect's schema + API
contract and produce working code.

## Rules

- Read the architect's handoff (schema.sql + openapi.yaml) from the parent task
- Generate migrations, route handlers, validation, and tests
- Run migrations against the live DB via MCP to verify they apply cleanly
- Seed test data so the frontend builder can develop against real responses
- The API contract is the contract — if you need to deviate, block and
  file a comment for the architect, don't silently change it
```

## The data flow through the kanban

```
Task 1: "Design the dashboard" → designer
  Output: Penpot file with frames, components, data labels

Task 2: "Define data model + API" → architect (parent: Task 1)
  Reads: Penpot file (infers what data each screen needs)
  Output: schema.sql + openapi.yaml + data-flow.md

Task 3: "Implement backend" → backend (parent: Task 2)
  Reads: schema.sql + openapi.yaml
  Output: migrations applied, API running on :8000, test data seeded

Task 4: "Build frontend" → builder (parent: Task 2, Task 3)
  Reads: Penpot file (visual) + openapi.yaml (data shape) + live API (real responses)
  Output: working frontend calling the real backend

Task 5: "E2E QA" → critic (parent: Task 4)
  Checks: visual fidelity to Penpot + data correctness + API contract compliance
```

## The "source of truth" problem

This is the actual hard part that the taste pipeline doesn't address. In a full-stack app, you have **three representations of the same data**:

1. **Penpot file** — visual layout, labels, form fields
2. **Database schema** — columns, types, constraints
3. **API contract** — request/response shapes

These three must agree, and drift between them is where full-stack agent pipelines break. The architect role exists specifically to be the **single point of derivation**:

```
Penpot design ──→ architect infers data needs ──→ schema
                                                    │
                                                    ▼
                                              API contract
                                                    │
                          ┌─────────────────────────┤
                          ▼                         ▼
                    backend builds            builder builds
                    (from schema)             (from design + contract)
```

If the designer changes a field in Penpot, the architect re-runs and the schema + contract update. The kanban dependency chain enforces this: the designer's task is the root, and everything downstream re-triggers if it changes.

## What doesn't change from the frontend pipeline

- **Penpot** is still the design surface — it's where the _product_ is defined visually
- **Taste skills** still gate the frontend output — they don't apply to SQL or API routes
- **The critic** still does the final visual + functional QA
- **The kanban dependency chain** is the same mechanism, just with more nodes

## Practical: where to start

If you're adding this to your existing setup, the minimum viable extension is:

1. Add a **Postgres container** to your existing compose file (or use Supabase free tier for dev)
2. Create the **architect profile** with the Postgres MCP server
3. Split your existing **builder** into **backend** + **frontend** (or keep one "builder" that does both if the app is small — the skill just needs the API contract in context)
4. Add the `supabase-postgres-best-practices` skill to the backend profile

The taste skills remain frontend-only. The backend quality gate is different — it's **schema correctness, RLS coverage, and API contract compliance**, not visual judgment. The critic handles both: visual axes for the frontend, structural axes for the backend.

---

https://github.com/payloadcms/payload

I intend to developin an affordance centric approach, the intent and structural definitions will be part of the hermes/kanban/profiles pipeline and the affordances provided by payload/penpot/scraper-data-ingress will be contract based to allow minimal refactoring i the even any one layer need to be swapped out due to functionality or licensing issues.

---

This is the right framing, and Payload's architecture is unusually well-suited to it. The plugin contract — "a function that receives a config and returns a modified config" — is already an affordance boundary. You're formalising what Payload does informally.

## The three affordance contracts

Each layer exposes a **capability surface** that the Hermes pipeline consumes, and a **swap boundary** that defines what you'd need to reimplement if that layer changes:

| Layer               | Affordance (what it provides)                          | Contract (what the pipeline depends on)                                           | Swap boundary                                                                  |
| ------------------- | ------------------------------------------------------ | --------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Payload**         | CRUD API, auth, file storage, admin UI, schema-as-code | REST/GraphQL endpoints + Local API (type-safe in-process) + MCP plugin (GA v3.78) | Replace with Strapi/Supabase: rewrite the adapter, keep the schema definitions |
| **Penpot**          | Design file, tokens, components, export                | MCP tools (create frame, read tokens, export SVG/PNG)                             | Replace with Figma: same MCP tool names, different backend                     |
| **Scraper ingress** | Cleaned, anonymised, keyed records                     | Staging table schema + merge/reaper semantics                                     | Replace crawler: same table contract, different source                         |

The key insight: **the contracts are the data shapes and the MCP tool names**, not the implementations. The Hermes profiles depend on:

- "I can `GET /api/jobs?filters[status][$eq]=active`" (Payload)
- "I can call `penpot.create_frame` / `penpot.get_tokens`" (Penpot MCP)
- "Rows appear in `jobs_staging` with these columns" (scraper)

None of those depend on _how_ the data gets there or _where_ the design file lives.

## Why Payload specifically fits this pattern

Three things make it the cleanest fit for affordance-based contracts:

**1. The Local API is the ultimate contract boundary.** It's a type-safe, in-process function call — zero network overhead, no HTTP layer to break. Your Hermes builder profile can call `payload.find({ collection: 'jobs', where: {...} })` directly. If you swap Payload for something else, you swap the import, not the query logic.

**2. `definePlugin` (v3.83) is a formal extension contract.** Execution ordering, cross-plugin discovery by slug, typed `RegisteredPlugins`. This means your "scraper ingress" can be a Payload plugin that registers its collection, hooks, and endpoints — and the rest of the system discovers it by slug. Swap the plugin, keep the slug, nothing else changes.

```typescript
// Your scraper ingress as a Payload plugin
export const scraperIngress = definePlugin({
  slug: "job-scraper-ingress",
  order: 10,
  plugin: (config) => ({
    ...config,
    collections: [...config.collections, JobCollection],
    // hooks, endpoints, etc.
  }),
});
```

**3. The MCP plugin (GA since v3.78) is the AI-agent contract.** Your Hermes profiles talk to Payload through MCP, not through raw HTTP or the Local API. The MCP tool names (`payload.find`, `payload.create`, `payload.update`) are the stable interface. If you swap the CMS, you swap the MCP server, not the agent profiles.

```yaml
# Hermes profile — depends on MCP tool names, not Payload specifically
mcp_servers:
  - name: cms
    url: http://localhost:3000/mcp # Payload MCP, or whatever replaces it
```

## The Hermes pipeline as the "intent layer"

Your architecture in one diagram:

```
┌─────────────────────────────────────────────────────────┐
│  HERMES / KANBAN / PROFILES  (intent + structure)       │
│                                                         │
│  orchestrator → designer → architect → backend          │
│                → builder → critic → crawler             │
│                                                         │
│  Defines: WHAT to build, in what order, with what       │
│  quality gates. Does NOT know HOW the data is stored    │
│  or WHERE the design file lives.                        │
└──────────────────────┬──────────────────────────────────┘
                       │
          ┌────────────┼────────────────┐
          ▼            ▼                ▼
   ┌────────────┐ ┌──────────┐  ┌──────────────┐
   │  PAYLOAD   │ │ PENPOT   │  │  SCRAPER     │
   │            │ │          │  │  INGRESS     │
   │ Affordance │ │Affordance│  │  Affordance  │
   │: CRUD API  │ │: design  │  │  : cleaned   │
   │  + auth +  │ │  file +  │  │  records in  │
   │  files +   │ │  tokens  │  │  staging     │
   │  admin UI  │ │  + export│  │  table       │
   │            │ │          │  │              │
   │ Contract:  │ │ Contract:│  │  Contract:   │
   │ REST/QL +  │ │ MCP tool │  │  column      │
   │ MCP tools  │ │ names    │  │  schema +    │
   │ + Local API│ │          │  │  merge rules │
   └────────────┘ └──────────┘  └──────────────┘
```

The Hermes profiles never import Payload, never call Penpot's API directly, never know which scraper wrote the data. They consume **contracts** through MCP tool names and API response shapes.

## Practical: where the contracts live

You'd want a single `contracts/` directory in your repo that the architect profile reads and the critic profile validates against:

```
contracts/
├── cms.yaml          # What the CMS must expose (endpoints, auth model, file storage)
├── design.yaml       # What the design tool must expose (MCP tools, export formats)
└── ingress.yaml      # What the data source must provide (column schema, key strategy, merge semantics)
```

The architect profile reads these to produce its schema + API spec. The critic profile validates that the running system actually satisfies them. If you swap Payload for Strapi, you update `cms.yaml` to match Strapi's API surface, and the pipeline adapts — the profiles don't change, only the adapter layer does.

## The one thing to watch

Payload v4 is in canary (requires Node 24.15, Next 16.4, TS 6.0.3) and brings breaking changes. For a new project in October 2026, you're in a window where v3.90 is stable and v4 is close. The affordance contract approach protects you here: if v4 changes the Local API or plugin interface, your Hermes profiles don't care — only the Payload adapter layer does. Pin to v3.90 for now, track v4, and the contract boundary means the migration is a single-layer change, not a pipeline-wide refactor.

---

This is a clean setup and it actually simplifies the terminal backend question. Since Hermes is _inside_ the Incus container (a full system container with its own init, filesystem, and network), the container **is** the sandbox — you don't need `terminal.backend: docker` on top of it.

## Terminal backend: `local`

```yaml
# ~/.hermes/config.yaml
terminal:
  backend: local
  cwd: /workspace/project
  timeout: 300
```

The Incus container provides the isolation boundary (namespaces, cgroups, its own network). Hermes runs natively inside it with full access to the local filesystem. No nested container, no socket mounting, no UID mapping issues. This is exactly the pattern the community recommended — a system container as the agent's home, not an OCI image.

## The Forgejo contract

Forgejo becomes a fourth affordance layer alongside Payload, Penpot, and the scraper ingress:

| Layer       | Affordance                                                         | Contract                                         | Swap boundary                                                     |
| ----------- | ------------------------------------------------------------------ | ------------------------------------------------ | ----------------------------------------------------------------- |
| **Forgejo** | Version control, issue tracking, PR review, webhooks, Actions (CI) | Git protocol (clone/push) + REST API (`/api/v1`) | Replace with Gitea/Codeberg: same API surface, different hostname |

The agent interacts with Forgejo two ways:

**1. Git (the code path):**

```bash
# Inside the Incus container, on first run or per task
git clone https://forgejo.local:3000/youruser/yourproject.git /workspace/project
# ... work ...
git push origin main
```

**2. Forgejo REST API (the workflow path):**

The agent can create issues, open PRs, comment, and trigger webhooks via the API. This is where the kanban-to-forgejo mapping lives:

```bash
# The orchestrator (or you) creates a Forgejo issue per kanban task
curl -X POST \
  -H "Authorization: token $FORGEJO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Implement landing page", "body": "From kanban task #42", "labels": ["builder"]}' \
  https://forgejo.local:3000/api/v1/repos/youruser/yourproject/issues

# The builder opens a PR when done
curl -X POST \
  -H "Authorization: token $FORGEJO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "feat: landing page from Penpot design", "head": "builder/landing-page", "base": "main"}' \
  https://forgejo.local:3000/api/v1/repos/youruser/yourproject/pulls
```

## The workflow loop

```
┌─────────────────────────────────────────────────────────────┐
│  Incus container (system container, persistent)             │
│                                                             │
│  hermes-agent (terminal.backend: local)                     │
│  ├── ~/.hermes/          (profiles, skills, config)         │
│  ├── /workspace/project  (git clone of Forgejo repo)        │
│  └── /workspace/data     (kanban workspace, artifacts)      │
│                                                             │
│  Network:                                                   │
│  ├── forgejo.local:3000    (git + REST API)                 │
│  ├── localhost:3000        (Penpot MCP)                     │
│  ├── localhost:3000        (Payload MCP / Next.js)          │
│  ├── localhost:5432        (Postgres)                       │
│  └── external              (model API: OpenRouter/Anthropic) │
└─────────────────────────────────────────────────────────────┘
```

The key architectural point: **the codebase lives on Forgejo, not in the container.** The container is ephemeral in the sense that if it dies, you lose nothing — you `git clone` again. The `~/.hermes/` directory (profiles, skills, kanban state) is the only thing you'd want on a persistent storage volume:

```bash
# Incus setup
incus launch images:debian/13 hermes-dev \
  -d root.disk.size=20GiB \
  -d eth0.ipv4.address=10.0.1.50

# Persistent volume for Hermes state
incus storage volume create default hermes-data
incus config device add hermes-dev hermes-data disk \
  pool=default source=hermes-data path=/root/.hermes
```

## Forgejo Actions as the CI layer

Since you're on Forgejo, you get **Forgejo Actions** (the Gitea-compatible workflow engine) for free. This is where the critic's automated checks run — not inside the agent, but as a pipeline trigger on PR:

```yaml
# .forgejo/workflows/qa.yml
name: Design QA
on:
  pull_request:
    branches: [main]

jobs:
  critic:
    runs-on: [self-hosted]
    steps:
      - uses: actions/checkout@v4
      - name: Run critic checks
        run: |
          # Lint, type-check, visual regression
          npm run lint
          npm run typecheck
          npx playwright test --reporter=json
      - name: Upload screenshots
        uses: actions/upload-artifact@v4
        with:
          name: qa-screenshots
          path: test-results/
```

The agent's critic profile does the _judgment_ (scoring, blocking, filing notes). Forgejo Actions does the _mechanical verification_ (does it compile, do tests pass, do screenshots match). The PR stays open until both are satisfied.

## How the profiles adapt

The only change from the previous architecture: the builder and backend profiles now have a **git + forgejo** toolset instead of assuming local file access:

```yaml
# builder profile
toolsets:
  - terminal # runs locally inside the Incus container
  - file # /workspace/project
  - browser # Playwright for screenshot loop
mcp_servers:
  - name: penpot
    url: http://localhost:3000/mcp
  - name: payload
    url: http://localhost:3000/mcp
env:
  FORGEJO_URL: https://forgejo.local:3000
  FORGEJO_TOKEN: ${FORGEJO_TOKEN}
  FORGEJO_REPO: youruser/yourproject
```

The builder's SOUL.md gets one addition:

```markdown
## Git workflow

- Work on a branch: `builder/<task-slug>`
- Commit with conventional commits
- Push and open a PR to `main` via the Forgejo API
- The PR description includes the kanban task ID and critic score
- Do NOT merge your own PR — the critic's approval is the merge gate
```

## The contract, stated

If you ever swap Forgejo for Codeberg or Gitea (they're all API-compatible), the only thing that changes is the hostname in `FORGEJO_URL` and the token. The git protocol is identical, the REST API is identical, the Actions syntax is identical. That's the affordance boundary doing its job.

If you swap the Incus container for a VM or a bare-metal box, the only thing that changes is how you provision the environment. The agent's config, skills, and profiles are all in `~/.hermes/` — copy that directory and you're done.

---
