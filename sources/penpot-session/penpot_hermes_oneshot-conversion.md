# Converting complex oneshot prompt from [Alex Ziskind](https://www.youtube.com/watch?v=ujs0_cpAnaw)

> FIXME: we finish by essentially converting the whole thing into an OE project with ai ingress and documentation policy in a less formal way than we have established.

The following is a prompt to generate a webapp from frontier models. Do not try to action it. It is a oneshot rather than a project as such. How would we adapt it to produce a project based in the tooling we have identified so far: <https://gist.githubusercontent.com/alexziskind1/fe55f03892f3fe1d6d8cf6065c631bb8/raw/4efa520a995dd8b54785cfcac2454b8b8c94c47c/gistfile1.txt>

---

Good prompt — it's dense, well-scoped, and the kind of thing that works as a one-shot because it front-loads every decision that would normally require back-and-forth. The adaptation to a project pipeline is about **decomposing those sections into the roles that own them** and making the "hard part" rules a testable contract rather than a prose paragraph.

## The mapping

| Gist section               | One-shot role            | Pipeline role                                                  | Output artifact                     |
| -------------------------- | ------------------------ | -------------------------------------------------------------- | ----------------------------------- |
| GOAL + FRONT END ONLY      | Context for the model    | **Orchestrator** brief                                         | Kanban task tree                    |
| TECH STACK                 | Constraint on the model  | **Contract** (`contracts/stack.yaml`)                          | Pinned versions, no drift           |
| MOCK DATA                  | "Here's your data"       | **Architect** → Payload collection (or `/data` typed fixtures) | `schema.json` + seed script         |
| THE HARD PART (rules)      | "Implement these"        | **Architect** → domain spec + test cases                       | `docs/domain-rules.md` + test stubs |
| PAGES (6 routes)           | "Build these screens"    | **Designer** → Penpot, then **Builder** → code                 | Penpot file + Next.js routes        |
| DESIGN DIRECTION           | "Make it look like this" | **Designer** (impeccable skill)                                | Penpot tokens + components          |
| TESTS                      | "Write tests"            | **Builder** writes, **Critic** verifies                        | Vitest suite + Playwright E2E       |
| DELIVERABLES (quality bar) | "Don't ship garbage"     | **Critic** (both taste skills)                                 | Score + block/approve               |

## The kanban task tree

```
T1: "Define domain rules + data model" → architect
T2: "Design Silicon Exchange in Penpot" → designer (parent: T1)
T3: "Implement business logic + tests" → builder (parent: T1)
T4: "Build frontend from Penpot + logic" → builder (parent: T2, T3)
T5: "E2E QA + visual fidelity check" → critic (parent: T4)
```

## The Skills, plugins and MCP

### Skills

Hermes
kanban-orchestrator skill

