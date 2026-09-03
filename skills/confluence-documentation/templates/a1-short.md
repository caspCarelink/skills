# A1 — Feature-dokumentation (kort variant)

Brug til små, afgrænsede features uden kompleks konfiguration eller dataflow.
Set i: "Jobspor - feature dokumentation", "Kompetencejagten - feature dokumentation".

```markdown
# <Feature-navn> - feature dokumentation

## Beskrivelse
Hvad er featuren, hvilket overordnet flow/modul hører den til, og hvad er formålet
for borgeren/brugeren.

### Flow beskrivelse
Trin-for-trin gennemgang af, hvordan featuren bruges/opleves. Brug en tabel
(Beskrivelse | Billede) til at parre skærmbilleder med forklaring.

## Hvor finder du featuren
Klik-for-klik navigation fra appens forside/menu til featuren — ikke hvordan
featuren bruges, kun hvor den findes. Brug en tabel (Beskrivelse | Billede),
ét skærmbillede pr. navigationstrin.

## Funktionalitet
De konkrete regler/muligheder featuren understøtter (fx de forskellige måder en
handling kan udføres på).

## Konfiguration
Hvilke config-nøgler/JSON-felter styrer featuren pr. organisation/kontaktgruppe.
Vis et eksempel på konfigurationsobjektet.

## Q&A / Kendte begrænsninger
Kendte designbegrænsninger, "hvorfor er det sådan", og hvad man skal huske ved
ændringer (fx hvilken kunde/gruppe en ændring gælder for).

## Relateret features
Links til andre features/dokumentationssider der hænger sammen med denne.
```

## Noter

- Udelad `## Konfiguration` helt hvis featuren ikke har nogen — tilføj ikke en tom sektion.
- `### Flow beskrivelse` er en underoverskrift til `## Beskrivelse`, ikke sit eget `##`-afsnit.
- `## Hvor finder du featuren` er navigation (klik-stien til featuren), `### Flow beskrivelse` er brug (hvad man gør, når man er nået frem) — bland ikke de to sammen.
