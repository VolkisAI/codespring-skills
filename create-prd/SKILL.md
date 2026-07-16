---
name: create-prd
description: Specialist for creating CodeSpring PRDs (Product Requirement Docs) for a feature. Interactive — it lists the user's core features, asks which one and whether to generate Frontend, Backend, or Both, then deep-dives the real code and generates the PRD(s) and attaches them to the feature's bridge on the canvas. Use when the user wants a PRD for a feature, wants to document a feature so they can build new ones without duplicating code, or says. Triggers, "create a PRD", "make a frontend PRD", "make a backend PRD", "generate PRDs for this feature", "document this feature in CodeSpring", "I want a spec for X so I can design new features".
version: 0.1
---

# Create a PRD in CodeSpring

A PRD (Product Requirement Doc) is CodeSpring's requirement document for a feature. It exists so CodeSpring has a **full, accurate understanding of how the app actually works** — so that when the user designs a NEW feature, they build on what exists instead of duplicating backend, re-creating a shared API route, or reinventing the design system.

There are two kinds, and this skill can make either or both:
- **Backend PRD** — how the feature is built under the hood: architecture, data flow, API routes, data model, security, external services, infra, and the reusable pieces a new feature should reuse rather than rebuild.
- **Frontend PRD** — how the feature looks and behaves: the design system (colors, radius, spacing, type), component styles, layout/positioning, and the exact navigation path (click X on this page → go to this route → see this → it looks like this).

You are the specialist. Be interactive, be precise, and ground everything in the real code.

---

## 0. Connect first (every CodeSpring skill starts here)

1. **Authenticated?** Run `codespring auth status`. If not authenticated / expired, ask the user to run `codespring auth login` and STOP until confirmed. (Running any CLI command refreshes an expired OAuth token — do this before reading the token file.)
2. **Project linked?** Run `codespring status`. If nothing is linked, help them: `codespring projects` → `codespring init --project <id> --force` (or `codespring project create ...` then init). If the codebase is not yet imported into CodeSpring at all, run the **import-codebase** skill first — this skill needs the feature map to exist.
3. **Use the LOCAL project.** Read `projectId` from `<cwd>/.codespring/config.json`, NEVER the global `~/.codespring/config.json` (it points at the last project used elsewhere). Print the projectId before any write.

API base for the steps below: `https://server.codespring.app/api`, header `Authorization: Bearer <accessToken>` (or `x-api-key: <apiKey>`) from `~/.codespring/credentials.json`.

---

## 1. Ask what to generate (interactive)

First, show the user their core features so they can choose:
```bash
codespring features --json      # list features; the CORE ones are the root features (no parent)
```
Fetch the mindmap and read `node-features.items` to get the clean list of core features with their ids. Then ask the user two things:

1. **Which core feature?** (present the list; allow one, several, or "all")
2. **Frontend, Backend, or Both?**

Do not assume — wait for their choice. (If they already told you, skip the question.)

---

## 2. Deep-dive the real code for the chosen feature

This is where the value is. Read the actual code for the selected feature (read-only; sub-agents help for breadth). Do NOT guess — open files. Gather the material for whichever PRD types were chosen.

### For a BACKEND PRD — capture everything useful for designing new features safely:
- **Architecture & structure** — how this feature's code is organized (folders, layers: route/handler → service/action → data), and the patterns/conventions it follows (so a new feature matches them).
- **What the code actually does** — the real behavior, step by step, for each operation.
- **API routes** — every endpoint the feature uses: path, method, request/response shape, and **which OTHER features also use it (flag shared routes explicitly)** so new work reuses them instead of duplicating.
- **Data model** — tables/collections, key columns, enums, relationships; what is written vs read.
- **Server actions / services / queries** — the reusable functions that already exist (a new feature should call these, not re-implement them).
- **Security** — auth (who can call what), ownership/authorization checks, input validation, secrets/keys, row-level security or access rules, webhook signature verification.
- **External services & env vars** — every integration and the env vars it needs.
- **Infra & gotchas** — hosting model, request timeouts/limits, async or polling jobs, cron/queues (or their absence), storage buckets, rate/credit logic, hardcoded hosts, provider mismatches, orphan/misnamed routes. Anything a new-feature author would trip over.
- **Reuse map** — a short "already exists, reuse this" list: shared routes, shared utilities, shared tables. This is the point of the whole PRD.

### For a FRONTEND PRD — capture the design system AND the navigation:
- **Design tokens** — colors/branding (hex or CSS vars), corner radius, spacing scale, typography (font, sizes, weights), shadows, borders. Pull these from the Tailwind config, CSS variables, theme file, or the component classNames.
- **Component & UI styles** — which UI components/library (e.g. shadcn) and how they are styled here; buttons, cards, inputs, modals, badges — their look and states (hover/active/disabled).
- **Layout, positioning & spacing** — how the page is laid out (grid/flex, columns, breakpoints), where things sit relative to each other, padding/margins/gaps. Be concrete ("2-col grid, 24px gap, cards with 12px radius and a subtle shadow").
- **What the user sees on this page** — header, sections, cards, tables, empty states, loading states, toasts.
- **Navigation / interaction map** — the exact path to and through the feature: which sidebar item or button opens it, what route it lands on, what it shows, and what each interaction does. Phrase it concretely: "Click *New Project* on the Projects page → routes to /projects/new → shows a 2-step form → on submit, toast + redirect to the project detail." Include where this feature links FROM and TO other features.

