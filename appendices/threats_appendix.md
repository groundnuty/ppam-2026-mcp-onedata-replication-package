# Threats to validity

*Cut entirely from PPAM submission per PPAM convention (no Threats section in PPAM proceedings papers). The full 5-paragraph treatment below preserves the rigour for the FGCS extension and for reviewer-grade audit.*

## Five threats (LaTeX-source verbatim)

```latex
\section{Threats to validity}
\label{sec:threats}

\paragraph{Single federation.} The K=8 sweep ran on a single Onedata federation (\texttt{cloud-pl} + \texttt{Cloud-SK}; SPICE-platform). Results characterise the LLM-agent--federated-namespace interface, not federations of larger size, different geography, or different administrative-domain composition. Results on a 5-provider AT/DE/PL/SK federation, an S3-on-IAM federation, or a posix-on-LDAP federation may differ; comparative measurements are left to future work.

\paragraph{K=8 stochasticity.} At $n = 8$ trials per cell, cell-level Wilson 95\% CI half-widths are bounded above by $0.34$. Per-band intervals tighten to $0.13$ at $n = 144$ (one band, all 7 LLMs); per-LLM all-bands intervals to $0.08$ at $n = 144$. The cell-stability decomposition of Section~\ref{sec:stochastic} surfaces the K=8 stochastic cells explicitly; readers can identify which (LLM, scenario) cells would benefit from $K \geq 16$ to stabilise the per-cell estimate.

\paragraph{Closed-model reproducibility.} The Anthropic Claude Sonnet 4.5 leg runs through the \texttt{claude-agent-sdk} on a local Claude Code session against Anthropic's API. Reviewers may reasonably ask whether the Sonnet results are reproducible without Anthropic infrastructure; the answer is no, but Sonnet is the frontier reference, not a candidate for production deployment in the paper's scope. The six open-weight legs are reproducible end-to-end without proprietary infrastructure.

\paragraph{Federation eventual-consistency.} Onedata 25.0's \texttt{dbsync} eventual-consistency window introduces millisecond-to-second windows during which oracle reads may observe pre-convergence state. We mitigate via the federation-reset protocol's \texttt{RESET\_HARD\_CAP=120\,s} and three-attempt retry-with-backoff (Section~\ref{sec:methodology}), and via the two-axis OracleResult that lets P3 dynamic-tier trials be scored on $\mathit{mcp\_pass}$ even when $\mathit{federation\_pass}$ has not converged within the 60-second polling deadline. The eventual-consistency reality is documented; a follow-up dbsync-calibration sweep against geographically distant Oneproviders (50 trials, p99 + 20\% margin) would refine the cap.

\paragraph{Tool-call schema bias.} The MCP tool schemas were authored against Onedata's REST surface; they reflect what Onedata exposes and what the K=8 sweep needed. Other federations expose different operations (S3+IAM, posix+LDAP, OneFS access zones), and a curated MCP surface for those substrates would have a different shape. The methodology --- two-axis OracleResult, federation-reset protocol, multi-LLM panel composition, per-LLM-space architecture --- is reusable across substrates; the specific 16-tool curation is not.

% ============================================================
```
