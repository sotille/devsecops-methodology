# Implementation Playbook

## Overview

This playbook provides week-by-week guidance for the first 90 days of a DevSecOps transformation implementation — the most critical period that establishes whether the program will succeed or stall. It covers team structure, toolchain deployment sequence, pilot program design, scaling strategy, managing resistance, executive stakeholder management, and resource planning.

---

## 90-Day Implementation Playbook

### Pre-Launch (Weeks -2 to 0)

**Week -2: Mobilize**

The pre-launch period is used to establish infrastructure for the program before any development teams are affected.

- [ ] Form the transformation team (Transformation Lead, Security Architect, Platform Engineer, Change Management Lead)
- [ ] Confirm executive sponsor and schedule inaugural sponsor meeting
- [ ] Finalize Phase 2 design documents and obtain sign-off
- [ ] Identify and confirm the pilot team (team lead agreement in writing)
- [ ] Provision CI/CD pipeline infrastructure for pilot team testing
- [ ] Create the program Slack/Teams channel and invite initial stakeholders
- [ ] Set up the security metrics dashboard (even if it shows no data yet)
- [ ] Procure tool licenses for any commercial tools in the approved toolchain

**Week -1: Pilot Team Preparation**

- [ ] Brief the pilot team lead in detail: what will change, when, what to expect
- [ ] Meet with the pilot team: introduce the program, invite questions, set expectations
- [ ] Identify the pilot team's security champion candidate (may not be officially selected yet)
- [ ] Run a read-only scan against the pilot team's main repository (no pipeline changes yet)
- [ ] Review findings from the read-only scan privately before sharing with the team
- [ ] If CRITICAL secrets are found, immediately engage team lead privately (do not broadcast)
- [ ] Prepare the initial findings report with clear triage guidance
- [ ] Set up the vulnerability management tracking integration (Jira/GitHub Issues)

---

### Weeks 1–2: Observe Mode (Pilot Team)

**Goal:** All security stages running in warn-only mode. No build failures. Baseline data collected.

**Week 1:**

- [ ] Deploy pipeline security template to pilot team repository (all stages, warn-only mode)
- [ ] Confirm all stages run successfully without errors on a test commit
- [ ] Run first full pipeline scan and review results with Security Architect
- [ ] Share initial finding report with pilot team lead and champion candidate
- [ ] Hold Day 3 check-in with pilot team: "Here are what the tools found — these are not blockers yet, but here's what they mean"
- [ ] Begin daily stand-up inclusion for the transformation team

**Week 2:**

- [ ] Categorize all findings: true positive exploitable, true positive non-exploitable, false positive
- [ ] Configure initial suppressions (.trivyignore, .gitleaks.toml allowlists, Semgrep tuning)
- [ ] Formally identify and onboard the pilot team's security champion
- [ ] Begin champion Foundations training module
- [ ] Deliver developer Security Foundations training to pilot team (2-hour session)
- [ ] Document all false positives with justification for future audit reference

**Week 1–2 Metrics to Track:**
- Total findings by severity and tool
- False positive rate estimate (before tuning)
- Pipeline duration impact (how much time are security stages adding?)
- Developer questions received and answered (measure engagement)

---

### Weeks 3–4: First Blocking Gates (Pilot Team)

**Goal:** Secrets scanning and CRITICAL findings blocking in pilot team. First remediation sprint underway.

**Week 3:**

- [ ] Enable Gitleaks as a blocking gate
- [ ] Ensure all previously detected secrets are rotated (verify with team lead)
- [ ] Enable CRITICAL blocking for Semgrep SAST
- [ ] Enable CRITICAL blocking for Trivy SCA
- [ ] Enable CRITICAL blocking for Trivy container scan
- [ ] Prioritize and assign all CRITICAL findings in the team backlog
- [ ] Establish vulnerability remediation SLA: CRITICAL = 5 days
- [ ] Hold the first security champion Community of Practice session (even if it is just 2–3 people for now)

**Week 4:**

- [ ] Review pilot team CRITICAL finding remediation progress
- [ ] Confirm Gitleaks false positive rate is acceptable (target: < 10%)
- [ ] Deliver Tool Practicum training to champion
- [ ] First weekly security metrics report to engineering leadership
- [ ] Conduct pilot team retrospective: what is working, what is frustrating, what needs to change?
- [ ] Address top 3 friction points identified in retrospective before proceeding

**Week 3–4 Metrics:**
- Open CRITICAL findings: count and age
- CRITICAL findings remediated this week
- Gitleaks false positive rate
- Pipeline failure rate (are CRITICAL gates blocking frequently, suggesting real issues or FP noise?)

---

### Weeks 5–8: HIGH Blocking and Pilot Expansion

**Goal:** HIGH blocking enabled on pilot team. First wave teams onboarded to warn-only mode.