[Taste](https://github.com/leonxlnx/taste-skill)
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

[Impeccable](https://github.com/pbakaus/impeccable)
npx impeccable install

hermes profile create builder
hermes profile create auditor

hermes -p builder skills install Leonxlnx/taste-skill
hermes -p auditor skills install pbakaus/impeccable

Each profile sees only its own skill. The builder profile has taste for generation; the auditor profile has impeccable for audit/polish. No rule conflicts, no context pollution.

Right — the **`profile-delegate-plugin`** (Heeervas) is a separate tool (`profile_delegate`), not a backend swap on `delegate_task`. That actually makes the setup simpler for your use case:

**Install on the builder profile only:**

```bash
mkdir -p ~/.hermes/profiles/builder/plugins
git clone https://github.com/Heeervas/profile-delegate-plugin.git \
  ~/.hermes/profiles/builder/plugins/profile-delegate
hermes -p builder plugins enable profile-delegate
```

**Security config** (required before it works):

```bash
export PROFILE_DELEGATE_ALLOWED_PROFILES=auditor
export PROFILE_DELEGATE_MAX_DEPTH=1
export PROFILE_DELEGATE_MAX_CONCURRENT=1
export PROFILE_DELEGATE_ALLOWED_TOOLSETS=file,terminal
```

**The call** (from the builder, mid-session):

```json
{
  "profile": "auditor",
  "task": "Audit this component for design quality: /path/to/component.tsx",
  "context": "Built with taste-skill. Stack: React + Tailwind. Focus on token consistency and anti-slop patterns.",
  "timeout_seconds": 600
}
```

The child runs **as the `auditor` profile** — so it automatically loads impeccable (and whatever else is in that profile's skill set) without you passing `skills` explicitly. The result comes back as a summary to the builder.

**One caveat:** unlike `profile_routes` (which shares the parent's credentials with the child), `profile_delegate` runs the target profile with **its own** credentials and terminal policy. That's actually cleaner for your setup — the auditor can be locked down to read-only file access if you want.

So the pipeline is:

1. Builder profile (taste-skill) generates the component
2. Builder calls `profile_delegate` → auditor
3. Auditor (impeccable) runs `/impeccable audit` or `/impeccable polish`
4. Summary returns to builder

No `delegation.backend` config needed, no `profile_routes` block — just the plugin and the allowlist env var.

[Design-extract](<>)
npx skills add Manavarya09/design-extract

[Penpot skills](<>)
[Penpot MCP skill](https://github.com/ar27111994/penpot-mcp)
npx skills add ar27111994/penpot-mcp

[Payload skills](https://github.com/payloadcms/skills)
npx skills add payloadcms/skills

[Vitest](https://github.com/vitest-dev/vitest)
npx skills add https://github.com/sanity-io/next-sanity --skill vitest

### MCP

Penpot MCP - included in the penpot web-app

[Penpot AI Kit](https://github.com/penpot/penpot-ai-kit)
Repository of skills and workflows for AI Design in Penpot

## What each role gets (extracted from the gist)

**T1 — Architect** receives the "MOCK DATA" + "THE HARD PART" sections:

```markdown
# Task: Domain rules + data model for Silicon Exchange

## Data model

- 24 listings, 5+ regions, chips: H100, RTX 5090, M3 Ultra, MI300X, RTX Pro 6000
- Fields: id, slug, chip, memory_gb, tflops, rate_cents_per_hour, region, status
- 30 days × 24h utilization samples (seeded PRNG, deterministic)
- Reservations: listing_id, start, end, status (held|confirmed|cancelled|expired)

## Domain rules (these are the contract — the builder's tests must prove them)

1. Overlap: half-open intervals. [14:00, 16:00) and [16:00, 18:00) do NOT overlap.
2. Pricing: 15-min increments, round UP. Min 1h. >24h continuous → 10% off hours 25+.
   All money in integer cents. No floating point.
3. Holds: 10-min expiry. Real timer. Visible countdown.
4. Maintenance: blocks new reservations. Existing confirmed ones unaffected.
5. Persistence: localStorage. Survives page reload.

## Output

- /data/types.ts (all interfaces)
- /data/seed.ts (deterministic PRNG, no Math.random)
- /lib/domain/ (pure functions: canReserve, calculatePrice, isExpired)
- /lib/domain/**tests**/ (Vitest: one test per rule, including the "naive overlap" edge case)
```

**T2 — Designer** receives the "DESIGN DIRECTION" section:

```markdown
# Task: Design Silicon Exchange in Penpot

## Direction (from brief)

- Dark-first, real light mode toggle, no flash on reload
- Technical-industrial: near-black, hairline borders, monospace for numbers/IDs
- One electric accent, used sparingly. Data-dense but calm. Trading terminal, not SaaS.
- Micro-interactions: hover lift, animated focus rings, scroll reveals, gradient hero
- Responsive: phone + 4K. Tables → cards on mobile. Charts readable at 375px.
- Accessibility: full keyboard nav, visible focus, semantic headings, aria labels
- No stock photos. Inline SVG, CSS gradients, generated only.
- Two self-hosted fonts: one heading, one body.

## Pages to design (6)

1. Home — hero, GPU counter, 3 feature cards, CTA
2. /browse — grid, filters (search, region, memory range, status, sort), empty state
3. /listings/[slug] — spec sheet, 24h utilization chart, 7-day calendar, reservation form with live price
4. /dashboard — reservations, countdowns, cancel, running total
5. /compare — up to 3 listings side by side
6. 404 — on-brand

## Output

- Penpot file with all 6 screens
- Design tokens (colors, type scale, spacing, border-radius)
- Component library (card, filter bar, chart wrapper, calendar, reservation form)
```

The designer applies **impeccable** (dice roll for direction, 62 detector rules as pre-flight). The "trading terminal not SaaS" line is the brief that outranks the skill's own taste.

**T3 — Builder (logic)** receives the architect's domain spec:

```markdown
# Task: Implement business logic + unit tests

Read the domain rules from the parent task. Implement:

- /lib/domain/overlap.ts — pure function, tested
- /lib/domain/pricing.ts — pure function, tested (integer cents only)
- /lib/domain/holds.ts — timer logic, tested
- /lib/domain/maintenance.ts — status gate, tested

Every rule from the architect's spec gets at least one test.
The "naive overlap would wrongly allow" case MUST be a named test.
No `any` types. No TODOs.
```

**T4 — Builder (frontend)** receives the Penpot file + the domain library:

```markdown
# Task: Build frontend from Penpot design + domain logic

Read the Penpot file via MCP for:

- Layout structure per screen
- Token values (colors, spacing, type)
- Component hierarchy

Use the domain functions from /lib/domain/ — do NOT reimplement pricing or overlap.
Tech stack (from contract): Next.js App Router, TS strict, Tailwind, Framer Motion, Recharts, Zustand.
Filter state in URL query string. Survives refresh + back button.
Live price quote recalculates on time-range drag.
```

The builder applies **taste-skill** (reference library, browser check loop).

**T5 — Critic** receives the running app + the Penpot file + the domain tests:

```markdown
# Task: E2E QA on Silicon Exchange

## Visual pass (taste-skill + impeccable)

- Screenshot every page at 375px, 1440px, 2560px
- Compare against Penpot export
- Score on 7 axes. Block if < threshold.
- Check: dark/light toggle works, no flash, monospace on numbers, one accent only

## Functional pass (domain rules)

- Run the Vitest suite. All must pass.
- Manually verify: create a reservation, confirm overlap is blocked,
  verify pricing math on a 30-hour booking (10% off hours 25-30 only),
  verify hold expiry at 10 min.
- Check localStorage persistence across reload.

## Accessibility pass

- Keyboard-only navigation through all 6 pages
- Focus states visible
- Chart has text summary alternative
```

## The contract files

These live in the Forgejo repo and are the **affordance boundaries** that make the pipeline swappable:

```yaml
# contracts/stack.yaml
framework: next@15
language: typescript@5
strict: true
styling: tailwindcss@4
animation: framer-motion
charts: recharts
state: zustand
testing: vitest
e2e: playwright
# No paid services. No external API calls.
```

```yaml
# contracts/domain.yaml
# The "hard part" as a machine-readable contract
rules:
  - id: overlap-half-open
    description: "Reservations use half-open intervals [start, end). Adjacent ranges do not overlap."
  - id: pricing-increments
    description: "Billed in 15-min increments, rounded UP. Min 1h."
  - id: pricing-long-booking
    description: ">24h continuous: 10% discount on hours 25+ only, not whole booking."
  - id: money-integer-cents
    description: "All monetary values are integer cents. No floating point."
  - id: hold-expiry
    description: "Unconfirmed holds expire after 10 minutes. Real timer, visible countdown."
  - id: maintenance-blocks
    description: "Maintenance status blocks new reservations. Existing confirmed unaffected."
  - id: persistence
    description: "Reservations persist to localStorage. Survive full page reload."

# Each rule maps to at least one named test in /lib/domain/__tests__/
```

```yaml
# contracts/design.yaml
# Extracted from DESIGN DIRECTION
theme: dark-first
light_mode: true, persistent, no-flash
aesthetic: technical-industrial
surfaces: near-black
borders: hairline
typography:
  numbers_ids: monospace
  fonts: 2, self-hosted via next/font
accent: 1 electric color, used sparingly
density: data-dense but calm
reference: "trading terminal, not SaaS landing page"
no:
  - stock photography
  - external image requests
  - lorem ipsum
  - dead buttons
responsive: [375px, 1440px, 2560px]
accessibility:
  - full keyboard nav
  - visible focus states
  - semantic headings
  - labelled form controls
  - aria labels on icon buttons
  - text summary for every chart
```

## What changes from a one-shot

| One-shot                                             | Pipeline                                             | Why                                                                                                    |
| ---------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Model reads the whole prompt in one context          | Each role reads only its section                     | Smaller context = better compliance with the specific rules                                            |
| "THE HARD PART" is prose the model must interpret    | `contracts/domain.yaml` + named tests                | The critic can verify rule-by-rule, not "does it look right"                                           |
| Design direction is adjectives                       | Penpot file with tokens + components                 | The builder reads actual values, not "technical-industrial"                                            |
| Tests are "write tests covering..."                  | Test stubs generated by architect, filled by builder | The critic runs them as a gate, not a suggestion                                                       |
| Quality bar is "should look like a high-end product" | 7-axis score + detector rules + browser screenshots  | Measurable, not vibes                                                                                  |
| One model, one context, one shot                     | 5 tasks, 3 profiles, dependency chain                | If the builder gets pricing wrong, the critic catches it and re-assigns with the specific failing test |

## The one thing you'd keep as a "prompt"

The **orchestrator's brief** — the GOAL section, condensed — is still a natural-language prompt. It's what you type into the kanban:

```
Build "Silicon Exchange" — a GPU rental marketplace frontend.
Dark, technical-industrial, trading-terminal aesthetic.
6 pages: home, browse, listing detail, dashboard, compare, 404.
Business logic: overlap detection, 15-min pricing, 10-min holds, maintenance gates.
All mock data, deterministic, no external services.
```

The orchestrator decomposes that into the five tasks above, assigns them to profiles, and the pipeline runs. The detailed sections (hard part, design direction, tech stack) live in the contracts and the role-specific task bodies — not in a single monolithic prompt.

## The Forgejo shape

```
silicon-exchange/
├── contracts/
│   ├── stack.yaml
│   ├── domain.yaml
│   └── design.yaml
├── docs/
│   └── domain-rules.md          # human-readable version of domain.yaml
├── /data/
│   ├── types.ts
│   └── seed.ts
├── /lib/domain/
│   ├── overlap.ts
│   ├── pricing.ts
│   ├── holds.ts
│   ├── maintenance.ts
│   └── __tests__/
│       ├── overlap.test.ts
│       ├── pricing.test.ts
│       ├── holds.test.ts
│       └── maintenance.test.ts
├── /app/                         # Next.js App Router
│   ├── page.tsx                  # Home
│   ├── browse/page.tsx
│   ├── listings/[slug]/page.tsx
│   ├── dashboard/page.tsx
│   ├── compare/page.tsx
│   └── not-found.tsx
├── /components/
├── /lib/
├── penpot/                       # exported tokens + component specs
│   └── tokens.json
├── .forgejo/workflows/
│   └── qa.yml                    # critic's mechanical checks on PR
└── package.json
```

The gist prompt is ~200 lines of dense instruction. The pipeline version is ~50 lines of orchestrator brief + three contract files + five task bodies. More total text, but each piece is **owned by one role, verified by one gate, and swappable without touching the others.**

---

The following is a prompt to generate a webapp from frontier models. Do not try to action it. It is a oneshot rather than a project as such. How would we adapt it to produce a project based in the tooling we have identified so far: <https://gist.githubusercontent.com/alexziskind1/fe55f03892f3fe1d6d8cf6065c631bb8/raw/4efa520a995dd8b54785cfcac2454b8b8c94c47c/gistfile1.txt>

---

No — it's correctly absent _for this project_, not lost from the architecture. Silicon Exchange is explicitly a **frontend-only, mock-data, no-external-services** demo. The data lives in `/data/seed.ts` (deterministic PRNG) and persists to localStorage. There's no content to manage, no crawler feeding it, no real backend — so Payload has nothing to serve.

The division is intact at the **contract layer**. You just don't have a `contracts/cms.yaml` for this project because the CMS affordance isn't exercised. The moment you make this a real product (crawler feeding live GPU listings, multiple regions, real reservations), Payload reappears as:

```
/data/seed.ts  →  replaced by  Payload API (GET /api/listings)
localStorage   →  replaced by  Payload DB (reservations collection)
```

And the builder's task body changes from "use the domain functions + mock data" to "call the Payload MCP for listings, write reservations via Payload Local API." The Penpot side is unchanged — it's still the design source either way.

If you want to keep the division _visible_ in the project structure even for the mock version (so the swap path is explicit), add a thin adapter:

```typescript
// /lib/data/source.ts
// Swap this file when moving from mock → Payload
import { listings, reservations } from "../data/seed";

export const getListings = async (filters: ListingFilters) =>
  listings.filter(matchesFilters);

export const createReservation = async (r: ReservationInput) => {
  // localStorage for now, Payload API later
};
```

Now the builder only ever imports from `/lib/data/source.ts`. When you add Payload, you rewrite _that one file_ and nothing else in the frontend changes. That's the affordance boundary doing its job — the division is preserved even when the layer is a stub.

---

For the ai harness like hermes that would attach to thos project I we have an ingress documentation or associated skills that would allow a new session to construct the needed observer position to know which contracts are currently not in place and understand the current state and rules of the project and the ongoing direction.

---

Yes, and it's the missing piece that makes the whole thing self-sustaining across sessions. The pattern is a **project constitution + contract status registry + observer skill** that any new session reads before doing anything else.

## The three artifacts

### 1. `constitution.md` (project root, in the Forgejo repo)

This is the "ingress documentation" — the single file a new session reads first. It's not a skill, it's a **standing order**:

```markdown
# Constitution — Silicon Exchange

## What this is

A GPU rental marketplace frontend. Dark, technical-industrial,
trading-terminal aesthetic. Currently: mock-data demo.
Target: live product fed by scraper ingress + Payload CMS.

## Direction (as of 2026-10-24)

- Phase 1: Frontend demo with deterministic mock data [IN PROGRESS]
- Phase 2: Payload CMS + real scraper ingress [PLANNED]
- Phase 3: Multi-tenant, real reservations, auth [NOT STARTED]

## Rules (non-negotiable)

- All contracts in /contracts/ are the source of truth for what's
  implemented vs. what's planned. If code and contract disagree,
  the contract wins and the code is a bug.
- No new dependencies without updating contracts/stack.yaml.
- No domain rule changes without updating contracts/domain.yaml
  AND adding a named test for the new rule.
- The critic blocks any PR that violates an active contract.
- Open-source only. No BSL/SSPL/proprietary dependencies.
  If a dependency's license changes, it's a P0.

## How to assess current state

1. Read /contracts/ — each file has a `status:` field per rule
2. Read the kanban board — what's done, in-flight, blocked
3. Run `npm test` — which domain rules are actually verified
4. Read the latest critic report in /docs/reports/
5. Compare: contracted vs. tested vs. designed vs. built.
   The gaps are your work.

## Affordance boundaries (do not cross)

- Designer: reads design.yaml, writes to Penpot only
- Architect: reads all contracts, writes schema + test stubs only
- Backend: reads domain.yaml + stack.yaml, writes /lib/ only
- Builder: reads Penpot + domain lib, writes /app/ + /components/ only
- Critic: reads everything, writes to /docs/reports/ only
- Crawler: writes to jobs_staging only (when active)
```

### 2. `contracts/` with status markers

Each contract file gets a per-rule `status` field. This is what the observer checks:

```yaml
# contracts/domain.yaml
status: active # this contract is in force
rules:
  - id: overlap-half-open
    status: verified # has a passing test
  - id: pricing-increments
    status: verified
  - id: pricing-long-booking
    status: implemented # code exists, test not yet written
  - id: money-integer-cents
    status: verified
  - id: hold-expiry
    status: implemented
  - id: maintenance-blocks
    status: contracted # in the spec, not yet implemented
  - id: persistence
    status: contracted

# Status values:
#   contracted  — in the spec, no code yet
#   implemented — code exists, not yet tested
#   verified    — has a passing named test
#   waived      — explicitly deferred (with reason + date)
```

```yaml
# contracts/design.yaml
status: active
theme:
  status: verified # tokens extracted, applied
typography:
  status: implemented # fonts loaded, not yet verified at 3 breakpoints
responsive:
  status: contracted # designed for 375/1440/2560, not yet built
accessibility:
  status: contracted
```

```yaml
# contracts/stack.yaml
status: active
framework: next@15
language: typescript@5
# ... (no status per line — the whole file is either in force or not)
```

```yaml
# contracts/cms.yaml
status: planned # not yet in force — Phase 2
# When Payload is added, this becomes:
# status: active
# and the builder's data source adapter switches
```

### 3. The observer skill (`skills/project-observer/SKILL.md`)

This is the "how to assess" capability. Any profile can load it (especially the orchestrator on session start):

```markdown
# Skill: project-observer

## When to use

- Session start (orchestrator, before task assignment)
- Before the critic scores a PR (to know what to check)
- When a user asks "where are we?" or "what's next?"

## Procedure

1. Read /constitution.md — direction, phase, rules
2. Read every file in /contracts/ — note status per rule
3. Run: `npm test -- --reporter=json 2>&1 | jq '.numTotalTests, .numPassedTests, .numFailedTests'`
4. Read the kanban board: `kanban_list(status: [done, in_progress, blocked])`
5. Read the latest file in /docs/reports/ (critic's last assessment)
6. Cross-reference:
   - contracted but no test → gap (priority: high if in current phase)
   - implemented but not verified → gap (priority: medium)
   - verified but design not matching → gap (priority: high)
   - contract says "planned" but code references it → drift (flag)

## Output format

Write to /docs/reports/observer-YYYY-MM-DD.md:

## Phase: [current]

## Contracts in force: [list]

## Contracts planned: [list]

## Gaps:

- [contract rule] — [status] — [what's missing]

## Drift:

- [discrepancy between code and contract]

## Next actions (suggested, not assigned):

- [specific task] → [profile]

## Blockers:

- [anything preventing progress]
```

## How a new session bootstraps

The orchestrator's `SOUL.md` gets one line at the top:

```markdown
## On session start

Load the project-observer skill. Run the full assessment.
Do NOT assign tasks until you've written the observer report.
The report is your context — work from it, not from memory.
```

So the first thing any new session does is:

```
Session starts
    │
    ▼
Orchestrator loads project-observer skill
    │
    ▼
Reads constitution.md → knows the direction, phase, rules
    │
    ▼
Reads contracts/ → knows what's contracted, implemented, verified
    │
    ▼
Runs tests → knows what's actually working
    │
    ▼
Reads kanban → knows what's in-flight, blocked, done
    │
    ▼
Writes observer report → /docs/reports/observer-2026-10-24.md
    │
    ▼
Now assigns tasks based on the gaps
```

The report is committed to Forgejo. The _next_ session reads it as the starting point rather than re-deriving everything. Over time, `/docs/reports/` becomes the project's running log — a chronological record of what the observer saw and what was done about it.

## The "not yet in place" question

Your specific question — "which contracts are currently not in place" — is answered by the status field. For Silicon Exchange as of today:

```
contracts/stack.yaml    → active, all in place
contracts/domain.yaml   → active, 2/7 verified, 2/7 implemented, 3/7 contracted
contracts/design.yaml   → active, 1/4 verified, 1/4 implemented, 2/4 contracted
contracts/cms.yaml      → planned (Phase 2, not yet in force)
contracts/ingress.yaml  → planned (Phase 2, not yet in force)
```

The observer skill reads those status fields and the answer is mechanical, not interpretive. A new session doesn't need to "understand the project" — it needs to read five YAML files and a test report.

## Why this works across sessions

The key property: **the project's state is in the repo, not in the agent's context.** The constitution, contracts, test results, kanban board, and observer reports all live in Forgejo. A new session on a fresh Incus container clones the repo, runs the observer skill, and is fully oriented in under a minute. No "remember what we discussed last time" — the state is in the files.

This is also what makes it swappable: if you replace Hermes with a different agent framework, the constitution + contracts + reports are still valid. They're plain markdown and YAML. The agent is the consumer, not the owner, of the project state.

---

Here's how the full stack connects:

## Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│  DESIGN LAYER                                                        │
│                                                                     │
│  Penpot (self-hosted)                                               │
│  ├── Design tokens (W3C DTCG JSON)                                  │
│  ├── Components / styles / libraries                               │
│  └── Penpot MCP Server (official, ~68 tools)                        │
│       ├── Read shapes, extract tokens, generate CSS/HTML            │
│       ├── Create/modify components in-file                          │
│       └── Design-to-code, code-to-design, design-to-design          │
│                                                                     │
│  Skills (guide agent behaviour when using MCP):                     │
│  ├── taste-skill → art direction, anti-slop at generation           │
│  └── impeccable  → audit/polish post-build                          │
└──────────────────────────────┬──────────────────────────────────────┘
                               │ tokens + component specs
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│  CMS / DATA LAYER                                                    │
│                                                                     │
│  Payload 3 (Next.js app)                                            │
│  ├── @payloadcms/db-postgres → Postgres 16/17                       │
│  ├── Collections: pages, sections, components, design-tokens        │
│  ├── REST + GraphQL + Local API                                     │
│  ├── Custom endpoints (for scraper ingestion)                       │
│  └── Hooks: beforeChange / beforeValidate (data transformation)     │
│                                                                     │
│  Payload MCP (disruption-hub or ngyngcphu)                          │
│  ├── validate, generate_collection, generate_field                  │
│  ├── scaffold_project, query                                       │
│  └── (development-time; not a runtime data channel)                 │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│  DATA INGRESS                                                        │
│                                                                     │
│  External sources / scrapers                                        │
│  ├── → Payload REST API (API key auth: `{slug} API-Key {key}`)      │
│  ├── → Custom endpoint (e.g. /api/ingest/scrape)                    │
│  └── → beforeChange hook: transform, validate, enrich               │
└─────────────────────────────────────────────────────────────────────┘
```

## Key clarifications

**1. Penpot → Payload token sync**

Penpot exports tokens as JSON (W3C DTCG, though the community notes the actual export is closer to Tokens Studio multi-set format — there's an open issue on this). The flow:

- **Manual / CI:** Penpot webhook fires on token change → script exports JSON → POSTs to a Payload custom endpoint → stored in a `design-tokens` collection or global
- **Agent-driven:** Hermes agent calls Penpot MCP to read current tokens, then calls Payload REST API to upsert them. The `penpot-mcp` skill (ar27111994) has token-extraction and Style Dictionary config workflows built in.

**2. Skills + MCPs are not the same layer**

| Layer          | Role                                                                         | Examples                                                                     |
| -------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **MCP Server** | _Capability_ — gives the agent tools to read/write a system                  | Penpot MCP (shapes, tokens, export), Payload MCP (validate, scaffold, query) |
| **Skill**      | _Policy_ — tells the agent _how_ to use those tools, what quality bar to hit | taste-skill (anti-slop rules), impeccable (audit checklist)                  |

So your Hermes profiles would load **both**: the skill file (SKILL.md) for policy, and the MCP server config for capability. The `profile_delegate` setup from earlier still applies — builder profile gets taste + Penpot MCP, auditor profile gets impeccable + Penpot MCP.

**3. Data ingress from scrapers**

Three patterns, depending on where the scraper lives:

| Pattern              | How                                                             | When                                                      |
| -------------------- | --------------------------------------------------------------- | --------------------------------------------------------- |
| **Same process**     | Scraper calls Payload Local API (`payload.create()`) directly   | Scraper is a route handler or cron inside the Next.js app |
| **Separate service** | Scraper calls Payload REST API with API key auth                | Scraper is a separate Node/Python service                 |
| **Webhook / queue**  | External event → queue (BullMQ, Redis) → worker calls Local API | High-volume or bursty ingestion                           |

The `beforeChange` hook is where you normalise scraped data into your collection schema before it hits Postgres:

```ts
hooks: {
  beforeChange: [
    async ({ data, operation }) => {
      if (operation === 'create' && data.source === 'scraper') {
        data.title = slugify(data.title)
        data.status = 'draft'  // force human review
      }
      return data
    },
  ],
}
```

**4. What the Payload MCP actually does (and doesn't)**

The community Payload MCPs (`disruption-hub/payloadcmsmcp`, `ngyngcphu/payload-mcp`) are **development-time** tools — they help the agent scaffold collections, validate field configs, and generate boilerplate. They are _not_ a runtime data channel. For runtime data access, the agent uses the REST API or the Local API (if in-process).

So in your Hermes setup:

- **Builder profile** → Penpot MCP + taste-skill → generates design, extracts tokens, writes to Payload via REST
- **Auditor profile** → Penpot MCP + impeccable → audits the generated output against the design system
- **Payload MCP** → available to both for scaffolding new collections or validating field configs during development

**5. One gap to watch**

Penpot doesn't have a dedicated token-retrieval API endpoint yet (issue #7916). The workaround is `get-file` + parsing, or using the MCP server's token extraction tool. If your pipeline depends on programmatic token sync in CI, you'll want to pin to the MCP-based path rather than raw API until that lands.

---

Payload 3 is database-agnostic via its adapter pattern. Three official options:

| Adapter      | Package                   | Best for                                                                    |
| ------------ | ------------------------- | --------------------------------------------------------------------------- |
| **Postgres** | `@payloadcms/db-postgres` | Production. Rigid schema, enforced relationships, migrations, read replicas |
| **MongoDB**  | `@payloadcms/db-mongodb`  | Dynamic/document-shaped fields, existing Mongo stacks                       |
| **SQLite**   | `@payloadcms/db-sqlite`   | Local dev & prototyping only (WAL mode supported)                           |

Plus two variants: `@payloadcms/db-vercel-postgres` (Vercel serverless-optimised) and `@payloadcms/db-d1-sqlite` (Cloudflare Workers/D1).

**For a presentation layer with a defined content model** (pages, sections, components, design tokens extracted by impeccable), Postgres is the clear pick:

- Your data shape is known and relatively flat
- You'll have relationships between collections (page → section → component → token)
- Migrations keep the schema in sync as your design system evolves
- Works with any managed Postgres (Neon, Supabase, RDS, Railway, Layerbase, etc.)

```ts
import { buildConfig } from "payload";
import { postgresAdapter } from "@payloadcms/db-postgres";

export default buildConfig({
  db: postgresAdapter({
    pool: { connectionString: process.env.DATABASE_URL },
  }),
  // collections: [...],
});
```

One practical note from the community: Payload 3.x had a brief incompatibility with **Postgres 18** (fixed in 3.65.0). If you're spinning up a fresh instance, target Postgres **16** or **17** to be safe.

The other thing to decide alongside the DB: **storage adapter** for uploaded media (local disk, S3, Cloudflare R2, GCS, etc.). That's a separate adapter from the database — worth locking in early since it affects your deploy topology.

---

Two errors and one significant overlap to flag:

## Corrections

| Entry              | Issue                                     | Fix                                                         |
| ------------------ | ----------------------------------------- | ----------------------------------------------------------- |
| **Vitest**         | `sanity-io/next-sanity` is the wrong repo | `npx skills add antfu/skills --skill vitest`                |
| **Penpot skills**  | Empty link                                | `npx skills add penpot/penpot-ai-kit` (official, 13 skills) |
| **Design-extract** | Empty link                                | `npx skills add Manavarya09/design-extract`                 |

## The overlap problem

The Penpot AI Kit already ships its own `penpot-anti-slop` skill (scores 0–100, loops until ≤ 35) and `penpot-audit-tokens` (hardcoded values, off-grid spacing, orphan tokens). These directly overlap with taste-skill and impeccable's audit commands.

So you have three anti-slop/audit layers:

| Skill                | Scope                            | Where it fires                                  |
| -------------------- | -------------------------------- | ----------------------------------------------- |
| **taste-skill**      | Generic frontend (any framework) | Builder profile, at generation time             |
| **penpot-anti-slop** | Penpot design files specifically | Builder profile, when working in Penpot via MCP |
| **impeccable**       | Generic frontend (any framework) | Auditor profile, post-build                     |

**Practical resolution:** taste-skill and `penpot-anti-slop` serve the same role at different fidelity levels. When the builder is working _inside Penpot via MCP_, `penpot-anti-slop` is more precise (it can probe the actual shape tree, check token bindings, score visually). taste-skill is the fallback for when you're generating code directly without Penpot in the loop.

You can keep both — they don't conflict because they activate on different triggers (Penpot MCP context vs. raw code generation) — but be aware that if the builder profile has both active and is working in Penpot, you'll get double anti-slop passes. The cleaner setup:

## Recommended profile assignment

```
BUILDER profile:
├── kanban-orchestrator          (built-in)
├── taste-skill                  (code-gen art direction)
├── penpot-ai-kit (full)         (Penpot MCP skills incl. anti-slop, audit-tokens, design-md)
├── penpot-mcp (ar27111994)      (MCP connection skill)
├── design-extract               (extract tokens from existing sites → Penpot)
├── payload (payloadcms/skills)  (CMS development patterns)
└── vitest (antfu/skills)        (test writing)

AUDITOR profile:
├── impeccable                   (audit / polish / harden)
├── penpot-ai-kit (audit subset) (penpot-audit-accessibility, penpot-audit-tokens, penpot-design-to-code-review)
├── payload (payloadcms/skills)  (validate CMS schema)
└── vitest (antfu/skills)        (verify test coverage)
```

**Key decision:** install the _full_ penpot-ai-kit on the builder, but only the three audit skills on the auditor (`penpot-audit-accessibility`, `penpot-audit-tokens`, `penpot-design-to-code-review`). The build skills (`penpot-build-screen`, `penpot-component-factory`, etc.) belong on the builder only.

This gives you: taste for code-gen, penpot-ai-kit for design-file work, impeccable as the independent quality gate, and design-extract as the ingestion path from existing sites into your Penpot design system.

---
