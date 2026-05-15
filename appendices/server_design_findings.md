# Server-design findings (M-1..M-13)

*Cut from §6.1 of an earlier 28-page draft of the PPAM paper. The PPAM paper retains a single-paragraph summary in §6 Conclusions and Future Work; the full taxonomy below feeds into the planned FGCS journal extension.*

Running seven independently trained LLMs over an iterative sequence of MCP-server design choices revealed thirteen issues that 158 unit tests and a read-only smoke check had not exposed --- because none of those tests exercised the cross-tool reasoning an LLM agent performs against live federation state. The thirteen issues cluster into seven design themes.

The full upstream record of each finding (root cause, evidence, fix commit) lives at:
`onedata-mcp-ppam2026/research/empirical-mcp-server-findings.md`

## Seven design themes (LaTeX-source verbatim)

```latex
\section{Findings}
\label{sec:findings}

Beyond the headline reliability numbers, the K=8 sweep surfaced two classes of substantive findings that single-LLM benchmarking would have missed: server-design lessons that only multi-LLM panel pressure exposes, and capability-gap patterns visible in the 52 model failures. We catalogue each in turn.

\subsection{Server-design lessons surfaced by panel pressure}
\label{sec:findings-server}

Running seven independently trained LLMs over an iterative sequence of MCP-server design choices revealed thirteen issues that 158 unit tests and a read-only smoke check had not exposed --- because none of those tests exercised the cross-tool reasoning an LLM agent performs against live federation state. The thirteen issues cluster into seven design themes; we describe each by example.

\paragraph{Path normalisation (M-1, M-6, M-12).} Onedata's REST API mixes absolute (\texttt{/<space>/<path>}) and relative (\texttt{<path-under-listed-root>}) path forms across endpoints, and an LLM agent that reads a relative path from one tool's response and feeds it back into another tool's input falls into a quiet failure mode --- the second call resolves against the wrong root and silently returns zero matches or an HTTP~404. The wrapper now normalises every tool's path output to the absolute form (M-1 in \texttt{query\_by\_metadata}; M-6 in \texttt{list\_files\_recursively}) and disambiguates the \texttt{prefix} parameter as relative-only with a docstring tightening (M-12). Three of the panel's weaker legs hit M-12 in the absolute-prefix form on D2 (silent zero-match) before the docstring fix; none hit it after.

\paragraph{Response-shape design for smaller-model accommodation (M-5, M-7).} Onedata's \texttt{getFileDistribution} returns a \texttt{distributionPerProvider} dictionary keyed by hex provider IDs (M-5); its \texttt{getFileQosSummary} returns \texttt{\{qosId: status\}} flat with no per-rule details (M-7). On both, smaller-capacity models in our panel skipped the chained name-resolution or rule-detail call and answered with the wrong shape. The wrapper now joins provider name in-line on the distribution response and inlines per-rule \texttt{expression} and \texttt{replicas\_num} on the QoS summary; both fixes preserve the original flat-shape fields under \texttt{requirements\_flat} for backwards compatibility. Larger panel legs (Sonnet, V4-Pro) reliably executed the chained calls and so are unaffected by the response-shape change; the smaller legs (Granite, GLM) flip from FAIL to PASS on P1 and P6 after the join.

\paragraph{Parameter-name fidelity (M-3, M-4).} Two parameters that ought to be polymorphic instead reject the natural spelling. \texttt{space\_id} on \texttt{list\_space\_providers} and \texttt{list\_space\_transfers} accepts only the hex space-id (M-3 --- four of five panel models passed the human-readable name from the brief and got HTTP~403); \texttt{metadata\_type} on \texttt{set\_file\_metadata} accepts \texttt{\{json, rdf, xattrs\}} only (M-4 --- multiple models read ``custom JSON metadata'' in the brief and faithfully passed \texttt{metadata\_type="custom"}, which yields HTTP~404 from a path-construction failure). Both were fixed by polymorphism: \texttt{space\_id} now accepts name or hex; \texttt{metadata\_type="custom"} aliases to \texttt{json}.

\paragraph{Directory primitive (M-11).} Without a dedicated \texttt{create\_directory} tool, agents asked to set up a target directory before placing a file in it fell into a structural trap: \texttt{create\_file(path="archive", content="", create\_parents=True)} silently creates a regular file at \texttt{archive}, after which every \texttt{archive/<x>} subsequent operation fails with \texttt{ENOTDIR}. Two of the panel's weaker legs hit this on A4. The fix is three coordinated changes: a new \texttt{create\_directory} tool exposed in the ablation surface, a default flip of \texttt{create\_parents} to \texttt{True}, and a defensive error in \texttt{create\_file} that triggers when content is empty and the basename has no recognisable extension.

\paragraph{Byte-count out-of-band (M-10).} \texttt{download\_file} originally returned the raw content; agents asked for ``byte count of file X'' computed \texttt{len(content)}, which counts characters after UTF-8 decode rather than bytes. Three of four panel LLMs gave wrong byte counts on D3 despite reading the right file. The fix returns a structured envelope --- \texttt{\{content, size\_bytes, content\_type\}} --- with \texttt{size\_bytes} computed server-side. Backwards-compatible: agents that destructure \texttt{.content} keep working.

\paragraph{Docstring tightening for federation-specific operands (M-8).} Onedata accepts arbitrary \texttt{key=value} operands in QoS expressions but the working set is per-federation: SPICE has no admin-set storage attributes, so expressions like \texttt{country=PL}, \texttt{type=ssd}, \texttt{geo=EU} that LLMs trained on generic Onedata documentation confidently emit will create rules in permanent \texttt{impossible} status. The fix is a docstring tightening that names the always-safe operands (\texttt{providerId}, \texttt{storageId}, \texttt{anyStorage}), flags the admin-attributed family as federation-specific, and explicitly calls out common fictional operand names to avoid. The K=8 sweep still showed several A5 trials emitting \texttt{cloud=EU} (capability-gap C-2 below), so the docstring tightening is necessary but not sufficient.

\paragraph{Transport hardening (M-13, RESOLVED).} The HTTP transport mode of the MCP server originally accepted requests with arbitrary \texttt{Host} and \texttt{Origin} headers --- a DNS-rebinding vulnerability that the modelcontextprotocol/conformance suite~\cite{mcpconformance2026} \texttt{dns-rebinding-protection} scenario detects. We added FastMCP~\cite{fastmcp2025} ASGI middleware that enforces Host/Origin allow-listing, and the conformance suite now reports 2/2 PASS on the rebinding scenario. The stdio-transport mode used by the K=8 sweep through the \texttt{claude-agent-sdk} subprocess was unaffected (HTTP headers do not apply to the stdio path), but operators running the server via HTTP for Inspector tooling must now apply the middleware fix or remain on the stdio path.

```
