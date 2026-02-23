# Solo Plugin — Context Memory

<!-- ═══════════════════════════════════════════════════════════
     ZONE 1 — STATIC CONFIG (never changes between sessions)
     Fully cacheable. Do not place dynamic content in this zone.
     ═══════════════════════════════════════════════════════════ -->

## Ecosystem Detection

Solo is the core plugin. At the start of every session, detect which addon plugins are installed by checking for their plugin files relative to Solo's root:

- `${CLAUDE_PLUGIN_ROOT}/../sales/.claude-plugin/plugin.json` → Sales addon
- `${CLAUDE_PLUGIN_ROOT}/../copywriter/.claude-plugin/plugin.json` → Copywriter addon
- `${CLAUDE_PLUGIN_ROOT}/../sentinel-v8/.claude-plugin/plugin.json` → Sentinel addon

Announce detected addons in the first response of each session:
- If sales installed: "Sales addon detected — pipeline and deal intelligence active."
- If copywriter installed: "Copywriter addon detected — content calendar and publishing active."
- If sentinel installed: "Sentinel active — structured decision hygiene enabled."

## Shared DNA (Single Source of Truth)

These files live in Solo and are read by all installed addons. Solo creates them on /solo:start.

| File | Path | Used by |
|------|------|---------|
| business-profile.json | data/2-Domaines/business-profile.json | Solo + Sales + Copywriter |
| voice-dna.json | data/2-Domaines/voice-dna.json | Solo + Sales + Copywriter |
| icp.json | data/2-Domaines/icp.json | Solo + Sales + Copywriter |
| content-calendar.md | data/2-Domaines/content-calendar.md | Solo (reads) + Copywriter (writes) |
| clients/ | data/1-Projets/clients/ | Solo (manages) + Sales (writes signals) |

If DNA files are empty or missing: prompt user to run /solo:start first.

## Cross-Plugin Data Flows

Solo receives from Copywriter (when installed):
- /copywriter:plan writes content calendar to data/2-Domaines/content-calendar.md
- blog-agent writes publish records to data/4-Archives/content/[slug]-[date].md
- monday-morning-agent and weekly-digest-agent read both paths automatically

Solo receives from Sales (when installed):
- signal-trapper-agent appends signals to data/1-Projets/clients/[Company].md
- pipeline-guardian-agent reads data/1-Projets/active-deals/ (shared path)

Solo provides to all addons:
- /solo:start creates all three DNA files in the canonical schema
- data/ is the shared filesystem root for the entire ecosystem

## Language & Localization

- Check: data/2-Domaines/business-profile.json → business_profile.language_preference
- If "fr": ALL outputs (reports, emails, proposals, content) must be in French
- If "en": ALL outputs in English
- If missing: default English, then ask user preference

## Sentinel Integration (Decision Hygiene)

Decision ledger path (shared across ecosystem): `../sentinel-v8/data/decision-ledger.json`

Skills that invoke Sentinel automatically when installed:

| Skill | Trigger point | Sentinel agents invoked |
|-------|--------------|------------------------|
| idea-test | After GO/LEARN MORE verdict | failure-finder, questioner |
| launch-planner | Before D-14 calendar generation | failure-finder, reality-checker, calibration-coach |
| pricing-strategy | Before rate card save | reality-checker, sentinel-reframe |
| tam-sam-som-calculator | After SOM calculation | reality-checker, questioner |

## Token Budget

| Category              | Target  | Max     |
|-----------------------|---------|---------|
| System + DNA refs     | 2,000   | 3,000   |
| Active skill content  | 3,000   | 5,000   |
| Tool outputs (recent) | 4,000   | 8,000   |
| Message history       | 5,000   | 10,000  |
| Reserved buffer       | 2,000   | —       |

Trigger context compression when message history exceeds 10,000 tokens.

## Observation Masking

Tool outputs that are 3+ turns old AND whose purpose has been served:
- Replace with: `[Ref: <tool_name> result turn N — key finding: <1 sentence>]`
- Never mask: current turn outputs, DNA file reads, active client data
- Always mask: repeated glob results, boilerplate template reads already used

## Context Compression

At 70-80% utilization, generate an Anchored Iterative Summary:

### Session Intent
### Files Modified
### Decisions Made
### Current State
### Next Steps

## Skills Discovery Protocol

On session start: reference bundle names + descriptions only. Load full skill content only when a specific skill is activated. Never load all 44 skills simultaneously.

Skill bundles (load by name, fetch content on activation):

| Bundle | load_priority | Skills |
|--------|--------------|--------|
| core-identity | 1 (always pre-loaded) | business-profile-creator, voice-dna-creator, icp-creator, positioning-statement, para-organizer |
| client-work | 2 | client-management, client-onboarding, discovery-call, proposal-generator, scope-management, contract-templates |
| revenue-ops | 2 | invoice-generator, expense-tracker, financial-health, retainer-manager, pricing-strategy, product-pricing-model |
| sales-growth | 3 | sales-pipeline, draft-outreach, company-research, competitive-analyzer, exa-search-expert, reddit-research-insights, landing-page-builder |
| product-build | 3 | pipeline-orchestrator, prd-development, user-story, design-brief-generator, problem-statement, proto-persona |
| research-validate | 3 | user-discovery, tam-sam-som-calculator, customer-journey-map, idea-test |
| diagnostics | 4 | diagnostic-builder, diagnostic-runner, diagnostic-analyzer, tally-integration |
| operations | 4 | weekly-review, project-management, capacity-planner, business-health-advisor, launch-planner, antislop-expert |

## Glossary

| Term | Meaning |
|------|---------|
| PARA | Projects, Areas, Resources, Archives |
| DNA files | business-profile, voice-dna, icp — shared identity layer |
| Standalone | Solo works fully without addons |
| Ecosystem | All three plugins in same Cowork folder |

---

<!-- ═══════════════════════════════════════════════════════════
     ZONE 2 — SEMI-STATIC STATE (set once by /solo:start)
     Rarely changes. Update only when business profile changes.
     ═══════════════════════════════════════════════════════════ -->

## Me

[Waiting for onboarding via /solo:start. Run this command to initialize your business profile.]

---

<!-- ═══════════════════════════════════════════════════════════
     ZONE 3 — DYNAMIC SESSION MEMORY (per-session state)
     Always last. High churn — never cache this zone.
     ═══════════════════════════════════════════════════════════ -->

## Active Clients

- [No clients yet. Add one via /solo:clients add]

## Current Pipeline

- [No opportunities in pipeline.]

## Revenue Tracking

- **MRR:** $0
- **Quarterly Target:** $0
