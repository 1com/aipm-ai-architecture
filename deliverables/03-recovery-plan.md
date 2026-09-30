# Prioritized Recovery Plan

> **Exercise case: synthetic.** Actions and thresholds below are proposed workshop recommendations, not approved production policy or measured baselines. Owners are role assumptions to confirm.

## Decision

- **Recommendation:** Contain, then investigate before making a permanent architecture change.
- **Decision owner:** Assumed: Employee Experience product owner, with HR Policy and Platform owners; actual authority must be confirmed.
- **Reason in one sentence:** The synthetic answer cites an archived policy, but available information cannot distinguish stale indexing, retrieval selection, caching, or generation/validation.
- **Evidence that would change the recommendation:** A trace and source/index records proving the current policy was selected, permission-checked, and used correctly would permit lifting containment after regression testing; evidence of broader or cross-user leakage would require pausing the affected feature and escalating severity.

## Actions

| Priority | Horizon | Action | Linked cause or unknown | Impact | Effort | Risk reduction | Confidence | Reversibility | Owner |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Contain | For the affected policy/region, suppress numeric answers unless a current effective source can be validated; show the authoritative HR link and escalation route. | Unknown source currency and actual user reach | High | Low | High | High that it reduces exposure; root cause confidence remains low | High | Product + HR Operations |
| 2 | Investigate | Capture a privacy-reviewed trace for the frozen failing query; compare the authoritative source, index manifest, retrieval ranking/filtering, cache status, final model context, and citation validator. | Distinguishes ingestion, retrieval, cache, and generation hypotheses | High | Medium | High | High that it is discriminating; cause confidence pending | High | Platform + HR Policy + Privacy |
| 3 | Correct | Apply only the correction supported by the trace: retire expired records or repair effective-date/region filters; invalidate affected cache entries if present; correct citation/abstention validation if current evidence was ignored. | H1-H4 in the diagnostic register | High | Medium | High | Low until evidence identifies the cause | Medium to high, with versioned rollback | Platform owner for the verified component |
| 4 | Prevent | Add frozen regression cases for current, expired, conflicting, wrong-region, and missing-source policies; alert on expired citations and monitor escalation/answer rates after changes. | Recurrence and change-detection gaps | High | Medium | High | Medium; directly tests the target behavior | High | Product Quality + Platform + HR Policy |

## Options Considered

| Option | Why it could help | Why not first? | Reversible? |
|---|---|---|---|
| Workflow or non-ML change: suppress unsupported numeric answers, display source date, route to HR | Immediately reduces risk without needing a confirmed model or retrieval defect | Adds friction and does not identify the underlying component | Yes; feature flag or scoped policy rule |
| Small architecture correction: version-aware retrieval/retirement or cache invalidation | Addresses stale evidence if the trace confirms the relevant path | Choosing one before inspecting the trace risks fixing the wrong layer | Usually, if configuration/index versions are retained |
| Larger model, data, or platform change: replace/fine-tune model or rebuild the whole platform | Could alter answer behavior or simplify operations in some cases | No evidence that model capability is the cause; larger changes add cost and make causal learning harder | Not reliably; defer unless evidence warrants |

## Proof of Recovery

> Proposed starting guardrails; owners must approve thresholds against policy risk and establish baselines before release.

| Level | Measure or test | Baseline | Pass or guardrail threshold | Owner |
|---|---|---|---|---|
| User or workflow | Frozen failing query and representative policy questions by region/date | No production baseline supplied | 100% of release-gate cases either answer from the correct effective policy or safely abstain/escalate; zero archived-only numeric answers | Product Quality + HR Policy |
| Retrieval or context | Correct effective document in top-k and correct permission/date filtering | Not measured | 100% of curated release-gate cases retrieve only permitted applicable policy evidence; expired-only retrieval must trigger abstention | Platform |
| Generation or action | Citation matches supplied policy and numeric claim; no autonomous claim approval | Not measured | 100% citation/value match on release-gate set; no answer when applicable source is absent, expired, or conflicting | AI Platform + HR Policy |
| Operations | Trace completeness, latency, cache invalidation behavior, and fallback availability | Not measured | Required version/cache/retrieval fields present for 100% of sampled policy requests; fallback available during dependency failure | Platform Operations |
| Risk | Expired-citation rate, policy escalation rate, access violations, employee reports | Not measured | Zero known answers based only on expired policy and zero unauthorized disclosures; any occurrence triggers incident review and rollback assessment | Security + HR Policy |

## Release and Rollback

- **Release scope:** First validate in offline evaluation; then limited, monitored rollout for the affected policy/region. Do not expand until the decision owner approves results.
- **Monitoring required:** Effective-date citation checks, retrieval version/rank, cache status where applicable, abstention/escalation rate, user reports, and permission-denial events, using minimised and access-controlled telemetry.
- **Rollback or shutdown trigger:** Any confirmed answer based only on an expired source, wrong-region policy, or unauthorized document; inability to prove source currency; or a material increase in policy-answer complaints beyond an owner-approved threshold.
- **Fallback experience:** Show the authoritative HR policy page and escalation channel; do not guess a limit.
- **Remaining risk and authorized risk owner:** The real architecture, incident scope, and user impact are unknown. The designated product risk owner and HR Policy owner must explicitly accept any residual risk before rollout; names and authority remain to be confirmed.
