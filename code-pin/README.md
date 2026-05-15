# Code pin — what produced the artefacts in this package

The MCP server, the benchmark harness, the 18 scenarios, the federation-reset protocol, the oracles, and the rescore tooling all live in a **separate repository**, pinned at the commit that produced the artefacts in [`../artefacts/20260503T002305_k8/`](../artefacts/20260503T002305_k8/).

## Pin

| | |
|---|---|
| **Repository** | https://github.com/groundnuty/onedata-mcp |
| **Branch** | `ppam2026/14-tools` |
| **Commit** | **`08e201b`** |
| **Date** | 2026-05-04 |

## What is in the pinned commit

- `onedata_mcp/` — the 16-tool MCP server (interface modules + REST API client layer + ASGI middleware)
- `benchmark/` — the harness, panel composition, 18 scenarios, oracles, federation reset, rescore script
- `test/` — 203 unit tests (`make test`)
- `Makefile` — the canonical operational interface (`make help` for the full menu)
- `research/` — empirical findings: M-1..M-13 server-design issues, L-1..L-3 LLM-output-stability findings, scenario catalogue
- `design/` — design records: tool-allowlist curation, move_file strategy, query_by_metadata recursive-primitives choice, scenario authoring decisions, reset protocol, oracle philosophy
- `IMPLEMENTATION_NOTES.md` — endpoint mapping, three corrections vs. paper §3 spec
- `replication/` (paper-side) — the same appendix-style files mirrored here under [`../appendices/`](../appendices/) and [`../runbook/`](../runbook/)

## Why is the code in a separate repo

Two reasons:

1. **The MCP fork is also an upstream engineering project** — it inherits from `M0rgho/onedata-mcp` and may continue to evolve after this paper. Pinning a commit (rather than vendoring the code into this replication package) preserves the upstream contribution path.
2. **Size and clarity** — the engineering repo includes test fixtures, generated artefacts, and design records that have value for the upstream community but would clutter the replication package.

The trade-off is one extra `git clone` for reviewers who want to re-run the sweep; it is documented in [`../runbook/RUNBOOK.md`](../runbook/RUNBOOK.md).

## Verifying the pin

```bash
git clone https://github.com/groundnuty/onedata-mcp.git
cd onedata-mcp
git checkout 08e201b
git log -1 --pretty='%h  %ad  %s' --date=short
# expected: 08e201b  2026-05-04  docs: detailed model-failure analysis for paper (52 trials)
```

## Verifying that this commit produced these artefacts

The `run_id` recorded in every line of every `*.jsonl` in [`../artefacts/20260503T002305_k8/`](../artefacts/20260503T002305_k8/) is `20260503T002305_k8`. The sweep that produced it began on **2026-05-03 00:23:05 UTC**, one day before the pinned commit was tagged. The commit batches three things — the rescore protocol, the K=8 driver, and the paper-prep documentation — but the artefacts themselves were materialised one day earlier by a commit reachable from `08e201b`. The driver script at `08e201b` reproduces an equivalent run.

## Where the code went after `08e201b`

The branch is left frozen at `08e201b` for replication-purposes; subsequent work (paper-side polish, L-3 vLLM-devops fix, FGCS-extension scoping) will happen on follow-on branches and will not move the replication pin.