### Feed the deep-dive back as the feature's note (context for generation)
CodeSpring's generator uses the feature's **note** as context, so a rich note makes a far better PRD. Write your findings into a "how it works" note before generating (ONE note per core feature; re-running overwrites it; only ROOT feature ids are accepted):
```bash
codespring mindmap note <coreFeatureId> --title "How it works — <Feature>" --text "$(cat note.txt)"   # redirect output to /dev/null
```

---

## 3. Generate the PRD(s) (internal API — current workaround)

PRD creation is not yet a CLI command, so call the API the web app uses. Run `codespring auth status` first so the token is fresh, then:
```
POST /prds/generate   body: { projectId, bridgeNodeId, prdType }
  bridgeNodeId = "bridge-feature-<featureId>"      (doubled "feature-feature" when featureId starts with "feature-")
  prdType      = "frontend" | "backend"            (or "database" if asked)
```
- It **streams (SSE) and takes ~20–30s**. Use an HTTP timeout **≥ 120s**. A client-side abort does NOT stop the server — it still creates the record — so a short timeout + retry produces **duplicate PRDs**.
- For "Both", generate frontend and backend (two calls per feature).
- The generated PRD pulls in the feature's note as context, so §2's note directly shapes quality.

### If you created duplicates (timeouts), dedupe:
`DELETE /prds/<id>` requires an active checkpoint. First `POST /projects/<projectId>/checkpoints {message:"..."}`, then delete the shorter/duplicate ones. Group by the PRD `name` field ("Frontend PRD: <Feature>" / "Backend PRD: <Feature>") — the detail endpoint's `featureName` is unreliable.

---

## 4. Attach the PRD to the canvas (so it actually shows)

`/prds/generate` creates the PRD **record** but does NOT add a node to the mindmap, so it will not appear until you add nodes and PUT the whole `flowJson`. For each targeted feature add a `prdBridge` off the feature's bridge, then the PRD node(s) carrying the record id:
```
node prdBridge    id: prdBridge-<bridgeNodeId>
     data {title:"PRD Bridge", itemId:<featureId>, featureId:<featureId>, isCollapsed:false, generatingType:null, handlePosition:"left"}
node prdFrontend  id: prdFrontend-<ts>-<rand>  data {prdId:<recordId>, title:"Frontend PRD", isCollapsed:false, isGenerating:false, handlePosition:"left"}
node prdBackend   id: prdBackend-<ts>-<rand>   data {prdId:<recordId>, title:"Backend PRD",  isCollapsed:false, isGenerating:false, handlePosition:"left"}
edge bridge    -> prdBridge   sourceHandle "<bridgeNodeId>-source-prd"        targetHandle "<prdBridgeId>-target-prd-bridge"
edge prdBridge -> prdFrontend sourceHandle "<prdBridgeId>-source-prd-bridge"  targetHandle "<prdNodeId>-target-prd"
edge prdBridge -> prdBackend  (same source handle as above)
```
All edges: `{type:"default", className:"stroke-foreground", style:{strokeWidth:2}}`. Position `prdBridge` to the right of the feature bridge and the PRD nodes to the right of it. If a `prdBridge` already exists for that feature, add the PRD node to it instead of creating a second one. Persist with:
```
PUT /mindmaps/project/<projectId>   body: { flowJson: { nodes, edges } }
```
This REPLACES the entire flowJson — fetch the current one, ADD your nodes/edges, keep everything else, then re-verify (next step).

---

## 5. Verify
- The PRD record(s) exist with real content (thousands of chars, not empty). Read one back to sanity-check accuracy against the code.
- The canvas shows a `prdBridge` on the feature with the Frontend/Backend PRD node(s), each carrying a valid `prdId`.
- **Core features unchanged:** `node-features.items.length` still equals the real number of core features — PRD generation/checkpoints can flatten sub-features into the core list (`parentFeatureId` reset to null). If it happened, re-parent each stray sub-feature: `codespring feature update <subFeatureId> --parent <coreFeatureId>`.
- Give the user the link: `https://v2.codespring.app/project/<projectId>`.

## What good looks like
- The user picked a feature and FE/BE/Both, and got exactly that.
- The **Backend PRD** captures architecture, API routes, security, data model and a clear "reuse this, do not rebuild it" map — so new features avoid duplicate backend.
- The **Frontend PRD** captures the design system (colors, radius, spacing, type), component styles, layout, and the concrete navigation/interaction path — so a new feature looks and flows like the existing app.
- Both are attached to the feature's bridge and grounded in the real code.
