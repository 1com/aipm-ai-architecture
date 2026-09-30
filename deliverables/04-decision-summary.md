# Three-Slide Decision Summary

> **Exercise case: synthetic.** These slide-ready notes describe an invented incident. They are not evidence of a real production failure or a verified root cause.

## Slide 1: What Is Broken

**Decision context:** An internal reimbursement-policy assistant gives an employee a numeric cap that conflicts with the current policy in the synthetic scenario.

- **Synthetic observed example:** Asked for the North region meal cap on September 15, 2026, the assistant says "$75" and cites an archived May policy. The exercise premise says the current policy, effective September 1, sets the cap at "$100."
- **Expected behavior:** Answer from the currently effective policy with a matching citation, or abstain/escalate if applicability cannot be confirmed.
- **Relevant flow:** Employee/SSO -> application -> authorized, date-aware policy retrieval -> model -> citation/effective-date validation -> answer or HR escalation. HR source -> versioned ingestion/index. Actual architecture is unconfirmed.
- **Impact:** Could mislead employees about eligible expenses; actual reach, financial impact, and severity are unknown.
- **Immediate containment:** Suppress numeric answers for the affected policy/region unless source currency is validated; link to the authoritative HR channel.
- **Evidence labels:** The example and expected answer are synthetic exercise facts. Architecture, actual logs, scope, and cause are unknown.

## Slide 2: Why We Think It Is Broken

**No root cause is verified.** The archived citation makes stale evidence a sensible first investigation, not a conclusion.

| Rank | Hypothesis | Evidence needed to distinguish it |
|---|---|---|
| 1 | Expired policy remains active in the index | Compare authoritative source versions with index records and ingestion/retirement history |
| 2 | Retrieval date/region filtering or ranking selects the old version | Inspect ranked passages and filters for the frozen query; replay with cache bypass |
| 3 | A stale response cache returns an earlier answer | Inspect cache hit/key/TTL and compare with a cache-bypassed request |
| 4 | Current evidence reaches the model but generation or citation validation fails | Compare final model context, generated claim, and validator decision |

**Next best test:** Obtain one privacy-reviewed end-to-end trace for the frozen query, then compare it with the authoritative HR source and index manifest. This can separate cache, retrieval/index, and generation/validation paths. Do not change the model or declare a cause from the synthetic citation alone.

## Slide 3: What We Recommend

**Recommendation:** Contain first; investigate the request path; make only the correction supported by the evidence.

1. Temporarily suppress affected numeric answers unless an effective source is validated; route employees to HR.
2. Inspect request/config versions, cache status, identity scope, retrieved IDs/ranks/effective dates, final model context, and citation validation.
3. Correct the verified layer only: version retirement, retrieval filters/ranking, cache invalidation, or citation/abstention validation.
4. Add regression cases for current, expired, conflicting, wrong-region, and missing policies; monitor citations, escalation, and access violations.

**Proof before rollout:** Every release-gate case must either cite the correct current policy or safely abstain/escalate; zero archived-only numeric answers and zero unauthorized disclosures. Begin with a limited rollout and retain rollback capability.

**Owner and guardrails:** Product decision owner, HR Policy, Platform, and Privacy/Security roles must be named before release. Roll back or pause on any confirmed expired-only answer, wrong-region policy, or unauthorized disclosure. The current cause, scope, metric baselines, and owner names remain unknown.
