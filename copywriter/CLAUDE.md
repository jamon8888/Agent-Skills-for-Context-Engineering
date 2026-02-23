# Copywriter Studio Context (V2)

<!-- ═══════════════════════════════════════════════════════════
     ZONE 1 — STATIC CONFIG (never changes between sessions)
     Fully cacheable. Do not place dynamic content in this zone.
     ═══════════════════════════════════════════════════════════ -->

## Ecosystem Detection

At the start of every session, check whether Solo (core plugin) is installed:

```python
WRITER_ROOT = "${CLAUDE_PLUGIN_ROOT}"
SOLO_ROOT   = "${CLAUDE_PLUGIN_ROOT}/../solo"

solo_installed = file_exists(f"{SOLO_ROOT}/.claude-plugin/plugin.json")
```

**If Solo is installed (ecosystem mode):**
- Load DNA from: ../solo/data/2-Domaines/
- Write content calendar to: ../solo/data/2-Domaines/content-calendar.md
- Write publish records to: ../solo/data/4-Archives/content/
- Save drafts to: ../solo/data/1-Projets/ (shared PARA root)
- Announce: "Solo core detected — reading shared DNA, writing content outputs to Solo PARA structure."

**If Solo is NOT installed (standalone mode):**
- Load DNA from own: data/2-Domaines/
- Write calendar to: data/2-Domaines/content-calendar.md
- Write archives to: data/4-Archives/content/
- All features work independently

## Shared DNA Paths

| File | Ecosystem path | Standalone path |
|------|---------------|-----------------|
| business-profile.json | ../solo/data/2-Domaines/ | data/2-Domaines/ |
| voice-dna.json | ../solo/data/2-Domaines/ | data/2-Domaines/ |
| icp.json | ../solo/data/2-Domaines/ | data/2-Domaines/ |
| analytics-history.json | data/2-Domaines/ (always local) | data/2-Domaines/ |

If DNA files are empty or missing and Solo is installed: prompt user to run /solo:start.
If standalone and DNA is missing: prompt user to run /copywriter:start.

## Language Directive

**CRITICAL**: Check `business.language_preference` (from DNA path above).

- If `"fr"`: ALL output must be in French. Auto-translate source material.
- If `"en"`: Output in English.
- If `"bilingual"`: Match requested language, default to English.

## PARA Structure Paths

| Folder | Ecosystem path | Standalone path | Purpose |
|--------|---------------|-----------------|---------|
| PROJECTS | ../solo/data/1-Projets/ | data/1-Projets/ | Active writing assignments |
| AREAS | ../solo/data/2-Domaines/ | data/2-Domaines/ | Profiles and evergreen assets |
| RESOURCES | ../solo/data/3-Ressources/ | data/3-Ressources/ | Templates, research, transcripts |
| ARCHIVES | ../solo/data/4-Archives/ | data/4-Archives/ | Published work |
| INBOX | ../solo/data/0-Inbox/ | data/0-Inbox/ | Incoming content triggers |

## Anti-Slop Guard Word List

NEVER use: "delve", "tapestry", "landscape", "game-changer", "unleash", "elevate", "demystify", "revolutionize", "synergy", "leverage" (as verb).
ALWAYS use: Concrete nouns, active verbs, specific examples. Vary sentence length. Avoid "sandwich" paragraphs.
When Solo is installed, antislop hook runs centrally from Solo's hooks.json.

## Sentinel Integration (Decision Hygiene)

```python
SENTINEL_ROOT      = "${CLAUDE_PLUGIN_ROOT}/../sentinel-v8"
sentinel_installed = file_exists(f"{SENTINEL_ROOT}/.claude-plugin/plugin.json")
```

If installed, announce: "Sentinel active — content strategy decisions structured."

**Skills that invoke Sentinel automatically when installed:**

| Skill | Trigger point | Sentinel agents invoked |
|-------|--------------|------------------------|
| content-calendar-planner | Before cascade model runs | sentinel-diverge, strategy-marketing domain |
| linkedin-analytics | Before strategy recommendations | calibration-coach |

Decision ledger path: `../sentinel-v8/data/decision-ledger.json`

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
- Never mask: current turn outputs, DNA file reads, active draft data
- Always mask: repeated glob results, boilerplate template reads already used

## Context Compression

At 70-80% utilization, generate an Anchored Iterative Summary:

### Session Intent
### Files Modified
### Decisions Made
### Current State
### Next Steps

## Skills Discovery Protocol

On session start: reference bundle names + descriptions only. Load full skill content only when a specific skill is activated. Never load all 20 skills simultaneously.

Skill bundles (load by name, fetch content on activation):

| Bundle | load_priority | Skills |
|--------|--------------|--------|
| identity-core | 1 (always pre-loaded) | business-profile-creator, voice-dna-creator, icp-creator |
| content-strategy | 2 | content-calendar-planner, linkedin-analytics, seo-blog-writer, title-brain |
| social-content | 2 | linkedin-post, linkedin-scheduler, twitter-thread, video-script-generator |
| long-form-conversion | 3 | newsletter-writer, sales-email-sequence, landing-page-copy |
| research-tools | 3 | exa-search-expert, reddit-research-insights, quote-extractor |
| content-ops | 4 | antislop-expert, social-media-bio-generator, wordpress-publisher |

---

<!-- ═══════════════════════════════════════════════════════════
     ZONE 2 — SEMI-STATIC STATE (set once by /copywriter:start)
     Rarely changes. Update only when copywriter profile changes.
     ═══════════════════════════════════════════════════════════ -->

## Copywriter Profile

[Waiting for onboarding via /copywriter:start. Run this command to initialize your copywriter profile.]

---

<!-- ═══════════════════════════════════════════════════════════
     ZONE 3 — DYNAMIC SESSION MEMORY (per-session state)
     Always last. High churn — never cache this zone.
     ═══════════════════════════════════════════════════════════ -->

## Content Calendar (Current Week)

- [No calendar yet. Run /copywriter:plan to generate.]

## Active Drafts

- [No active drafts.]

## Publishing Queue

- [No items queued for publishing.]
