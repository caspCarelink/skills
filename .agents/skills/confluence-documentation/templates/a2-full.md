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

## 2. Ordliste
Tabel over domænebegreber der er specifikke for featuren (Begreb | Forklaring).
Undlad hvis featuren ikke introducerer nye begreber.

## 3. Arkitektur / Filstruktur
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

## 4. Sådan fungerer det / Feature beskrivelse
Underopdelt med `###` per delfunktion (fx 4.1, 4.2, 4.3...). Beskriv trin,
regler og logik. Brug kodeeksempler i sprogspecifikke code-blocks. Brug
billeder/mermaid-diagrammer hvor et flow er visuelt tydeligere end tekst.

## 5. Konfiguration
### 5.1 Frontend-konfiguration
### 5.2 Anden konfiguration (fx filter-, org- eller kundegruppekonfiguration)
Vis konkrete JSON/kode-eksempler på config-nøgler og hvad de styrer.

## 6. Teknisk specifikation
### 6.1 Endpoints / API
Liste over relevante endpoints, request/response-form, eller andre tekniske
grænseflader (hooks, helpers, store-actions).

## 7. Sådan tilføjer/bruger man det (guide)
Nummererede trin til at udvide featuren (fx "sådan tilføjer man en ny
oversættelse", "sådan tilføjer du ny sporing"). Hvert trin som eget
`###`-afsnit med kodeeksempel.

## 8. Test
Hvordan testes featuren — testbrugere, miljøer, hvordan man verificerer at det
virker (inkl. evt. API-kald man kan bruge til verifikation).

## 9. Midlertidige løsninger og kendte begrænsninger / FAQ
Workarounds, hardkodede fallbacks, ting der mangler (`TODO`), og svar på
gentagne spørgsmål.

## 10. Relaterede filer / Jira-opgaver
Links til kildefiler, relaterede Confluence-sider og Jira-opgaver.
```

## Noter

- Nummerér `##`-overskrifterne (1–10) som vist — det er sådan eksisterende sider navigerer i Confluence.
- Spring sektioner over hvis de er tomme (fx `## 2. Ordliste`), men behold nummereringen på de resterende — omnummerér ikke.
- `### 5.1` / `### 5.2` og `### 6.1` er obligatoriske underoverskrifter, ikke valgfrie forslag.
