# ADR-0001: ToSMonitor-LLM ⊣ ToSArchiveGovernor -- a governed actor layered on this archive

- Status: Accepted (2026-07-24)
- Related: [`com-junkawasaki/root` ADR-2607110300](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607110300-cloud-itonami-lei-corporate-tos-catalog.edn)
  (the archive-only design this repo was created under -- unchanged by this
  ADR); [`com-junkawasaki/root` ADR-2607241900](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607241900-cloud-itonami-lei-tos-monitor-actor-pilot.edn)
  (the original 1-repo pilot, on `cloud-itonami-lei-2572ibtt8cczw6au4141`,
  P&G); [`com-junkawasaki/root` ADR-2607242600](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607242600-cloud-itonami-lei-tos-monitor-actor-batch10-round5.edn)
  (the fifth 10-repo validation batch this repo is part of).

## Context

This repository archives the publicly published Terms of Use of Royal
Caribbean Cruises Ltd., per ADR-2607110300 -- a read-only reference archive.
As part of a fifth 10-repo validation batch extending the
`cloud-itonami-lei-2572ibtt8cczw6au4141` pilot (see ADR-2607241900/
ADR-2607242600 for the full fleet-level design rationale), this repo gains a
governed actor layer on top of the unchanged archive.

Royal Caribbean Cruises Ltd. is the first Liberia-jurisdiction (LR) company
in this actor family. Liberia is a common flag-of-convenience corporate
domicile in the shipping/cruise industry, distinct from the company's
operational headquarters (Florida, USA) -- `:company/jurisdiction` in
`blueprint.edn` reflects legal domicile, not where the company is actually
run from. This distinction does not change the actor's design; it is noted
here because it is the reason this repo's `demo-full-text` mentions
governing-law jurisdiction explicitly (see Decision below).

## Decision

Identical design and code to the pilot and every other repo in this batch
(`src/tosmonitor/{governor,phase,operation,registry,advisor}.cljc` are
byte-for-byte identical across all of them) -- see ADR-2607241900 for the
full rationale of each of the six HARD governor checks, the single
always-escalate `:tos/change-proposal` actuation, and the mock-advisor-only
scope. Only `tosmonitor.store`'s company/baseline demo data is specific to
this repo:

- **Company**: Royal Caribbean Cruises Ltd., LEI K2NEH8QNVW44JIWK7Z55,
  website `https://www.royalcaribbean.com`.
- **Baseline provenance** (real, from this repo's own `80-data/public/
  tos.journal.edn`): source-url `https://www.royalcaribbean.com/legal`,
  retrieved-at `2026-07-10T00:00:00Z`, doc-type `:terms-of-service`.
- **Baseline full text**: a short, hand-written representative excerpt (not
  the real, nav-menu-heavy archived page), with a self-consistent SHA-256
  computed from that excerpt itself -- matching the pilot's own convention
  (ADR-2607241900), not a claim that this is the verbatim archived text. The
  excerpt notes (plausibly, without overstating legal specifics) that the
  guest cruise/passenger contract governing an actual voyage is typically
  construed under the company's operational-HQ jurisdiction rather than its
  Liberia corporate domicile.

The archive-of-record (`80-data/public/tos.journal.edn`) is never touched;
`commit-record!` only writes to this actor's own Store.

## Consequences

Same as the pilot (ADR-2607241900) and the batch (ADR-2607242600) --
proves the actor pattern holds for a company whose legal domicile
(Liberia) differs from its operational headquarters (Florida, USA), the
first such shape in this actor family.

## Run

```bash
clojure -M:dev:run     # walk a clean lifecycle + all six HARD-hold checks + a phase-0 hold + a backend swap
clojure -M:dev:test    # governor contract · phase invariants · store parity · advisor smoke
clojure -M:lint        # clj-kondo (errors fail; CI mirrors this)
```
