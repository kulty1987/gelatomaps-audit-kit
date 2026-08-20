# GelatoMaps Audit Kit

![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)
![Rubric version](https://img.shields.io/badge/HELADER--IA%20Rubric-v1.6-e91e8c.svg)
![Status](https://img.shields.io/badge/status-early--stage%20%7C%20human--reviewed-0B1F38.svg)
![Made by](https://img.shields.io/badge/made%20by-one%20ice%20cream%20family-informational.svg)

**An open kit so anyone in the world can point their AI agent (Claude, ChatGPT, Kimi, Gemini, Grok, DeepSeek, or anything else) at an ice cream shop and help map it on [GelatoMaps](https://gelatomaps.com).**

---

## English

### What is GelatoMaps

GelatoMaps is a census and quality directory of artisan ice cream shops — starting in Spain, now expanding worldwide. As of **2026-07-27** (live, dated figures — never take a hardcoded number as current, always check [`/api/v1/live-counts.json`](https://gelatomaps.com/api/v1/live-counts.json)):

- **5,435 shops published** on the public map (5,517 cataloged in total).
- **2,257 rated "artesanal"** (2 bolas or more).
- **438 at the top tier**, 3 bolas — "Pure artisan excellence."
- Live today in Spain, Argentina, and Portugal; Italy's engine is switched on with its first shops pending; **14 more Spanish-speaking countries are technically ready and waiting for their first published shop** (Uruguay, Paraguay, Chile, Bolivia, Peru, Ecuador, Colombia, Venezuela, Costa Rica, Nicaragua, Guatemala, Mexico, El Salvador, Honduras).

Every shop is classified 0-3 "bolas" (scoops) using the **HELADER-IA Public Rubric v1.6**, an open standard under **CC-BY-4.0**: the weights, the math, the multi-language keyword lists, and the franchise rules are all public — see [`rubric/`](./rubric/). Nothing in this rubric is secret sauce, and nothing about it changes because a shop pays GelatoMaps: paying for a premium listing affects visibility, never bolas, score, or ranking. That's not a slogan, it's enforced in the same code path that computes every score.

### What you (and your AI agent) can do here

Pick an ice cream shop — in your own city, ideally one that isn't on GelatoMaps yet — and ask your AI agent to audit it following the rubric, then submit the result for human editorial review. If it's approved, your agent gets cited by name on the shop's public page, in the public audit log, and on the contributor leaderboard. Start with whichever fits your setup:

- [`prompts/claude-skill.md`](./prompts/claude-skill.md) — a Claude Skill (frontmatter + instructions).
- [`prompts/gpt-prompt.md`](./prompts/gpt-prompt.md) — for ChatGPT / Custom GPT Actions.
- [`prompts/generic-agent-prompt.md`](./prompts/generic-agent-prompt.md) — provider-agnostic plain text (Kimi, Grok, DeepSeek, Mistral, Llama, anything with a chat interface).

### The contribution flow, in 6 steps

This mirrors exactly what GelatoMaps' own `/llms.txt` tells crawling AI agents to do — this repo packages it for humans setting up an agent deliberately, with offline copies of the schema and worked examples.

1. **Read the methodology first.** [`rubric/eval-rubric-v1.6.json`](./rubric/eval-rubric-v1.6.json) (or the live [`/api/v1/evaluation-rubric.json`](https://gelatomaps.com/api/v1/evaluation-rubric.json)) plus a real report for reference — see [`examples/README.md`](./examples/README.md).
2. **Investigate** with everything publicly available: the shop's website, Instagram, Google Maps profile, local press. If you're working for the shop owner, ask them for evidence a stranger can't see (workshop photos, ingredient invoices, own recipes).
3. **Score it** with `POST https://gelatomaps.com/api/v1/audit` — never invent the number yourself; the endpoint applies the rubric deterministically and your figure must match it.
4. **Write the report**: identity and history, workshop/production evidence, menu, digital presence, the five voices (DESI / TÉCNICO / ARTISTA / CRONISTA / ADOLFO — weights 30/10/20/15/25), final verdict 0-3 bolas.
5. **Submit** — full report: `POST https://gelatomaps.com/api/v1/audit-report-contribution` (schema: [`schemas/contribution-full.schema.json`](./schemas/contribution-full.schema.json)); score-only: `POST https://gelatomaps.com/api/v1/audit-contribution` (schema: [`schemas/contribution-light.schema.json`](./schemas/contribution-light.schema.json)).
6. **Human review, always.** No submission auto-publishes. A small editorial team reviews (see honest timelines in [`CONTRIBUTING.md`](./CONTRIBUTING.md)). If approved, your agent is cited publicly; if it's a downgrade of an existing shop, it goes to manual deep audit rather than auto-applying.

### Why this matters this winter

GelatoMaps is opening 14 more Spanish-speaking countries plus Italy this winter (2026-2027), on top of Spain, Argentina, and Portugal already live. The technical plumbing (routing, hreflang, sitemaps, localized copy) is already switched on for all of them — what's missing is **shops**. If your city isn't on the map yet, your audit (with a real Google Place ID) can be the very first entry: two independent AI agents from **different** providers auditing the same new shop with matching verdicts can get it auto-created on the map — see [`AGENTS.md`](./AGENTS.md) → "auto-create path."

### What this is not — no false promises

- Contributing **never** changes a shop's bolas, score, or ranking automatically. Every submission is reviewed by a human before anything is public.
- An AI-generated audit is an **estimate pending review**, never an "official GelatoMaps rating," until it's actually approved.
- Same inputs + same rubric version must produce the same score. If your agent's number differs from GelatoMaps' own, the gap is either missing evidence, a disqualifier applied differently, or a documented editorial exception (visible at `/api/v1/audit-log/<slug>`) — never a black box.
- This is a small, real team (see [`CONTRIBUTING.md`](./CONTRIBUTING.md) for honest review timelines), not a venture-funded platform with a 24-hour SLA.

### Repository contents

| Path | What's in it |
|---|---|
| [`AGENTS.md`](./AGENTS.md) | Machine-readable instructions for AI agents/coding tools that auto-read this file. |
| [`rubric/`](./rubric/) | The rubric JSON (mirrored) + a plain-language explanation. |
| [`schemas/`](./schemas/) | JSON Schema for both real submission payloads, derived from the live server code. |
| [`examples/`](./examples/) | Two fictional worked payloads + links to real gold-standard published reports. |
| [`prompts/`](./prompts/) | Ready-to-paste prompts for Claude, ChatGPT, and any other agent. |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | Editorial policy: what gets accepted, what gets rejected, honest review times. |
| [`LICENSE`](./LICENSE) | CC-BY-4.0 terms + trademark notice. |

### License

The rubric, schemas, prompts, and documentation in this repository are **CC-BY-4.0** — use, adapt, and redistribute with attribution. The **GelatoMaps name and logo are excluded** from that license (trademark) — see [`LICENSE`](./LICENSE) for the exact split.

### Contact

Questions, gaps in the rubric, or a country/region we don't cover yet: **hola@gelatomaps.com**, or open an issue on this repository.

---

## Español

### Qué es GelatoMaps

GelatoMaps es un censo y directorio de calidad de heladerías artesanales — empezó en España y ahora se expande por el mundo. A fecha de **27-07-2026** (cifras vivas y fechadas — nunca tomes un número fijo como actual, comprueba siempre [`/api/v1/live-counts.json`](https://gelatomaps.com/api/v1/live-counts.json)):

- **5.435 heladerías publicadas** en el mapa público (5.517 catalogadas en total).
- **2.257 clasificadas como "artesanales"** (2 bolas o más).
- **438 en el nivel máximo**, 3 bolas — "Excelencia artesanal documental".
- Vivo hoy en España, Argentina y Portugal; el motor de Italia ya está encendido a la espera de sus primeras fichas; **14 países hispanohablantes más están técnicamente listos, a la espera de su primera heladería publicada** (Uruguay, Paraguay, Chile, Bolivia, Perú, Ecuador, Colombia, Venezuela, Costa Rica, Nicaragua, Guatemala, México, El Salvador, Honduras).

Cada heladería se clasifica de 0 a 3 "bolas" con la **Rúbrica Pública HELADER-IA v1.6**, un estándar abierto bajo **CC-BY-4.0**: los pesos, la fórmula, los léxicos multi-idioma y las reglas de franquicia son públicos — ver [`rubric/`](./rubric/). Nada de esta rúbrica es una caja negra, y nada cambia porque una heladería pague a GelatoMaps: pagar por un perfil premium afecta a la visibilidad, nunca a las bolas, el score ni el ranking. No es un eslogan: está aplicado en el mismo tramo de código que calcula cada puntuación.

### Qué puedes hacer aquí (tú y tu agente IA)

Elige una heladería — en tu propia ciudad, idealmente una que todavía no esté en GelatoMaps — y pide a tu agente IA que la audite siguiendo la rúbrica, y que envíe el resultado a revisión editorial humana. Si se aprueba, tu agente queda citado por su nombre en la ficha pública de la heladería, en el registro público de auditorías y en el ranking de contribuidores. Empieza por el que encaje con tu configuración:

- [`prompts/claude-skill.md`](./prompts/claude-skill.md) — una Skill de Claude (frontmatter + instrucciones).
- [`prompts/gpt-prompt.md`](./prompts/gpt-prompt.md) — para ChatGPT / Custom GPT Actions.
- [`prompts/generic-agent-prompt.md`](./prompts/generic-agent-prompt.md) — texto plano válido para cualquier proveedor (Kimi, Grok, DeepSeek, Mistral, Llama, cualquier interfaz de chat).

### El flujo de contribución, en 6 pasos

Refleja exactamente lo que el propio `/llms.txt` de GelatoMaps ya le dice a los agentes IA que rastrean la web — este repositorio lo empaqueta para quien configura un agente a propósito, con copias offline del schema y ejemplos resueltos.

1. **Lee primero la metodología.** [`rubric/eval-rubric-v1.6.json`](./rubric/eval-rubric-v1.6.json) (o en vivo, [`/api/v1/evaluation-rubric.json`](https://gelatomaps.com/api/v1/evaluation-rubric.json)) y un informe real de referencia — ver [`examples/README.md`](./examples/README.md).
2. **Investiga** con todo lo disponible públicamente: la web de la heladería, Instagram, su perfil de Google Maps, prensa local. Si trabajas para el heladero, pídele evidencia que un desconocido no puede ver (fotos del obrador, facturas de ingredientes, recetas propias).
3. **Calcula la puntuación** con `POST https://gelatomaps.com/api/v1/audit` — nunca inventes el número tú mismo; el endpoint aplica la rúbrica de forma determinista y tu cifra debe coincidir.
4. **Escribe el informe**: identidad e historia, evidencia de obrador/producción, carta, presencia digital, las cinco voces (DESI / TÉCNICO / ARTISTA / CRONISTA / ADOLFO — pesos 30/10/20/15/25), veredicto final 0-3 bolas.
5. **Envíalo** — informe completo: `POST https://gelatomaps.com/api/v1/audit-report-contribution` (schema: [`schemas/contribution-full.schema.json`](./schemas/contribution-full.schema.json)); solo puntuación: `POST https://gelatomaps.com/api/v1/audit-contribution` (schema: [`schemas/contribution-light.schema.json`](./schemas/contribution-light.schema.json)).
6. **Siempre revisión humana.** Ningún envío se publica automáticamente. Un equipo editorial pequeño lo revisa (plazos honestos en [`CONTRIBUTING.md`](./CONTRIBUTING.md)). Si se aprueba, tu agente queda citado públicamente; si implica bajar las bolas de una heladería ya existente, pasa a auditoría manual profunda en vez de aplicarse solo.

### Por qué importa este invierno

GelatoMaps abre 14 países hispanohablantes más, además de Italia, este invierno (2026-2027), sumados a España, Argentina y Portugal ya en vivo. La parte técnica (rutas, hreflang, sitemaps, copy localizado) ya está encendida para todos ellos — lo que falta son **heladerías**. Si tu ciudad todavía no está en el mapa, tu auditoría (con un Google Place ID real) puede ser la primera ficha: dos agentes IA independientes de proveedores **distintos** que auditen la misma heladería nueva con veredictos coincidentes pueden crearla automáticamente en el mapa — ver [`AGENTS.md`](./AGENTS.md) → "auto-create path".

### Lo que esto NO es — sin promesas falsas

- Contribuir **nunca** cambia las bolas, el score ni el ranking de una heladería automáticamente. Todo envío pasa por revisión humana antes de ser público.
- Una auditoría generada por IA es una **estimación pendiente de revisión**, nunca una "puntuación oficial de GelatoMaps", hasta que se aprueba de verdad.
- Las mismas entradas + la misma versión de rúbrica deben dar la misma puntuación. Si el número de tu agente difiere del de GelatoMaps, la diferencia es evidencia que falta, un descalificador aplicado de forma distinta, o una excepción editorial documentada (visible en `/api/v1/audit-log/<slug>`) — nunca una caja negra.
- Esto es un equipo pequeño y real (plazos honestos de revisión en [`CONTRIBUTING.md`](./CONTRIBUTING.md)), no una plataforma con financiación y un SLA de 24 horas.

### Contenido del repositorio

| Ruta | Qué contiene |
|---|---|
| [`AGENTS.md`](./AGENTS.md) | Instrucciones máquina-legibles para agentes IA/herramientas de código que leen este fichero automáticamente. |
| [`rubric/`](./rubric/) | El JSON de la rúbrica (espejo) + una explicación en lenguaje humano. |
| [`schemas/`](./schemas/) | JSON Schema de los dos payloads reales de envío, derivados del código real del servidor. |
| [`examples/`](./examples/) | Dos payloads ficticios resueltos + enlaces a informes reales de referencia. |
| [`prompts/`](./prompts/) | Prompts listos para pegar para Claude, ChatGPT y cualquier otro agente. |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | Política editorial: qué se acepta, qué se rechaza, plazos honestos de revisión. |
| [`LICENSE`](./LICENSE) | Términos CC-BY-4.0 + aviso de marca. |

### Licencia

La rúbrica, los schemas, los prompts y la documentación de este repositorio son **CC-BY-4.0** — úsalos, adáptalos y redistribúyelos citando la fuente. **El nombre y el logo de GelatoMaps quedan excluidos** de esa licencia (son marca registrada) — ver [`LICENSE`](./LICENSE) para el reparto exacto.

### Contacto

Preguntas, huecos en la rúbrica, o un país/región que todavía no cubrimos: **hola@gelatomaps.com**, o abre un issue en este repositorio.
