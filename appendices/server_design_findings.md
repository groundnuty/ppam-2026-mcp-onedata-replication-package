# Server-design findings (M-1 .. M-13)

This appendix carries the full server-design taxonomy. The camera-ready PPAM paper retains a one-sentence reference in §6; the depth-level catalogue below is reserved for the planned FGCS journal extension.

Running seven independently trained LLMs over an iterative sequence of MCP-server design choices revealed **thirteen** issues that 203 unit tests and a read-only smoke check had not exposed — because none of those tests exercised the cross-tool reasoning an LLM agent performs against live federation state. Eleven of the thirteen issues cluster into **seven design themes**; the remaining two are environment-level findings tracked as cross-references at the end.

The full upstream record of each finding (root cause, evidence, fix commit) lives in the engineering repo at `research/empirical-mcp-server-findings.md` — see the pin in [`../code-pin/README.md`](../code-pin/README.md).

## The seven design themes

### Theme 1 — Path normalisation (M-1, M-6, M-12)

Onedata's REST API mixes **absolute** (`/<space>/<path>`) and **relative** (`<path-under-listed-root>`) path forms across endpoints. An LLM agent that reads a relative path from one tool's response and feeds it back into another tool's input falls into a quiet failure mode: the second call resolves against the wrong root and silently returns zero matches or an HTTP 404.

The wrapper now normalises every tool's path output to the absolute form (M-1 in `query_by_metadata`; M-6 in `list_files_recursively`) and disambiguates the `prefix` parameter as relative-only with a docstring tightening (M-12). Three of the panel's weaker legs hit M-12 in the absolute-prefix form on D2 (silent zero-match) before the docstring fix; none hit it after.

### Theme 2 — Response-shape design for smaller-model accommodation (M-5, M-7)

Onedata's `getFileDistribution` returns a `distributionPerProvider` dictionary keyed by hex provider IDs (M-5); its `getFileQosSummary` returns `{qosId: status}` flat with no per-rule details (M-7). On both, smaller-capacity models in our panel skipped the chained name-resolution or rule-detail call and answered with the wrong shape.

The wrapper now **joins** the provider name in-line on the distribution response and **inlines** per-rule `expression` and `replicas_num` on the QoS summary. Both fixes preserve the original flat-shape fields under `requirements_flat` for backwards compatibility. Larger panel legs (Sonnet, V4-Pro) reliably executed the chained calls and are unaffected by the response-shape change; the smaller legs (Granite, GLM) flip from FAIL to PASS on P1 and P6 after the join.

### Theme 3 — Parameter-name fidelity (M-3, M-4)

Two parameters that ought to be polymorphic instead reject the natural spelling.

- **M-3** — `space_id` on `list_space_providers` and `list_space_transfers` accepts only the hex space-id. Four of five panel models passed the human-readable name from the brief and got HTTP 403.
- **M-4** — `metadata_type` on `set_file_metadata` accepts `{json, rdf, xattrs}` only. Multiple models read "custom JSON metadata" in the brief and faithfully passed `metadata_type="custom"`, which yields HTTP 404 from a path-construction failure.

Both were fixed by polymorphism: `space_id` now accepts name or hex; `metadata_type="custom"` aliases to `json`.

### Theme 4 — Directory primitive (M-11)

Without a dedicated `create_directory` tool, agents asked to set up a target directory before placing a file in it fell into a structural trap: `create_file(path="archive", content="", create_parents=True)` silently creates a *regular file* at `archive`, after which every `archive/<x>` subsequent operation fails with `ENOTDIR`. Two of the panel's weaker legs hit this on A4.

The fix is three coordinated changes: a new `create_directory` tool exposed in the ablation surface, a default flip of `create_parents` to `True`, and a defensive error in `create_file` that triggers when `content` is empty and the basename has no recognisable file extension.

### Theme 5 — Byte-count out-of-band (M-10)

`download_file` originally returned the raw content; agents asked for "byte count of file X" computed `len(content)`, which counts *characters after UTF-8 decode* rather than bytes. Three of four panel LLMs gave wrong byte counts on D3 despite reading the right file.

The fix returns a structured envelope — `{content, size_bytes, content_type}` — with `size_bytes` computed server-side. Backwards-compatible: agents that destructure `.content` keep working.

### Theme 6 — Docstring tightening for federation-specific operands (M-8)

Onedata accepts arbitrary `key=value` operands in QoS expressions, but the working set is per-federation: this federation has no admin-set storage attributes, so expressions like `country=PL`, `type=ssd`, `geo=EU` that LLMs trained on generic Onedata documentation confidently emit will create rules in permanent `impossible` status.

The fix is a docstring tightening that names the always-safe operands (`providerId`, `storageId`, `anyStorage`), flags the admin-attributed family as federation-specific, and explicitly calls out common fictional operand names to avoid. The n = 8 sweep still showed several A5 trials emitting `cloud=EU` (this is capability-gap pattern C-2 in the paper's §5), so the docstring tightening is necessary but not sufficient.

### Theme 7 — Transport hardening (M-13, resolved)

The HTTP transport mode of the MCP server originally accepted requests with arbitrary `Host` and `Origin` headers — a DNS-rebinding vulnerability that the `modelcontextprotocol/conformance` suite's `dns-rebinding-protection` scenario detects.

We added FastMCP ASGI middleware that enforces `Host`/`Origin` allow-listing, and the conformance suite now reports **2 / 2 PASS** on the rebinding scenario. The stdio-transport mode used by the n = 8 sweep through `claude-agent-sdk` was unaffected (HTTP headers do not apply to the stdio path), but operators running the server via HTTP for Inspector tooling must now apply the middleware fix or remain on the stdio path.

## Cross-references — M-2 and M-9

Two findings in the upstream M-1 .. M-13 taxonomy are **environment-level** rather than server-design-level. They are documented here only as cross-references.

- **M-2 — Cross-scenario fixture pollution.** Scenario-authoring vs harness-reset granularity mismatch: A6's brief originally asked for a space-scoped `query_by_metadata`, while the harness's per-trial reset is subtree-scoped, so prior scenarios' fixtures leaked into A6's response. The fix is scenario re-authoring (A6's brief now subtree-scopes the query), not a server change.
- **M-9 — Upstream-service observation.** Inactive PLGrid Forge models still appear in `/v1/models`, observed during multi-LLM panel setup. Tracked for completeness but out of scope for the MCP-server taxonomy.

The eleven server-design issues plus these two cross-references make up the thirteen total findings surfaced by the multi-LLM sweep.
