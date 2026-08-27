# B — Teknisk branch/PR-dokumentation

Brug når dokumentationen beskriver et branch-diff/handoff frem for en stående
feature. Set i: "Driftsiden - teknisk dokumentation".

```markdown
# <Emne> - teknisk dokumentation af arbejdet indtil d. <dato>

**Repo:** <org/repo>
**Branch:** <branch-navn> (bygger evt. videre på <anden branch/ticket>)
**Sammenlignet med:** <base-branch, fx main>
**Omfang:** <X commits, Y filer ændret (+A / -B linjer)>

## Resumé
Kort opsummering (bullets) af de vigtigste ændringer/beslutninger i branchen.

## 1. Arkitektur / call-chain
Beskriv det lagdelte flow (Presentation → State → Data-fetching →
Data-access → ...) — gerne som et ```mermaid flowchart```-diagram — samt
kodeeksempler for de centrale lag.

## 2. Tematiske ændringer
Underopdelt med `###` per tema (fx "Migrering til X", "Y indført som eneste
data-lag", "Reduktion af Z", "Ny mappestruktur", "Nye features",
"Public-sider"). Hvert afsnit forklarer hvad der er ændret og hvorfor.

## 3. Filstruktur
Top-niveau mappetræ, evt. med et konkret eksempel på en feature-mappe som
reference-implementering.

## Handoff til videreudvikling
Konkrete punkter til den der skal overtage/fortsætte arbejdet — hvad mangler,
hvad skal man vide, hvor er faldgruberne.
```

## Noter

- Metadata-blokken (Repo/Branch/Sammenlignet med/Omfang) står altid lige under `#`-titlen, før `## Resumé`.
- `## 1. Arkitektur / call-chain` er nummereret, men `## Resumé` og `## Handoff til videreudvikling` er ikke — følg dette mønster præcist, det er ikke en fejl.
- Brug ```mermaid``` kun til call-chain/flow-diagrammer, ikke til filstruktur — filstruktur er altid ```text``` mappetræ.
