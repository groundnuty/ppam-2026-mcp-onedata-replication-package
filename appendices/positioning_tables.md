# Positioning tables (cut from PPAM 2026, reserved for FGCS extension)

Both tables were dropped from the PPAM 2026 paper on 2026-05-15 to fit the strict 15-page limit. The prose descriptions in §2 of the camera-ready paper enumerate the same benchmarks and prior work in narrative form. These tables are preserved here for the FGCS journal extension, which has the page budget to support them.

## Table 1 — Positioning vs the 2025 MCP-benchmark wave

Columns: substrate covered, tool count, LLMs evaluated, oracle methodology, federation scope, and **L/M** = whether servers are live or LLM-generated mocks.

Shorthand:

- *rule + rubric LLM* — rule-based plus rubric-driven LLM-as-judge scoring
- *fmt / stat / dyn* — format / static / dynamic oracle taxonomy of Luo et al.
- *TFS / TEFS* — Liu et al.'s Task Final Score and Task Execution Final Score
- *acc + OR* — accuracy plus Overhead Ratio
- *exec det* — deterministic execution-based oracles (this work)

| Paper                   | Substrate              | Tools  | LLMs | Oracle             | Federation       | L/M  |
|-------------------------|------------------------|:------:|:----:|--------------------|------------------|:----:|
| MCP-Bench               | 28 servers, 11 domains | 250    | 20   | rule + rubric LLM  | none             | live |
| MCP-Universe            | 11 servers, 6 domains  | 133    | 16   | fmt / stat / dyn   | none             | live |
| MCPGauge                | 30 suites              | varies | 6    | acc + OR           | none             | live |
| Liu MCPAgentBench       | 9 714 servers          | 20 k+  | 11   | TFS / TEFS         | none             | mock |
| Guo MCP-AgentBench      | 33 servers             | 188    | 10   | Sonnet 3.7 judge   | none             | live |
| **Ours**                | **1 MCP, federated**   | **16** | **7**| **exec det**       | **cross-admin**  | **live** |

## Table 2 — Positioning vs the scientific-data-MCP cluster

| Paper             | Substrate                              | Federation       | Evaluation                          | Contribution                        |
|-------------------|----------------------------------------|------------------|-------------------------------------|-------------------------------------|
| Pan & Chard 2025  | 7 MCP/Globus + ALCF/NERSC              | single domain    | 4 case studies + Recall@k           | position paper + implementations    |
| Kamatar 2025      | actor model / Globus (no MCP)          | single domain    | microbenchmark + 4 case studies     | federated-compute middleware        |
| **Ours**          | **16-tool MCP / Onedata**              | **cross-admin**  | **7 LLMs × 18 tasks × n = 8**         | **curated MCP + benchmark**         |
