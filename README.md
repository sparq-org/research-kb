# sparq Knowledge Base dump (`research-kb`)

> 🤖 **SPARQ agent** — this repository is maintained by an automated process
> running on the [sparq](https://github.com/sparq-org/sparq) project.
> See [sq-tzars.8] in the bead tracker for the design record.

This repository holds versioned, tier-aware exports of the **sparq Project Knowledge
Graph (PKG)** — the project's self-improving knowledge base of research literature,
engineering decisions, tool findings, and task data.

---

## Repository status

**PRIVATE** — this repository is intentionally private until the maintainer has
reviewed the tier-enforcement guarantees (zero restricted-tier content or secrets in
public artifacts) and explicitly flips visibility.  The mechanical leak check is
implemented in `scripts/export-kb-dump.py` and is run as a mandatory gate on every
dump before push.

---

## Tier semantics

Each dump is tier-aware: KB statements are partitioned into three named tiers
(byte-pinned IRIs in `crates/sparq-kb/src/vocab.rs`).

| Tier | Graph IRI | Exported as | Description |
|---|---|---|---|
| **hand-authored** | `pkg/graph#hand-authored` | `pkg-hand-authored.ttl.gz` | Human-curated PKG data: task projections from the bd tracker, skill front-matter, and hand-authored AGENTS.md findings (output of `ingest_pkg.py`). |
| **machine** | `pkg/graph#machine` | `pkg-machine.ttl.gz` | Literature pipeline output for sources with a known redistributable licence (CC-BY, CC0, public-domain). Machine-tier findings are capped at confidence ≤ 0.7 and assurance ≤ Conjectured. |
| **restricted** | `pkg/graph#license-restricted` | **NEVER EXPORTED** | Machine findings from sources with unknown or non-redistributable licences. Only its metadata-only public projection is included. |
| *restricted projection* | `pkg/graph#license-restricted` | `pkg-restricted-projection.ttl.gz` | Metadata-only public view of the restricted tier: source IRI, title, year, and licence status. Zero abstract-derived text. |
| **ontology** | `https://sparq.dev/ns/pkg#` | `pkg-ontology.ttl.gz` | The PKG vocabulary (`ontology/pkg/pkg.ttl`). |

The **LICENSE-RESTRICTED tier is never exported** in full.  Only its metadata-only
public projection ships in this repository.  The projection carries zero
abstract-derived text (`sigimpl:justification`, `dcterms:abstract`, `pkg:Finding`
triples are all absent by construction and asserted at export time by a mandatory
leak check).

---

## Provenance of dumps

Each dump is produced by `scripts/export-kb-dump.py` (the sparq repo, bead
[sq-tzars.8]) and pushed here by the `kb-dump.yml` GitHub Actions workflow.

Each dated subdirectory (`dumps/YYYY-MM-DD/`) contains:

- `*.ttl.gz` — compressed Turtle tier artifacts
- `manifest.json` — dump version, source commit, per-file tier + licence class +
  approximate triple count + SHA-256 of the uncompressed content
- `dump-provenance.ttl` — a `prov:Activity` record for the dump itself, with
  `prov:generatedAtTime`, `prov:used` (tier graph IRIs), and a back-link to the
  source commit

---

## Licence posture

The hand-authored tier and ontology are part of the sparq project
(MIT licence, `https://github.com/sparq-org/sparq`).

The machine tier contains findings derived from openly-licensed sources only
(CC-BY, CC0, public-domain; see the `licenseStatus` field in each source's
metadata).  The restricted tier, which covers sources with unknown or
non-redistributable licences, is **never exported** to this repository.

Consult `manifest.json` → `files[].license_class` for the per-file licence class
and `tier_semantics` for a plain-English description of each tier's provenance.

---

## Attribution

Built by the [sparq](https://github.com/sparq-org/sparq) agent pipeline.
🤖 SPARQ agent · [sq-tzars.8] · `scripts/export-kb-dump.py` · `[SONNET-4.6]`
