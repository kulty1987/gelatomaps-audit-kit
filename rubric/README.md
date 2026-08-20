# HELADER-IA Public Rubric v1.6 — plain-language guide

> Machine version: [`eval-rubric-v1.6.json`](./eval-rubric-v1.6.json) in this folder (mirrored 2026-07-27).
> Live canonical source: https://gelatomaps.com/api/v1/evaluation-rubric.json — always fetch fresh before a real submission, the live endpoint wins on any disagreement.
> Human-readable page: https://gelatomaps.com/eval-rubric/
> License: **CC-BY-4.0**. Effective since 2026-07-12.

This is the open scoring standard GelatoMaps uses to classify ice cream shops into a **4-tier scale (0-3 "bolas" — scoops)**. It is not secret sauce: the formula, the weights, and the franchise rules below are exactly what our own editorial team and `POST /api/v1/audit` apply. Any agent following this document correctly should land on the same number we would.

## The scale

| Bolas | Score | Label | What it means |
|---|---|---|---|
| 0 | 0–24 | Basic / industrial / franchise | No artisan signals. Default when no evidence is given. |
| 1 | 25–49 | Verified artisan presence | Ice cream is the main product, address verified. |
| 2 | 50–74 | Accredited artisan | Editorial evidence of artisan production (video, own recipes, workshop photos, certifications). |
| 3 | 75–100 | Pure artisan excellence | Documentary-verified: on-site production **and** own recipes **and** editorial consistency across all 5 voices. A single supplier invoice is never enough on its own. |

## The five weighted dimensions

```
score = 100 × ( 0.30 × rating_norm
              + 0.25 × reviews_log
              + 0.20 × artisanal_score
              + 0.10 × digital_score
              + 0.15 × base_score_norm )
```

| Dimension | Weight | How it's computed |
|---|---|---|
| `rating` | 30% | Linear from 3.0★→0.0 to 5.0★→1.0. |
| `reviews` | 25% | log10(reviews) / log10(3000), capped at 3000 reviews. |
| `artisanal` | 20% | Keyword match (strong/medium/weak, region-specific lexicon) + evidence boosts: supplier invoice +0.30, workshop photo/video +0.15 (once), own recipes +0.05. Capped at 1.0. |
| `digital` | 10% | Description >50 chars +0.30, ≥5 photos +0.40 (or ≥1 photo +0.20), Google place_id present +0.30. Website/Instagram inform editorial review but do not score numerically here. |
| `base_score` | 15% | Prior editorial history. **Always 0.0 for a fresh external/on-demand audit** — this is not something your agent can inflate. |

Full worked example, subscore formulas, and the exact multi-language keyword lists (es / it / en-US / en-UK / fr / pt / de / ja) live in [`eval-rubric-v1.6.json`](./eval-rubric-v1.6.json) → `subscore_algorithms` and `artisanal_keywords`.

## Franchise caps — v1.4's core change (2026-07-12)

The cap on a multi-location brand depends on **its production model**, never on the word "franchise" by itself:

| Category | Cap | What it is | Examples |
|---|---|---|---|
| `industrial_franchise` | **1 bola**, fixed | Central factory, identical frozen product at every location. | Llaollao, Smöoy, Cold Stone Creamery, Baskin-Robbins, Häagen-Dazs standard stores |
| `premium_franchise` | **2 bolas**, liftable | Consistent quality, but semi-centralized production. | Amorino, Grom, Venchi, La Romana, Bacio di Latte, Rocambolesc |
| `artisan_franchise` | **no cap** (up to 3), audited per location | Chain/brand where every location has its own workshop and own recipes — includes regional artisan chains. | Salt & Straw, Jeni's, Van Leeuwen (US) · Cremela (Asturias, Spain) |

**The cap on `premium_franchise` is only lifted with documented on-site artisan production per location** (own workshop + own recipes, evidence tier 3). Prestige, a celebrity chef's name, industry awards, or a peer recommendation do **not** lift it — only verifiable production does. This is deliberate: it makes the exception harder to claim for a chain than for an independent shop, not easier.

Two whitelists exist so you don't miscap shops that only *look* like chains:
- **Heritage trade names** (Spain): La Ibense, La Jijonenca, Los Valencianos, Llinares — independent artisans sharing a historical name from the Jijona (Alicante) tradition, not a franchise. Audit each individually.
- **US craft chains with in-house production**: Salt & Straw, Jeni's, Van Leeuwen, Humphry Slocombe — multiple locations, but each produces on-site with its own recipes. Not industrial. Audit each branch on local evidence.

## Regional lexicon — use the shop's language, not Spanish by default

The 5 dimensions and the franchise rules are universal. What changes by region is which words count as "artisanal" signals — see `artisanal_keywords` (8 language families) and `regional_equivalences` (cultural mapping, with named exemplar brands) in the JSON. A few explicit corrections (`bias_warnings`) exist because the rubric was authored in a Spain/Italy context:

- Do **not** penalize a shop for lacking "family tradition" or "X generations" — the US craft movement (2000s+) is fully artisan despite being young.
- Do **not** require the literal word "artisanal" — US shops say "craft"/"small batch"/"scratch-made", Japan says "手作り"/"工房", France says "artisanal"/"glacier artisan" (a legally protected label since 1996 — treat it as automatic evidence tier 2).
- A rotating seasonal menu is a **positive** artisan signal, not a lack of identity.

**Known gap (2026-07-27):** `regional_equivalences` currently covers US, UK, IT, FR, ES, DE-AT-CH, PT-BR, JP-KR-TW. It does **not yet** have dedicated entries for the ~14 additional Spanish-speaking countries GelatoMaps is opening this winter (Uruguay, Paraguay, Chile, Bolivia, Peru, Ecuador, Colombia, Venezuela, Costa Rica, Nicaragua, Guatemala, Mexico, El Salvador, Honduras). Until that's added: use the `es` keyword lexicon (it's Spanish-language, not Iberian-specific, so "artesanal" / "elaboración propia" / "obrador" apply fine across Latin America), but tag `regional_context` with the shop's real ISO code (e.g. `"UY"`, not `"ES"`) so editorial can tell where the shop actually is. Some countries have locally popular synonyms worth noting in your rationale even though they don't change the score — e.g. Mexico ("nevería", "paletería"), El Salvador ("sorbetería", "sorbete"). Raise a gap you find via `hola@gelatomaps.com` or a repo issue.

## Anti-gaming, in one paragraph

A rating ≥4.5 with 100+ reviews and **no** artisan evidence caps at 2 bolas, never 3 — reviews alone don't prove a workshop exists. A sudden review surge in under 30 days is a flag for human review, not an auto-promotion. Shop owners cannot self-certify: evidence must be third-party verifiable (invoices, photos/video, certifications). This is why every submission needs `evidence` with real URLs, not just a confident-sounding `rationale`.

## Conflict of interest, disclosed

GelatoMaps is built by an artisan ice cream-making family (Familia Llinares, Azuaga, Extremadura, Spain, since 1947), and two of their own shops appear as rubric exemplars. They are scored with the exact same public formula as everyone else, any editorial delta is logged at `/api/v1/audit-log/<slug>`, and paying never alters bolas — see `trust_contract` and `metadata.conflict_of_interest_disclosure` in the JSON for the full safeguards. Raise concerns at `hola@gelatomaps.com` or via a repo issue labeled `conflict-of-interest`.
