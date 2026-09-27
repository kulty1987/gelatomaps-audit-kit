# HELADER-IA Public Rubric v1.8 — plain-language guide

> Machine version: [`eval-rubric-v1.8.json`](./eval-rubric-v1.8.json) in this folder (mirrored 2026-09-26).
> Live canonical source: https://gelatomaps.com/api/v1/evaluation-rubric.json — always fetch fresh before a real submission, the live endpoint wins on any disagreement.
> Human-readable page: https://gelatomaps.com/eval-rubric/
> License: **CC-BY-4.0**. Effective since 2026-09-08.

This is the open scoring standard GelatoMaps uses to classify ice cream shops into a **4-tier scale (0-3 "bolas" — scoops)**. It is not secret sauce: the formula, the weights, and the franchise rules below are exactly what our own editorial team and `POST /api/v1/audit` apply. Any agent following this document correctly should land on the same number we would.

## Founder doctrine behind v1.8

*"Artisanry must outweigh reviews. Reviews are Google's; we do something else. Three bolas means signals of a well-trained maker — and no trained maker uses an industrial base."* Since v1.8, **own formulation is the heaviest dimension** (35%), taken from the verdict of a verified **FormulaMaps Formulation Certificate** — a deterministic, hash-verifiable recipe-book evaluation that contains no recipes. Accrediting is **free** for any claimed shop; paying never alters bolas, score, or ranking.

## The scale (bands are unequal on purpose)

The maximum score reachable **without** a Formulation Certificate is exactly 65 (100 − 35). That is why the 3-bolas band starts at 65 and spans 35 points — wide enough to keep differentiating *within* the category of own-formulation artisans.

| Bolas | Score | Label | What it means |
|---|---|---|---|
| 0 | 0–14 | Basic / industrial / franchise | No artisan signals, industrial product, or disqualified. Default when no evidence is given. |
| 1 | 15–29 | Verified artisan presence | Ice cream is the main product, address verified. |
| 2 | 30–64 | Accredited artisan | Editorial evidence of artisan production (video, own recipes, workshop photos). This is the **ceiling without a Formulation Certificate** — a maker who formulates but leans on semi-finished bases (a "parcial" verdict) also stays here. |
| 3 | 65–100 | Own-formulation artisan (trained · no industrial base) | Demonstrated own formulation: a verified FormulaMaps Formulation Certificate with verdict `produccion_propia_acreditada`, or a Fundador-approved documentary exception with a public written reason. Classic evidence (invoices, workshop video, own recipes) raises the artisanal subscore but **cannot reach this band alone**. |

## The six weighted dimensions

```
score = 100 × ( 0.35 × formulacion
              + 0.25 × artisanal_score
              + 0.15 × rating_norm
              + 0.10 × reviews_log
              + 0.10 × digital_score
              + 0.05 × base_score_norm )
```

| Dimension | Weight | How it's computed |
|---|---|---|
| `formulacion` | **35%** | Verdict of a verified FormulaMaps Formulation Certificate: `produccion_propia_acreditada` → 1.0 · `produccion_propia_parcial` → 0.5 · none/unverified/`sin_acreditar` → 0.0. A certificate whose `status` is not exactly `verificado` scores 0. See `subscore_algorithms.formulacion_subscore` in the JSON. |
| `artisanal` | 25% | Keyword match (strong/medium/weak, region-specific lexicon) + evidence boosts: verified certificate `acreditada` +0.60 / `parcial` +0.30, supplier invoice +0.30, workshop photo/video +0.15 (once), own recipes +0.05. Capped at 1.0. |
| `rating` | 15% | Linear from 3.0★→0.0 to 5.0★→1.0. |
| `reviews` | 10% | log10(reviews) / log10(3000), capped at 3000 reviews. Deliberately light — reviews measure popularity, not craft. |
| `digital` | 10% | Description >50 chars +0.30, ≥5 photos +0.40 (or ≥1 photo +0.20), Google place_id present +0.30. Website/Instagram inform editorial review but do not score numerically here. |
| `base_score` | 5% | Prior editorial history. **Always 0.0 for a fresh external/on-demand audit** — this is not something your agent can inflate. |

Full worked example (including the "same shop with/without a certificate" comparison), subscore formulas, and the exact multi-language keyword lists (es / it / en-US / en-UK / fr / pt / de / ja) live in [`eval-rubric-v1.8.json`](./eval-rubric-v1.8.json) → `subscore_algorithms` and `artisanal_keywords`.

## The third-bola gate — this is the load-bearing rule of v1.8

A shop cannot reach 65+ (3 bolas) on rating and reviews alone — Google inputs are capped at 25 points combined. The gate (`third_bola_requires_accredited_formulation`) requires ONE of:

