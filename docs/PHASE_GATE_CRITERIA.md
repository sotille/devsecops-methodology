# Phase-Gate Exit Criteria

This document defines the concrete exit criteria for each of the four phases in the DevSecOps Methodology. These are the conditions that MUST be satisfied before advancing to the next phase.

## Phase 1 — Assess (Days 1–15)

**Goal:** Create a defensible baseline.

**Exit criteria:**
- [ ] Current maturity snapshot complete across 8 domains (Strategy & Governance, Threat Modeling, Secure SDLC, Software Supply Chain, CI/CD Security, Cloud & Container Security, Compliance Automation, Operations & Incident Response)
- [ ] 5–8 prioritized gaps identified and documented
- [ ] 90-day target state defined (scored per domain)
- [ ] Executive sponsor sign-off on baseline + gap list
- [ ] Stakeholder map complete (engineering, security, compliance, ops)

**Do not proceed to Phase 2 without:** executive sponsor sign-off.

## Phase 2 — Design (Days 16–45)

**Goal:** Build the system, not buy tools.

**Exit criteria:**
- [ ] Pipeline reference architecture documented (canonical flow: Code → Build → SAST → SCA → Secrets → Container Scan → SBOM → Signing → Deploy → Runtime)
- [ ] RACI matrix complete at the activity level (not just "security owns security")
- [ ] Tool decisions documented INCLUDING tools rejected and why
- [ ] Policy enforcement model defined (which gates block vs warn, by environment)
- [ ] Architecture review with security + engineering leadership complete

**Do not proceed to Phase 3 without:** signed-off RACI + tool decisions.

## Phase 3 — Implement (Days 46–75)

**Goal:** Prove the model with ONE service deeply, not many services shallowly.

**Exit criteria:**
- [ ] One production service fully running the secured pipeline (SAST/SCA/Secrets/Container/SBOM/Signing/Deploy/Verify)
- [ ] Signed artifacts in production with verified signatures at deploy
- [ ] SBOM generated, queryable, and indexed
- [ ] Gate failure rate < 10% (false positives managed)
- [ ] Time to first secure deployment measured and documented
- [ ] Developer experience survey complete; feedback addressed

**Do not proceed to Phase 4 without:** one production service running cleanly + DevEx feedback addressed.

## Phase 4 — Optimize (Days 76–90)

**Goal:** Turn a project into a system.

**Exit criteria:**
- [ ] DORA metrics baseline established (deployment frequency, lead time, change failure rate, MTTR)
- [ ] Security metrics dashboard live (time to remediate critical, SBOM coverage, signed artifact %)
- [ ] Weekly triage cadence operating
- [ ] Monthly leadership review cadence operating
- [ ] Quarterly reassessment process documented
- [ ] Service-2 onboarding plan ready (proof the model scales)

**Phase 4 is not "complete" — it becomes ongoing operations.**

## Common phase-gate failures (and how to avoid)

| Symptom | Likely root cause | Mitigation |
|---|---|---|
| Phase 1 → Phase 2 without sponsor buy-in | Executive sees this as "security project" not transformation | Use Phase 1 output to quantify business risk |
| Phase 2 stuck in tool-evaluation paralysis | No constraints on decision timeline | Set hard deadline; commit to revisit annually |
| Phase 3 attempted on too many services | "We need to roll out everywhere" pressure | Stay disciplined — depth before breadth |
| Phase 4 metrics tracked but not acted on | No accountability for metric movement | Tie metrics to leadership review cadence with action items |
