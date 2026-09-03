# A2 — Feature-dokumentation (fuld variant)

Brug til features med arkitektur, konfiguration, dataflow, endpoints eller en
how-to-guide. Set i: "Dokumentation – Visitationmodul til sagsoverblikket",
"Tekster på forskellige sprog (i18n)", "Analytic (UMAMI)".

```markdown
# <Feature/modul-navn> — dokumentation

## Indholdsfortegnelse
(Kun ved lange dokumenter — Confluence auto-TOC eller manuel liste)

## 1. Formål og kontekst
Hvorfor findes featuren, hvilket forretningsbehov løser den, hvem er
brugeren/borgeren.

## 2. Hvor finder du featuren
Klik-for-klik navigation fra appens forside/menu til featuren — ikke hvordan
featuren bruges, kun hvor den findes. Brug en tabel (Beskrivelse | Billede),
ét skærmbillede pr. navigationstrin.

## 3. Ordliste
Tabel over domænebegreber der er specifikke for featuren (Begreb | Forklaring).
Undlad hvis featuren ikke introducerer nye begreber.

## 4. Arkitektur / Filstruktur
Mappetræ (```text``` code block) der viser hvilke filer/mapper der er
relevante, og hvad hvert lag/fil har ansvar for. Fx:

    shared/src/
    ├── lib/
    │   └── ...
    ├── hooks/
    │   └── ...
    └── store/<feature>/
        ├── <feature>Slice.ts
        ├── api.ts
        └── types.ts

## 5. Sådan fungerer det / Feature beskrivelse
Underopdelt med `###` per delfunktion (fx 5.1, 5.2, 5.3...). Beskriv trin,
regler og logik. Brug kodeeksempler i sprogspecifikke code-blocks. Brug
billeder/mermaid-diagrammer hvor et flow er visuelt tydeligere end tekst.

## 6. Konfiguration
### 6.1 Frontend-konfiguration
### 6.2 Anden konfiguration (fx filter-, org- eller kundegruppekonfiguration)
Vis konkrete JSON/kode-eksempler på config-nøgler og hvad de styrer.

## 7. Teknisk specifikation
### 7.1 Endpoints / API
Liste over relevante endpoints, request/response-form, eller andre tekniske
grænseflader (hooks, helpers, store-actions).

## 8. Sådan tilføjer/bruger man det (guide)
Nummererede trin til at udvide featuren (fx "sådan tilføjer man en ny
oversættelse", "sådan tilføjer du ny sporing"). Hvert trin som eget
`###`-afsnit med kodeeksempel.

## 9. Test
Hvordan testes featuren — testbrugere, miljøer, hvordan man verificerer at det
virker (inkl. evt. API-kald man kan bruge til verifikation).

## 10. Midlertidige løsninger og kendte begrænsninger / FAQ
Workarounds, hardkodede fallbacks, ting der mangler (`TODO`), og svar på
gentagne spørgsmål.

## 11. Relaterede filer / Jira-opgaver
Links til kildefiler, relaterede Confluence-sider og Jira-opgaver.
```

## Noter

- Nummerér `##`-overskrifterne (1–11) som vist — det er sådan eksisterende sider navigerer i Confluence.
- Spring sektioner over hvis de er tomme (fx `## 3. Ordliste`), men behold nummereringen på de resterende — omnummerér ikke.
- `### 6.1` / `### 6.2` og `### 7.1` er obligatoriske underoverskrifter, ikke valgfrie forslag.
- `## 2. Hvor finder du featuren` er navigation (klik-stien til featuren), `## 5. Sådan fungerer det` er brug/logik — bland ikke de to sammen.