**Week 5:**

- [ ] Enable HIGH blocking for all scan stages on pilot team
- [ ] Assign all HIGH findings to backlog with 14-day SLA
- [ ] Identify Wave 1 teams (2–3 teams beyond pilot)
- [ ] Brief Wave 1 team leads (same briefing the pilot team lead received)
- [ ] Begin recruiting Wave 1 champions

**Week 6:**

- [ ] Deploy pipeline template to Wave 1 teams in warn-only mode
- [ ] Run baseline scans on Wave 1 repositories, triage findings
- [ ] Hold tool demonstration session with Wave 1 teams
- [ ] Add DAST (OWASP ZAP) to pilot team pipeline in warn-only mode
- [ ] Enable Cosign artifact signing for pilot team

**Week 7:**

- [ ] Review pilot team HIGH finding remediation progress
- [ ] First DAST scan results review with pilot team
- [ ] Onboard Wave 1 champions to Foundations training
- [ ] Add Wave 1 champions to Community of Practice
- [ ] First full monthly security metrics report to CISO

**Week 8:**

- [ ] Wave 1 teams: enable Gitleaks and CRITICAL blocking
- [ ] Pilot team: enable DAST HIGH blocking
- [ ] Pilot team: activate manual approval gate for production deployments
- [ ] Pilot team retrospective: 8-week assessment, what has changed?
- [ ] Document lessons learned for Wave 1 and Wave 2 rollout refinement

---

### Weeks 9–12: Scaling and Full Coverage

**Goal:** Wave 1 at full enforcement. Wave 2 onboarded. Program self-sustaining on pilot and Wave 1.

**Week 9:**

- [ ] Wave 1 teams: enable HIGH blocking (after false positive tuning)
- [ ] Identify Wave 2 teams and begin briefing
- [ ] Deliver Security Code Review training to all champions

**Week 10:**

- [ ] Deploy pipeline template to Wave 2 teams in warn-only mode
- [ ] Wave 1 teams: enable DAST and signing
- [ ] First quarterly governance review (CISO, CTO, transformation leads)

**Week 11:**

- [ ] Wave 2 baseline scan and triage
- [ ] Threat Modeling training for pilot team champions and interested developers
- [ ] Review all suppressions added to date — are any unjustified?

**Week 12:**

- [ ] Wave 2: enable Gitleaks and CRITICAL blocking
- [ ] 90-Day program review: present full metrics, lessons learned, Wave 3+ plan
- [ ] Transition plan for remaining teams beyond the initial 90 days
- [ ] Update and finalize the 18-month program roadmap based on actuals

---

## Team Structure and RACI

### Transformation Team (Months 1–6)

| Role | Responsibilities | Time Allocation |
|---|---|---|
| Transformation Lead | Overall program management, stakeholder communications, executive reporting, blocker resolution | 100% |
| Security Architect | Pipeline design, tool configuration, technical advisory to pilot teams, toolchain evolution | 100% |
| Platform Engineer | Pipeline template deployment, CI/CD integration, automation scripting, toolchain operations | 100% |
| Change Management Lead | Developer communications, training delivery coordination, champions program management | 50–100% |
| Embedded Security Engineer | Hands-on support for pilot and Wave 1 teams, finding triage, training facilitation | 100% |

### Steady-State Security Team (Months 9+)

Once the transformation is underway and the model is proving out, the team transitions to a Platform Security operating model:

| Role | Responsibilities | Count |
|---|---|---|
| Platform Security Lead | Team leadership, strategic planning, executive reporting | 1 |
| Security Platform Engineer | Toolchain maintenance, pipeline evolution, automation | 1–2 |
| Security Enablement Lead | Champions program, training, documentation | 1 |
| Application Security Engineer | Advisory services, threat modeling, code review, incident support | 1–2 |

This team typically supports 30–60 developers. Scale proportionally.

---

## Toolchain Deployment Sequence

Deploy tools in this order to establish quick wins and minimize initial disruption:

### Phase A: Low-Friction, High-Value (Weeks 1–2)

Tools that are easy to deploy, low false positive rate, and immediately valuable:

1. **Gitleaks** — Fast to configure, low FP, immediately actionable
2. **Trivy SCA** — Broad dependency scanning, good default configuration
3. **Trivy Container** — Near-zero configuration, high signal

### Phase B: Medium Configuration (Weeks 3–6)

Tools that require customization for the organization's tech stack:

4. **Semgrep** — Ruleset selection and tuning for language/framework
5. **OWASP ZAP** — Context file configuration, staging environment access

### Phase C: Infrastructure (Weeks 4–8)

Tools requiring supporting infrastructure:

6. **Cosign** — Key generation, registry configuration, verification policy
7. **SBOM generation** — Part of Trivy, requires storage destination

