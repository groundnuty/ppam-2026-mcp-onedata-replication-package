# Threats to validity

The PPAM proceedings convention has no dedicated Threats section, so this discussion was cut from the camera-ready paper. The five threats below preserve the rigour for the planned FGCS extension and for reviewer-grade audit.

## 1. Single federation

The n = 8 sweep ran on a single Onedata federation (`cloud-pl` + `Cloud-SK`). Results characterise the LLM-agent / federated-namespace interface, not federations of larger size, different geography, or different administrative-domain composition. Results on a 5-provider AT/DE/PL/SK federation, on S3 with IAM, or on POSIX with LDAP may differ. Comparative measurements are left to future work.

## 2. n = 8 stochasticity

At *n* = 8 trials per cell, cell-level Wilson 95 % CI half-widths are bounded above by 0.34. Per-band intervals tighten to 0.13 at *n* = 144 (one band, all seven LLMs); per-LLM all-bands intervals to 0.08 at *n* = 144. The cell-stability decomposition in `stochasticity_appendix.md` surfaces the n = 8 stochastic cells explicitly, so readers can identify which (LLM, scenario) cells would benefit from *n* ≥ 16 to stabilise the per-cell estimate.

## 3. Closed-model reproducibility

The Anthropic Claude Sonnet 4.5 leg runs through `claude-agent-sdk` on a local Claude Code session against Anthropic's API. Reviewers may reasonably ask whether the Sonnet results are reproducible without Anthropic infrastructure; the answer is no, but Sonnet is the frontier reference, not a candidate for production deployment in the paper's scope. The six open-weight legs are reproducible end-to-end without proprietary infrastructure.

## 4. Federation eventual-consistency

Onedata 25.0's `dbsync` eventual-consistency window introduces millisecond-to-second windows during which oracle reads may observe pre-convergence state. We mitigate via the federation-reset protocol's `RESET_HARD_CAP=120s` and three-attempt retry-with-backoff (see `methodology_appendix.md`), and via the two-axis OracleResult that lets P3 dynamic-tier trials be scored on `mcp_pass` even when `federation_pass` has not converged within the 60-second polling deadline. The eventual-consistency reality is documented; a follow-up `dbsync` calibration sweep against geographically distant Oneproviders (50 trials, p99 + 20 % margin) would refine the cap.

## 5. Tool-call schema bias

The MCP tool schemas were authored against Onedata's REST surface; they reflect what Onedata exposes and what the n = 8 sweep needed. Other federations expose different operations (S3 + IAM, POSIX + LDAP, OneFS access zones), and a curated MCP surface for those substrates would have a different shape. The methodology — two-axis OracleResult, federation-reset protocol, multi-LLM panel composition, per-LLM-space architecture — is reusable across substrates; the specific 16-tool curation is not.
