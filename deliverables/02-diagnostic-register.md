# Diagnostic Register

> **Exercise case: synthetic.** All incident facts and example values are invented. No production logs, traces, index records, user reports, or system-owner confirmation were provided. No root cause is verified.

## Incident Frame

- **Observed symptom:** In the synthetic scenario, an employee asks what reimbursement cap applies. The assistant answers "$75" and cites a policy marked archived, while the scenario's current policy says "$100" for the same fictional region and effective date.
- **Reproducible example:** Synthetic prompt: "What is the meal reimbursement cap for North region on September 15, 2026?" Synthetic response: "$75 per meal," citing "Travel Policy v2, archived May 1, 2026." Synthetic expected source: "Travel Policy v3, effective September 1, 2026," with a $100 cap.
- **Affected users or workflow:** Employees seeking reimbursement guidance; actual reach is unknown.
- **Impact and severity:** Could cause employees to underspend, file incorrect claims, or lose confidence. No financial or user impact has been measured; severity is unassessed.
- **First observed:** Synthetic exercise date: September 15, 2026. Not a real incident timestamp.
- **Recent relevant changes:** Unknown; do not infer a release caused the case.
- **Immediate containment:** Temporarily suppress numeric cap answers for the affected policy/region unless the assistant can validate a current effective source; route users to HR's authoritative policy channel.
- **Facts frozen for this case:** The query, response, archived citation, expected current policy and values above are synthetic premises only. Treat architecture, logs, retrieval behavior, user impact, and recent changes as unknown until evidence is supplied.

## Evidence Available

| Evidence | Source | What it shows | Limitation | Observed, inferred, or verified? |
|---|---|---|---|---|
| One synthetic failing query and answer | Exercise premise | Defines the user-visible mismatch and archived citation | Not a real captured request or response | Synthetic, not production-observed |
| Synthetic current-policy statement | Exercise premise | Defines expected answer for this case | Not checked against a real HR source | Synthetic, not verified |
| Production request trace, prompt, model/config versions | Not provided | Could show exact serving path and versions | Unavailable | Unknown |
| Retrieved passage IDs, ranks, metadata, and permission filters | Not provided | Could show what context reached the model | Unavailable | Unknown |
| Index manifest and ingestion/retirement records | Not provided | Could show whether old and current versions were indexed correctly | Unavailable | Unknown |
| Cache hit/key/TTL information | Not provided | Could establish whether retrieval was bypassed by a cached answer | Unavailable | Unknown |

## Competing Hypotheses

| Rank | Hypothesis and component | Why it fits | Evidence against it | Next discriminating test | Confidence |
|---|---|---|---|---|---|
| 1 | **Expired policy remains active in the index** (ingestion/version retirement) | The synthetic answer cites an archived version; an old indexed document could be retrieved if retirement or effective-date metadata failed | No real index record or ingestion evidence; the citation itself is only a synthetic case fact | Compare authoritative source versions with index records and ingestion/retirement events for v2 and v3 | Low; plausible, unverified |
| 2 | **Serving retrieval selects the wrong version** (query filter/ranking) | Both policy versions may exist, but date/region filtering or ranking could select v2 | No ranked result list or retrieval configuration is available | Replay the exact query with cache bypass; inspect ranked passages and effective-date/region filters | Low; plausible, unverified |
| 3 | **A stale response cache returns a prior answer** (application/cache) | A cached answer could retain an old value and citation even after source/index updates | No evidence that response caching exists or that this request was a cache hit | Inspect trace/cache status; compare same request with a cache-bypassed retrieval path | Low; possible, unverified |
| 4 | **Current evidence reaches the model, but generation or citation validation fails** (model/post-processing) | The model could prefer or misstate an older passage, or validation may accept an expired citation | The synthetic answer's archived citation makes this less likely than upstream selection, but no actual model context is available | Compare final model context with output; test whether validator rejects expired or mismatched citations | Low; possible, unverified |

## Next-Best Evidence

- **The next observation or test we would request:** One redacted end-to-end trace for the failing request, including request/config versions, cache hit status, identity scope, retrieved document IDs with effective dates and ranks, permission filters, final model context, citation validation, and response.
- **Why it best separates the leading hypotheses:** A cache hit points toward stale response reuse; a cache miss with v2 first in retrieval points toward indexing/filter/ranking; current v3 present in final context but v2 cited points toward generation or validation. The trace should be paired with the authoritative HR source and index manifest before calling any cause verified.
- **Who can provide or run it:** Platform/observability owner, with HR Policy confirming the authoritative source and Privacy/Security approving the minimum necessary trace fields.
- **Decision that depends on it:** Whether to repair ingestion/version retirement, retrieval filters/ranking, cache invalidation, or model/output validation; keep numeric answers contained until current-source validation is demonstrated.

## AI Challenge Note

| LLM suggestion | How it was checked | Accepted, edited, or rejected | Reason |
|---|---|---|---|
| Do not assume that the archived citation proves an ingestion defect; test cache bypass and inspect final context so upstream selection can be separated from generation/validation. | Compared against the four distinct hypotheses above; no production artifacts are available to execute the tests. | Accepted as an investigation step, not as a cause finding. | It identifies discriminating evidence without inventing system behavior. Requires Platform and HR verification. |

## Current Diagnosis

- **Verified cause, if any:** None.
- **Best-supported hypothesis if not verified:** An expired policy was selected or reused somewhere before the answer was returned. The synthetic archived citation supports investigating this path first but does not distinguish stale indexing, retrieval selection, or caching.
- **Important uncertainty:** Whether the synthetic failure corresponds to a real reproducible production request, what source version is authoritative, which passages reached the model, and whether caching exists.
- **Evidence that would change the diagnosis:** The trace plus source/index version records, cache status, and a cache-bypassed replay of the frozen query.