### Phase D: Advanced (Months 3+)

Tools requiring organizational maturity to use effectively:

8. **Vault or cloud-native secrets** — Requires secrets management program maturity
9. **OPA Gatekeeper / Kyverno** — Requires Kubernetes admission webhook configuration
10. **SLSA provenance generator** — Requires GitHub Actions OIDC configuration

---

## Pilot Program Design

### Pilot Duration

The pilot should run for a minimum of 8 weeks before evaluating results and beginning Wave 1 expansion. Shorter pilots do not produce representative data — false positive rates need at least 2–4 weeks of real usage to stabilize.

### Pilot Success Criteria

Define success criteria before the pilot begins. Recommended criteria:

| Criterion | Target | How Measured |
|---|---|---|
| Pipeline runs successfully on every commit | 100% | Pipeline execution logs |
| CRITICAL findings remediated within SLA | 100% | Vulnerability management tool |
| False positive rate acceptable | < 20% for each tool | Manual FP classification |
| Developer satisfaction neutral or positive | ≥ 3.0/5.0 | Survey at week 8 |
| No emergency exceptions due to FP blocking | 0 | Exception log |
| Champion active and engaged | Active in CoP, handling triage | Champion activity log |

### Pilot Go/No-Go Decision

At the end of the pilot period, formally assess whether the program is ready to expand:

**Go:** All 6 pilot success criteria met, pilot team lead recommends expansion.

**Conditional Go:** 4–5 criteria met, but specific issues are identified and a remediation plan exists to address them within 2 weeks.

**No-Go:** Fewer than 4 criteria met, or pilot team lead has reservations. Investigate root causes before expanding. Do not expand a broken model to more teams.

---

## Scaling from Pilot to Enterprise

### Wave Structure

Organize post-pilot expansion into waves of 2–4 teams per wave:

| Wave | Teams | Duration | Key Milestone |
|---|---|---|---|
| Pilot | 1 team | Weeks 1–8 | First team at full enforcement |
| Wave 1 | 2–3 teams | Weeks 5–16 | All Wave 1 at CRITICAL blocking |
| Wave 2 | 3–5 teams | Weeks 10–22 | All Wave 2 at CRITICAL blocking |
| Wave 3 | Remaining teams | Months 6–12 | All teams at full enforcement |

Waves overlap — Wave 1 is at CRITICAL blocking while Wave 2 is beginning onboarding.

### Scaling the Champions Program

The champions program must scale ahead of the team onboarding wave. Recruit and begin training champions for Wave N+1 while Wave N is still in onboarding:

```
Timeline:
Month 1: Pilot champion recruited and trained
Month 2: Wave 1 champions recruited
Month 3: Wave 1 champions in training, Wave 2 champions recruited
Month 4: Wave 2 champions in training, Wave 3 champions recruited
...
```

### Scaling the Platform

As more teams onboard, the pipeline platform must scale:
- Use central template repositories (GitLab `include`, GitHub reusable workflows, Jenkins shared libraries) so that pipeline improvements are applied to all teams simultaneously, not one-by-one
- Automate onboarding: new repositories should automatically inherit the security pipeline template
- Vulnerability database caching at the infrastructure level (Trivy DB mirror, Semgrep rule cache)

---

## Managing Resistance to Change

### Common Resistance Forms

**"False positives are wasting my time"**
The most common objection, often valid. Response: Acknowledge it, measure the FP rate, commit to a target, and deliver. If a tool is generating > 20% FP on real code, tune it or replace it. Do not dismiss this complaint.

**"Security is slowing down our releases"**
Investigate whether this is about FP rate (tool problem), pipeline duration (performance problem), or remediation debt (backlog problem). Each has a different solution. Rarely is the correct response to remove a security gate.

**"We'll deal with security when we have more time"**
Security debt compounds. Every week of deferred remediation adds more findings. Use the risk register to make the cost of deferral concrete: "At current rates, we will have X CRITICAL findings in Y weeks, each requiring Z days to remediate, totaling W engineering weeks."

**"Our code is fine — we've never had a security incident"**
This is an absence of evidence, not evidence of absence. Most organizations do not discover security incidents until they are publicly disclosed or customer-reported. Use industry statistics and relevant CVE examples from similar technology stacks to contextualize the risk.

### Escalation Protocol for Resistance

When a team lead is resistant to adoption:
1. First attempt: Security Architect meets with team lead 1:1 to understand specific concerns
2. If unresolved: Transformation Lead joins next meeting, focuses on understanding vs. resolving
3. If still unresolved: Executive Sponsor meets with engineering VP to align on program expectations
4. If persistent: Document the resistance and its rationale; escalate to governance level as a program risk

