# Replication package — *LLM-agentic access to a federated scientific data layer with Onedata* (PPAM 2026)

This repository is the **replication package** that accompanies the paper

> M. Orzechowski, R.G. Słota, M. Maślak, Ł. Dutka, J. Kitowski.
> *LLM-agentic access to a federated scientific data layer with Onedata.*
> Proceedings of PPAM 2026.

It contains everything a reader needs to (a) audit any cited empirical claim against the source material, (b) re-run the headline `K = 8` sweep against an equivalent Onedata federation, and (c) inspect any of the 1008 trials individually.

The **server code, benchmark harness, oracles, federation-reset protocol, and rescore tooling** live in a separate repository pinned at a specific commit; this repository contains the **artefacts that commit produced**, the **appendices the paper had to cut** to fit the PPAM 15-page budget, and the **reproducibility runbook** that ties the two together.

## Repository structure

```
ppam-2026-mcp-onedata-replication-package/
├── README.md                          ← this file
├── LICENSE                            ← CC-BY-4.0 (text + data) and pin to upstream code licence
├── CITATION.cff                       ← cite-this-repo metadata
│
├── appendices/                        ← the seven text appendices the PPAM paper cites
│   ├── scenarios.md                   ← all 18 benchmark scenarios, verbatim briefs + fixtures + pass conditions + fail modes
│   ├── methodology_appendix.md        ← per-LLM-space architecture, federation-reset 5-phase protocol, oracle tiers, reliability metrics
│   ├── server_design_findings.md      ← M-1..M-13 server-design issues organised in 7 themes
│   ├── threats_appendix.md            ← five threats-to-validity paragraphs (single federation, K=8 stochasticity, closed-model reproducibility, eventual consistency, tool-call schema bias)
│   ├── stochasticity_appendix.md      ← stable/stochastic cell decomposition + tool-removal feasibility K-counts
│   ├── positioning_tables.md          ← Tables 1 & 2 (positioning vs the 2025 MCP-benchmark wave) cut from PPAM, slated for the FGCS extension
│   └── oracle_pseudocode.tex          ← Python verifier archetypes for D1 / A2 / P3 / P4 oracle tiers
│
├── runbook/
│   └── RUNBOOK.md                     ← reproducibility runbook: federation provisioning, model endpoints, vLLM launch flags, run commands, test-suite invocation, audit-trail layout
│
├── artefacts/
│   └── 20260503T002305_k8/            ← the K=8 headline run, 1008 trials
│       ├── README.md                  ← JSONL schema, rescore protocol summary, paper-trial cross-reference
│       ├── sweep-k8.log               ← full driver log
│       ├── _per-step-logs/            ← 9 per-step logs (per-LLM K=8 + V4-Pro probe + report)
│       ├── <llm>__<scenario>.jsonl              ← 126 as-measured trial files (1008 trials total)
│       └── <llm>__<scenario>.rescored.jsonl     ← 126 rescored sidecars with parser-bug + deployment-artefact lift annotations
│
└── code-pin/
    └── README.md                      ← the MCP server, harness, and tooling live at
                                          `groundnuty/onedata-mcp@ppam2026/14-tools@08e201b`;
                                          this file documents the pin and how to verify it
```

## How to navigate

### "I want to verify a specific claim in the paper"

Start with the **paper section** the claim is in, then resolve the citation:

| Paper claim refers to… | Resolve here |
|---|---|
| Verbatim brief for scenario `D1` / `A2` / `P3` / ... | [`appendices/scenarios.md`](appendices/scenarios.md) |
| Per-LLM-space architecture, federation-reset protocol, oracle tiers, $\mathrm{pass}^k$ metric | [`appendices/methodology_appendix.md`](appendices/methodology_appendix.md) |
| One of the 13 MCP-server design issues (M-1..M-13) | [`appendices/server_design_findings.md`](appendices/server_design_findings.md) |
| A threat to validity / scope qualifier | [`appendices/threats_appendix.md`](appendices/threats_appendix.md) |
| Stable / stochastic cell counts (Table 9) or the tool-removal $K_D, K_A, K_P$ feasibility numbers | [`appendices/stochasticity_appendix.md`](appendices/stochasticity_appendix.md) |
| A capability-gap pattern (C-1..C-5) — a specific failing trial | [`artefacts/20260503T002305_k8/<llm>__<scenario>.rescored.jsonl`](artefacts/20260503T002305_k8/) — pick a `failure_category != null` record |
| The pinned commit that produced the artefacts | [`code-pin/README.md`](code-pin/README.md) |
| How to set up Onedata + endpoints + commands to reproduce the sweep | [`runbook/RUNBOOK.md`](runbook/RUNBOOK.md) |

### "I want to inspect the per-trial data directly"

