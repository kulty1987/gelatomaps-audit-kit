# Examples

## Fictional payloads (in this folder)

- [`contribution-light.example.json`](./contribution-light.example.json) — a valid, minimal payload for `POST /api/v1/audit-contribution`.
- [`contribution-full.example.json`](./contribution-full.example.json) — a valid, complete payload for `POST /api/v1/audit-report-contribution`, with all five voices and structured evidence filled in.

Both describe a **fictional shop, "Heladería Ejemplo" in Montevideo, Uruguay** — clearly marked `_example_disclaimer` at the top of each file. Uruguay is deliberate: it's one of the 14 countries GelatoMaps is opening this winter (2026-2027) alongside Italy, on top of Spain/Portugal/Argentina already live. We picked a country with (as of this writing) close to zero published shops on purpose — it's the most honest illustration of "your audit could be the first one we publish from your country."

**Do not POST these files unmodified.** The `place_id` values are deliberately fake strings that will fail Google's real-establishment validation. Replace every field with real, independently-verifiable data about an actual shop before submitting.

## Real gold-standard reports (linked, not copied)

We do not vendor full rendered reports in this repo — the real ones embed high-resolution photos and run into several megabytes of inline data, which does not belong in a git-cloned kit. Instead, read the live pages:

| Report | Shop | Verdict | Why it's a good reference |
|---|---|---|---|
| [gelatomaps.com/hia/reports/COR092.html](https://gelatomaps.com/hia/reports/COR092.html) | Piamonte D'Ambrosio · Córdoba, Spain | 3 bolas · score 77 | Full five-voices narrative, family history, award evidence (MMAHE 2024), press mentions. The reference for what a deep, well-evidenced 3-bola report reads like. |
| [gelatomaps.com/hia/reports/SEV007.html](https://gelatomaps.com/hia/reports/SEV007.html) | Heladería Rosalía · Sevilla, Spain | 3 bolas | Shorter, still complete: hero, score block, comparador (city ranking), press section. Closer in length to what a first-time contributor should aim for. |
| [gelatomaps.com/api/v1/audit-report-example.html](https://gelatomaps.com/api/v1/audit-report-example.html) | Tiempo de Café · Badajoz, Spain (BAD009) | 0 bolas · score 16 | The official template's own worked example — and deliberately a **low** score. Read this one if you think "gold standard" only means high scores: a well-evidenced, honestly-argued 0-bola report is exactly as valuable to us as a 3-bola one. We are not grading you on how high the number is, we're grading you on whether the number is earned. |

## The two live JSON templates for reference

- [gelatomaps.com/audit-kit/templates/audit-report.template.v1.json](https://gelatomaps.com/audit-kit/templates/audit-report.template.v1.json) — GelatoMaps' own original JSON Schema for the full report, published 2026-05-17. **Heads up:** as of 2026-07-27 it still says `rubric_version_required: "1.3"` and uses the field names `assessment` (inside each voice) and `url_or_source` (inside each evidence item). The live endpoint that actually renders a published report reads `comment` and `url` instead. We built [`../schemas/contribution-full.schema.json`](../schemas/contribution-full.schema.json) by matching the endpoint's actual documented behavior, not by copying this older template, precisely to avoid that drift. If you use the field names from *this* older JSON file your submission will still be accepted (the server doesn't hard-fail on unknown keys), but the fields it doesn't recognize (`assessment`, `url_or_source`) will simply not render on the published page. Use the schema in this repo instead.
- [gelatomaps.com/audit-kit/](https://gelatomaps.com/audit-kit/) — the live web kit (brand assets, HTML layout templates, master prompts in Spanish/English). This repo is a GitHub-native companion to it, not a replacement.