Most resistance resolves at step 1 or 2 when the security team demonstrates flexibility (adjusting FP thresholds, fixing friction points) rather than rigidity.

---

## Executive Stakeholder Management

### Executive Sponsor Management

The executive sponsor requires monthly updates at minimum. Content:

**Monthly sponsor update (15-minute meeting or written brief):**
- Traffic light status for each wave (green/yellow/red)
- Top 3 wins since last update
- Top 3 blockers requiring sponsor attention
- Upcoming decision points requiring sponsor involvement
- Ask: specific action needed from sponsor (name it explicitly)

**Never surprise the sponsor.** If a significant blocker emerges between monthly updates, send a brief note within 24 hours.

### CISO Reporting

The CISO receives the monthly operational governance report. Key content:
- Transformation progress vs. milestones
- Security metrics trends (are they improving?)
- Open CRITICAL findings by team, with age
- Security incidents in the period (if any) with attribution to DevSecOps coverage gaps
- Compliance evidence status

### CTO/Engineering VP Reporting

Engineering leadership cares primarily about:
- Delivery impact: Are security gates increasing release cycle time?
- Developer experience: Are developers more or less frustrated with security than 3 months ago?
- Vulnerability debt: How large is the existing backlog, and is it growing or shrinking?

Frame the program in these terms, not in security jargon.

---

## Budget and Resource Planning

### Toolchain Budget

Typical toolchain cost for a 50-developer organization:

| Tool | Model | Annual Cost (estimated) |
|---|---|---|
| Semgrep (Open Source) | Free | $0 |
| Semgrep Pro | SaaS | $15,000–$25,000 |
| Trivy | Open source | $0 |
| Gitleaks | Open source | $0 |
| OWASP ZAP | Open source | $0 |
| Cosign / Sigstore | Open source | $0 |
| Vault (HashiCorp) | Open source / Enterprise | $0–$20,000 |
| Vulnerability management tool (DefectDojo) | Open source | $0 |
| **Total (OSS-first)** | | **$0–$5,000** |
| **Total (commercial tools)** | | **$30,000–$75,000** |

### People Budget

| Role | Duration | Cost Driver |
|---|---|---|
| Transformation Lead | 18 months FTE | Existing headcount or contractor |
| Security Architect | 18 months FTE | Usually existing security team |
| Platform Engineer | 12 months FTE | May require new hire or contractor |
| Change Management Lead | 9 months 50% FTE | May be combined with security enablement role |
| Embedded Security Engineers | 6 months FTE per pilot/wave team | Usually rotated from security team |

### Training Budget

| Item | Unit Cost | Scale |
|---|---|---|
| Security champions training (external) | $2,000–$5,000/champion | First cohort of 5–10 |
| Developer security awareness training (platform) | $50–$150/developer/year | All developers |
| Security conference attendance for champions | $3,000–$5,000/attendee | 1–2 champions annually |
| Internal training development | 40–80 hours security team time | One-time |

### ROI Planning

The ROI case for DevSecOps is straightforward but requires realistic numbers:

**Cost avoidance per vulnerability prevented in production:**
- Average industry estimate: $150,000 per security incident (IBM Cost of a Data Breach 2024)
- Conservative estimate for a non-breach vulnerability remediated in production: $15,000–$50,000 (incident response, emergency patch, communication, productivity loss)
- Cost of same vulnerability remediated in the pipeline: $2,000–$5,000 (developer time, review, testing)
- **Savings per vulnerability shifted left: $10,000–$45,000**

**Break-even calculation:**
If the program costs $200,000/year (tooling + people) and prevents 10+ production security incidents, the ROI is positive. Most organizations in the pilot phase identify significantly more than 10 real vulnerabilities that would have reached production.

---

## Vendor Selection Process

For commercial tools, use this structured evaluation process:

1. **Requirements definition** (1 week): Define must-have and nice-to-have criteria based on Phase 2 tool selection matrix
2. **Market scan** (1 week): Identify vendors in the category, eliminate those that don't meet must-haves
3. **RFI** (2 weeks): Send requirements document to shortlisted vendors, evaluate responses
4. **Proof of concept** (3–4 weeks): Run each shortlisted tool against your actual codebase with real developers
5. **PoC evaluation** (1 week): Score vendors against criteria, gather developer feedback
6. **Commercial negotiation** (2–3 weeks): Reference customers, security assessment, contract review
7. **Decision and procurement** (1 week): Award and purchase

Total: 10–13 weeks from requirements to purchase.

**PoC success criteria for vendor evaluation:**
- Tool installs and runs without significant IT friction
- Finds real vulnerabilities in the PoC codebase (not just test/demo code)
- False positive rate < 20% on real code (sample of 50+ findings)
- Developer can read and act on output without dedicated training
- Integration with the organization's CI/CD platform confirmed working