- A verified FormulaMaps Formulation Certificate with verdict **`produccion_propia_acreditada`** (FormulaMaps' deterministic engine judges: more than half of the 8-10 core recipes are finished ice creams formulated *without* a semi-finished base). Verifiable at `formulamaps.com/cert/<id>`, no recipes disclosed.
- An explicit **Fundador-approved documentary exception** with a public, written reason (never granted for payment).

A `produccion_propia_parcial` verdict, or classic on-site evidence alone (workshop video/photos + artisan supplier invoices + own recipes), **caps at 2 bolas and score 64** — it raises the artisanal subscore but does not open the third bola by itself. This supersedes the v1.6 rule that accepted classic evidence as a standalone path to 3 bolas.

**Grandfathering:** ~414 Spanish shops published with 3 bolas before v1.7 (2026-09-05) are not retroactively re-audited by this change — they carry a "3 bolas · acreditación pendiente" label until **2027-03-31** and only drop if a re-audit proves the score was inflated.

## Franchise caps — production model, never the word "franchise"

The cap on a multi-location brand depends on **its production model**:

| Category | Cap | What it is | Examples |
|---|---|---|---|
| `industrial_franchise` | **1 bola**, fixed | Central factory, identical frozen product at every location. | Llaollao, Smöoy, Cold Stone Creamery, Baskin-Robbins, Häagen-Dazs standard stores |
| `premium_franchise` | **2 bolas**, liftable | Consistent quality, but semi-centralized production. | Amorino, Grom, Venchi, La Romana, Bacio di Latte, Rocambolesc |
| `artisan_franchise` | **no cap** (up to 3), audited per location | Chain/brand where every location has its own workshop and own recipes — includes regional artisan chains. | Salt & Straw, Jeni's, Van Leeuwen (US) · Cremela (Asturias, Spain) |

**The cap on `premium_franchise` is only lifted with documented on-site artisan production per location** (own workshop + own recipes, or a per-location verified certificate). Prestige, a celebrity chef's name, industry awards, or a peer recommendation do **not** lift it. Note that lifting this cap re-classifies the shop as `artisan_franchise` — it still needs the third-bola gate above (an accredited formulation verdict) to actually reach 3 bolas; classic evidence alone tops out at 2.

A separate negative cap, `industrial_base_core` (max 2 bolas, since v1.6), applies when a shop's product is built on industrial bases/mixes as its core. Transparently declared pure nut pastes or neutral stabilizers do **not** trigger it on their own — a "parcial" verdict goes to editorial review.

Two whitelists exist so you don't miscap shops that only *look* like chains:
- **Heritage trade names** (Spain): La Ibense, La Jijonenca, Los Valencianos, Llinares — independent artisans sharing a historical name from the Jijona (Alicante) tradition, not a franchise. Audit each individually.
- **US craft chains with in-house production**: Salt & Straw, Jeni's, Van Leeuwen, Humphry Slocombe — multiple locations, but each produces on-site with its own recipes. Not industrial. Audit each branch on local evidence.

## Regional lexicon — use the shop's language, not Spanish by default

The 6 dimensions and the franchise rules are universal. What changes by region is which words count as "artisanal" signals — see `artisanal_keywords` (8 language families) and `regional_equivalences` (cultural mapping, with named exemplar brands) in the JSON. A few explicit corrections (`bias_warnings`) exist because the rubric was authored in a Spain/Italy context:

- Do **not** penalize a shop for lacking "family tradition" or "X generations" — the US craft movement (2000s+) is fully artisan despite being young.
- Do **not** require the literal word "artisanal" — US shops say "craft"/"small batch"/"scratch-made", Japan says "手作り"/"工房", France says "artisanal"/"glacier artisan" (a legally protected label since 1996 — treat it as automatic evidence tier 2).
- A rotating seasonal menu is a **positive** artisan signal, not a lack of identity.

**Known gap:** `regional_equivalences` covers US, UK, IT, FR, ES, DE-AT-CH, PT-BR, JP-KR-TW. Other Spanish-speaking countries beyond Spain and Argentina don't yet have a dedicated entry: use the `es` keyword lexicon (Spanish-language, not Iberian-specific — "artesanal"/"elaboración propia"/"obrador" apply across Latin America), but tag `regional_context` with the shop's real ISO code so editorial can tell where it actually is. Raise a gap you find via `hola@gelatomaps.com` or a repo issue.

## Anti-gaming, in one paragraph

Since v1.8 this is structural, not just a policy: the maximum score reachable without an accredited Formulation Certificate is 65, so a rating ≥4.5 with thousands of reviews and no certificate simply cannot cross into 3 bolas — Google inputs (rating+reviews) are capped at 25 combined points by construction. A sudden review surge in under 30 days is still a flag for human review, not an auto-promotion. Shop owners cannot self-certify with a keyword; the certificate itself is third-party verifiable (deterministic, hash-checkable) and free to obtain. This is why every submission needs real evidence, not just a confident-sounding `rationale`.

## Conflict of interest, disclosed

GelatoMaps is built by an artisan ice cream-making family (Familia Llinares, Azuaga, Extremadura, Spain, since 1947), and their own shop appears as a rubric exemplar and is planned as the pilot's certificate no. 1. It is scored with the exact same public formula as everyone else — no exemption — any editorial delta is logged at `/api/v1/audit-log/<slug>`, and paying never alters bolas. The formulation certifier (FormulaMaps) is operated by the same founding family: the safeguard is that the certificate does not grant bolas on its own — its deterministic verdict enters the public formula as the `formulacion` dimension, identically for every shop, and a "parcial" verdict never opens the third bola regardless of payment. See `trust_contract` and `metadata.conflict_of_interest_disclosure` in the JSON for the full safeguards. Raise concerns at `hola@gelatomaps.com` or via a repo issue labeled `conflict-of-interest`.