Each cell in the per-(LLM, scenario) reliability grid corresponds to one JSONL file with 8 trials. Open `<llm>__<scenario>.rescored.jsonl` — every line is one trial, with the agent's full tool-call sequence, final answer, and a two-axis oracle verdict. The schema is documented in [`artefacts/20260503T002305_k8/README.md`](artefacts/20260503T002305_k8/README.md).

Quick `jq` patterns:

```bash
# Count the post-clean pass rate across the full sweep
jq -s 'map(select(.outcome_v2 == "PASS")) | length' artefacts/20260503T002305_k8/*.rescored.jsonl
# → 956

# Per-LLM pass count
for f in artefacts/20260503T002305_k8/*.rescored.jsonl; do
  llm=$(basename "$f" | cut -d_ -f1-3)
  jq -s 'map(select(.outcome_v2=="PASS")) | length' "$f"
done | ... # roll-up

# All Granite P1 trials (the C-3 stable-fail cell)
jq '{trial_ix, outcome_v2, oracle_diagnosis_v2}' \
   artefacts/20260503T002305_k8/granite-4.1-30b__P1.rescored.jsonl

# Trials lifted by the parser-bug rescore
jq -s 'map(select(.lift_kind=="parser_bug")) | length' \
   artefacts/20260503T002305_k8/*.rescored.jsonl
# → 20

# Trials lifted by the deployment-artefact rescue
jq -s 'map(select(.lift_kind=="deployment_l3_granite")) | length' \
   artefacts/20260503T002305_k8/*.rescored.jsonl
# → 10
```

### "I want to re-run the K=8 sweep"

The full procedure — Onedata-federation provisioning, model endpoints, vLLM launch flags, environment variables, harness invocation, and rescore — is in [`runbook/RUNBOOK.md`](runbook/RUNBOOK.md). Approximate cost: an Onedata federation reachable from the harness machine, API keys for any commercial-LLM legs being reproduced, and ≈ 7.5 h of sequential wall-time (or ≈ 1 h if the panel can be parallelised without rate-limit caps).

## Cleaning protocol — three layers

The headline figure cited in the paper (956 / 1008 = 94.8 %) is the **post-clean** rate. Two non-destructive lift passes are applied to the as-measured oracles to separate **model-capability failures** from **oracle-parser bugs** and **deployment-layer artefacts** — none of which the paper's measurement question is about.

| Layer | Rate | What this isolates |
|---|---|---|
| **L0 — as-measured** | 926 / 1008 = 91.9 % | Whatever the live oracle on the live federation reported at trial time |
| **L1 — + parser-bug lift** | 946 / 1008 = 93.8 % | Three fixes in `benchmark/oracles/_helpers.py` (case-insensitive key match, 3-column-table tolerance, JSON-shape answer handling) — 20 trial lifts |
| **L2 — + deployment-artefact lift (`<tool_call>` markup leak)** | 956 / 1008 = 94.8 % | L-3 vLLM tool-call-parser leak on Granite — 10 trial lifts |
| Remaining (unlifted) failures | 52 trials | The substantive model-capability signal — five capability-gap patterns (C-1..C-5) in paper §5 |

The lift logic is strictly one-way (FAIL → PASS only); a rescored evaluation that would demote a passing trial is refused and recorded in `rescore_demotion_rejected`. Originals are preserved alongside the rescored sidecars; nothing is overwritten. See [`artefacts/20260503T002305_k8/README.md`](artefacts/20260503T002305_k8/README.md) for the field-level detail.

## What is **not** in this package

- The MCP server source, harness, scenarios, oracles, and rescore tooling — those live at [`groundnuty/onedata-mcp@ppam2026/14-tools@08e201b`](https://github.com/groundnuty/onedata-mcp). See [`code-pin/README.md`](code-pin/README.md).
- The Onedata federation deployment manifests (Kubernetes, Argo) — those live at `groundnuty/spice-deployments` (separate engineering repo).
- The paper LaTeX sources — separate repository.
- Funding-source / institutional acknowledgements — see the paper itself.

## Further reading

- The methodological pattern of [three-layer data cleaning with lift-only sidecars](appendices/methodology_appendix.md) generalises beyond this benchmark to any multi-LLM sweep that mixes oracle quality and model capability.
- The [per-LLM-space architecture](appendices/methodology_appendix.md#per-llm-space) generalises to any filesystem-like substrate with logical-isolation primitives.

## Citing this package

See [`CITATION.cff`](CITATION.cff). The package is registered under the same DOI / version as the paper itself once the camera-ready is accepted; until then please cite both the paper and this repository's permalink commit.

## Licence

[`LICENSE`](LICENSE): CC-BY-4.0 for the textual content and data in this repository. The upstream MCP-server code at the pinned commit is governed by its own repository's licence (see [`code-pin/README.md`](code-pin/README.md)); contributors and reviewers should check that licence before redistribution.
