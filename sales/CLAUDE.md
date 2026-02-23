# Sales-Brain: Working Memory

<!-- ═══════════════════════════════════════════════════════════
     ZONE 1 — STATIC CONFIG (never changes between sessions)
     Fully cacheable. Do not place dynamic content in this zone.
     ═══════════════════════════════════════════════════════════ -->

## Ecosystem Detection

At the start of every session, check whether Solo (core plugin) is installed:

```python
SALES_ROOT = "${CLAUDE_PLUGIN_ROOT}"
SOLO_ROOT  = "${CLAUDE_PLUGIN_ROOT}/../solo"

solo_installed = file_exists(f"{SOLO_ROOT}/.claude-plugin/plugin.json")
```

**If Solo is installed (ecosystem mode):**
- Load DNA files from SOLO_ROOT: ../solo/data/2-Domaines/
- Write client signals to: ../solo/data/1-Projets/clients/
- Use ../solo/data/1-Projets/active-deals/ as the shared deal directory
- Announce: "Solo core detected — reading shared DNA, writing signals to Solo client files."

**If Solo is NOT installed (standalone mode):**
- Load DNA files from own data/2-Domaines/
- Write all data to own data/ directory
- All features work independently

## Shared DNA Paths

| File | Ecosystem path | Standalone path |
|------|---------------|-----------------|
| business-profile.json | ../solo/data/2-Domaines/ | data/2-Domaines/ |
| voice-dna.json | ../solo/data/2-Domaines/ | data/2-Domaines/ |
| icp.json | ../solo/data/2-Domaines/ | data/2-Domaines/ |
| sales-profile.json | data/2-Domaines/ (always local) | data/2-Domaines/ |

If DNA files are empty or missing and Solo is installed: prompt user to run /solo:start.
If standalone and DNA is missing: prompt user to run /sales:start.

## Language Directive

- Check: business_profile.language_preference (from DNA path above)
- If "fr": ALL artifacts (emails, scripts, plans) in French
- If "en": English
- Internal reasoning always in English

## Quality Control (Antislop)

- All draft content checked by antislop-expert skill
- Zero tolerance for: "delve", "tapestry", "demystify", "game-changer"
- When Solo is installed, antislop hook is managed centrally in Solo's hooks.json

## Sentinel Integration (Decision Hygiene)

```python
SENTINEL_ROOT      = "${CLAUDE_PLUGIN_ROOT}/../sentinel-v8"
sentinel_installed = file_exists(f"{SENTINEL_ROOT}/.claude-plugin/plugin.json")
```

If installed, announce: "Sentinel active — decision hygiene enabled for forecasts and deal decisions."

**Skills that invoke Sentinel automatically when installed:**

| Skill/Command | Trigger point | Sentinel agents invoked |
|--------------|--------------|------------------------|
| /forecast | After weighted forecast generated | calibration-coach, reality-checker |
| rfp-shredder | After GO/borderline score | questioner, failure-finder |
| negotiation-advisor | Before Phase 2 (strategy) | questioner, failure-finder |

Decision ledger path (shared across ecosystem): `../sentinel-v8/data/decision-ledger.json`

If Solo is also installed, use Solo's detection of Sentinel as primary.

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
- Never mask: current turn outputs, DNA file reads, active deal data
- Always mask: repeated glob results, boilerplate template reads already used

## Context Compression

At 70-80% utilization, generate an Anchored Iterative Summary:

### Session Intent
### Files Modified
### Decisions Made
### Current State
### Next Steps

## Skills Discovery Protocol

On session start: reference bundle names + descriptions only. Load full skill content only when a specific skill is activated. Never load all 27 skills simultaneously.

Skill bundles (load by name, fetch content on activation):

| Bundle | load_priority | Skills |
|--------|--------------|--------|
| identity-tools | 1 (always pre-loaded) | icp-creator, voice-dna-creator, para-organizer |
| prospecting | 2 | account-research, competitive-intelligence, exa-search-expert, linkedin-prospector, outbound-sequence |
| pipeline-ops | 2 | client-management, daily-briefing, hubspot-sync, territory-planner |
| deal-execution | 3 | call-prep, create-an-asset, discovery-interview-prep, proposal-builder, rfp-shredder |
| deal-strategy | 3 | champion-builder, negotiation-advisor, objection-library, qbr-builder |
| linkedin-social | 3 | linkedin-creator, linkedin-engager, linkedin-orchestrator |
| coaching-quality | 4 | antislop-expert, email-coach, win-loss-analyzer |

---

<!-- ═══════════════════════════════════════════════════════════
     ZONE 2 — SEMI-STATIC STATE (set once by /sales:start)
     Rarely changes. Update only when sales profile changes.
     ═══════════════════════════════════════════════════════════ -->

## Sales Profile

[Waiting for onboarding via /sales:start. Run this command to initialize your sales profile.]

---

<!-- ═══════════════════════════════════════════════════════════
     ZONE 3 — DYNAMIC SESSION MEMORY (per-session state)
     Always last. High churn — never cache this zone.
     ═══════════════════════════════════════════════════════════ -->

## Active Deals Summary

- [No active deals yet.]

## Current Week Priorities

- [Run /sales:daily-briefing to populate.]

## Recent Signals Detected

- [No signals yet. Run /sales:start or connect Exa for daily scans.]
