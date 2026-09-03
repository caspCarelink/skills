---
name: confluence-documentation
description: Use when writing or updating Confluence documentation for a feature, module, or technical branch/PR handoff (Jobmatch/CGID-style projects), when asked to "document this feature", "skriv dokumentation", "lav Confluence-dokumentation", "lav en teknisk handoff-dokumentation", or when just deciding which doc template to use — "which template should I use", "hvilken skabelon skal jeg bruge", "vælg dokumentationsskabelon" — picks the right template variant and enforces the team's existing structural conventions.
---

# Confluence Documentation

## Overview

The team's Confluence docs (Jobmatch/CGID) follow one of three fixed templates, derived from existing pages. Do not invent your own heading structure — pick the matching variant below and fill it in. Documentation is written in **Danish**.

## When to Use

```dot
digraph variant_choice {
    "What are you documenting?" [shape=diamond];
    "Small, self-contained feature\n(no config/dataflow complexity)" [shape=box];
    "Feature with config, dataflow,\nendpoints, or a how-to guide" [shape=box];
    "A branch diff / PR handoff\n(not a standing feature)" [shape=box];
    "Use A1 (short)" [shape=box];
    "Use A2 (full)" [shape=box];
    "Use B (technical handoff)" [shape=box];

    "What are you documenting?" -> "Small, self-contained feature\n(no config/dataflow complexity)";
    "What are you documenting?" -> "Feature with config, dataflow,\nendpoints, or a how-to guide";
    "What are you documenting?" -> "A branch diff / PR handoff\n(not a standing feature)";
    "Small, self-contained feature\n(no config/dataflow complexity)" -> "Use A1 (short)";
    "Feature with config, dataflow,\nendpoints, or a how-to guide" -> "Use A2 (full)";
    "A branch diff / PR handoff\n(not a standing feature)" -> "Use B (technical handoff)";
}
```

If unclear which variant fits, ask the user rather than guessing.

If the user only wants the variant recommendation (e.g. "which template should I use for X") rather than the finished doc, answer with just the variant (A1/A2/B) + a one-line reason from the decision tree above, and stop there — don't write the template content unless asked.

## Templates

- `templates/a1-short.md` — Feature-dokumentation, kort variant. Small/simple features (e.g. "Jobspor", "Kompetencejagten").
- `templates/a2-full.md` — Feature-dokumentation, fuld variant. Features with architecture, config, endpoints, guides (e.g. "Visitationmodul", "i18n", "Umami-tracking").
- `templates/b-technical.md` — Teknisk branch/PR-dokumentation. A branch diff or handoff summary, not a standing feature.

Read the matching template file before writing. Copy its heading structure exactly (heading text and order) — only the content underneath is yours to fill in.

## Cross-Cutting Rules (apply to every variant)

1. **First section always answers "what is this and why does it exist"** — `## Beskrivelse`, `## Overblik`, or `## Formål og kontekst` depending on variant.
2. **Feature docs (A1/A2) always include a "Hvor finder du featuren" section** — click-by-click navigation from the app's entry point/menu to the feature, as a `Beskrivelse | Billede` table. This is navigation, not usage — keep it separate from the flow/usage section. Not required for B (technical branch/PR docs).
3. **Architecture/file structure is a fenced ` ```text ` code block showing a folder tree** — never prose-described.
4. **Configuration is always its own section with a concrete JSON/code example** — never only described in prose.
5. **How-to/guide sections are numbered `###` steps**, each with a code example where relevant.
6. **Known limitations, workarounds, and TODOs go in exactly one section** (`Q&A / Kendte begrænsninger`, `FAQ`, or `Midlertidige løsninger og kendte begrænsninger`) — do not scatter them through the text.
7. **Screenshots/UI flow steps use a two-column table**: `Beskrivelse | Billede`.
8. **Related files/Jira tickets/other features are listed at the end as references**, not inline.
9. **Written in Danish**, matching the existing documentation's tone.

## Common Mistakes

| Mistake | Fix |
|---|---|
| Inventing your own headings (e.g. "Status/Ejer/Sidst opdateret", "Brugerflow", "Fejlscenarier") | Use the exact heading names from the matching template |
| Describing folder structure in prose | Use a ` ```text ` mappetræ code block |
| Mixing config examples into prose paragraphs | Give config its own `## Konfiguration` section with a code block |
| Splitting limitations/TODOs across multiple sections | Consolidate into one Q&A/FAQ/limitations section |
| Picking a variant without checking scope first | Use the decision flow above; ask if ambiguous |
| Writing in English | Write in Danish |

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
