# Methodology Architecture

## Overview

This document describes the structural architecture of the Techstream DevSecOps Transformation Methodology — the four-phase framework, operating model design patterns, governance architecture, toolchain decision framework, metrics architecture, and organizational design options. It is intended to guide transformation leads in designing the program structure before implementation begins.

---

## Four-Phase Framework

The methodology is organized into four sequential phases. Each phase has defined inputs, activities, outputs, and success criteria. Phases may overlap in practice — Phase 3 (Implement) often begins with the pilot team while Phase 2 (Design) is still in progress for the broader organization.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 DEVSECOPS TRANSFORMATION METHODOLOGY                        │
│                                                                             │
│  PHASE 1           PHASE 2           PHASE 3           PHASE 4              │
│  ASSESS            DESIGN            IMPLEMENT         OPTIMIZE             │
│  ──────────────    ──────────────    ──────────────    ──────────────       │
│                                                                             │
│  Current state     Target state      Pilot → Scale     Measure →            │
│  assessment        definition        rollout           improve              │
│                                                                             │
│  Inputs:           Inputs:           Inputs:           Inputs:              │
│  • Interviews      • Phase 1 report  • Phase 2 design  • Metrics data       │
│  • Tool inventory  • Threat model    • Approved budget • Retrospective      │
│  • Risk data       • Constraints     • Pilot team      • findings           │
│                    • Benchmarks      identified                             │
│                                                                             │
│  Outputs:          Outputs:          Outputs:          Outputs:             │
│  • Current state   • Target arch     • Running pilot   • Updated metrics    │
│  • Gap analysis    • Toolchain       • Scale plan      • Optimized          │
│  • SAMM score      • RACI matrix     • Governance      • controls           │
│  • Risk register   • KPI framework   • Training        • Maturity           │
│  • Roadmap draft   • Champions pgm   • delivered       • advancement        │
│                    • design                                                 │
│                                                                             │
│  Duration: 4–6w    Duration: 4–6w    Duration: 12–18m  Duration: Ongoing    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Assess — Activities in Detail

### Current State Analysis

Current state analysis combines quantitative data gathering with qualitative stakeholder research. Neither is sufficient alone: metrics without context mislead, and interviews without data produce anecdote-driven recommendations.

**Quantitative inputs:**
- Deployment frequency (DORA)
- Lead time for changes (DORA)
- Change failure rate (DORA)
- Mean time to restore (DORA)
- Number of open vulnerabilities by severity across known scanners
- Time-to-fix metrics for past vulnerabilities (if tracked)
- Security incident frequency and severity
- Code coverage percentages

**Qualitative inputs:**
- Stakeholder interviews (engineering leads, security team, CISO, product management)
- Developer survey on security perception and friction
- Security team survey on escalation patterns and blockers
- Observation sessions in developer workflows (how do developers currently handle security feedback?)

### SAMM Assessment

Use the OWASP SAMM assessment questionnaire to score the organization across all 15 security practices. This produces a maturity scorecard that:
- Provides a defensible baseline for measuring transformation progress
- Identifies the practices with the largest gaps relative to the target state
- Enables benchmarking against industry norms (SAMM publishes benchmark data)

SAMM practices by business function:

| Business Function | Security Practice |
|---|---|
| Governance | Strategy & Metrics, Policy & Compliance, Education & Guidance |
| Design | Threat Assessment, Security Requirements, Security Architecture |
| Implementation | Secure Build, Secure Deployment, Defect Management |
| Verification | Architecture Assessment, Requirements-driven Testing, Security Testing |
| Operations | Incident Management, Environment Management, Operational Management |

Target SAMM scores should be set in Phase 2 based on the organization's risk profile and compliance requirements, not arbitrary "industry standards."

### Stakeholder Interview Guide

Recommended interview questions for each stakeholder group:

**Engineering Leadership:**
- How frequently do security issues cause deployment delays today?
- What is your current process for addressing vulnerabilities found in code?
- How does your team receive security guidance or requirements?
- What would make security tooling more useful and less disruptive to your team?

**Security Team:**
- What percentage of your time is spent on reactive incident response vs. proactive enablement?
- Where do you see the most vulnerabilities being introduced in the SDLC?
- What are the biggest blockers to your team providing security guidance earlier in the process?
- What tools are currently in use, and how well are they adopted by engineering teams?

**CISO / Security Leadership:**
- What are the top three security risks you believe are inadequately addressed today?
- What compliance requirements drive the most security work currently?
- What does success look like for this transformation program in 18 months?
- What organizational blockers do you anticipate, and who can help remove them?

**Product Management:**
- How do security requirements get incorporated into the product backlog today?
- Have security issues caused customer-facing incidents in the past year?
- What is your appetite for build breaks caused by security gate enforcement?

---

## Phase 2: Design — Operating Model Architecture

### Operating Model Design Patterns

The operating model determines how security work is distributed across the organization. Three primary patterns exist:

#### Pattern A: Centralized Security (Security Team as Service Provider)

```
┌──────────────────────────────────────────────────────────────────┐
│                    CENTRALIZED SECURITY MODEL                    │
│                                                                  │
│   ┌────────────────┐     ┌─────────────┐     ┌───────────────┐  │
│   │ Engineering    │────▶│  Security   │────▶│  Compliance   │  │
│   │ Team (builds)  │◀────│  Team       │     │  & Audit      │  │
│   └────────────────┘     │  (reviews,  │     └───────────────┘  │
│                          │  scans,     │                        │
│   ┌────────────────┐     │  approves)  │                        │
│   │ Engineering    │────▶│             │                        │
│   │ Team (builds)  │◀────└─────────────┘                        │
│   └────────────────┘                                            │
└──────────────────────────────────────────────────────────────────┘
```

**Strengths:** Consistent policy application; security specialists concentrated.
**Weaknesses:** Does not scale beyond ~3 development teams per security engineer; creates bottleneck; security team becomes a blocker rather than enabler; developers do not develop security skills.

**Appropriate for:** Small organizations (< 5 development teams), highly regulated environments where centralized control is required, early stages of maturity.

#### Pattern B: Federated Security (Security Champions Model) — Recommended

```
┌──────────────────────────────────────────────────────────────────┐
│                    FEDERATED SECURITY MODEL                      │
│                                                                  │
│   Platform Security Team (Enablers)                              │
│   ┌──────────────────────────────────────────────────────────┐  │
│   │ • Security platform & toolchain ownership                 │  │
│   │ • Security champions program management                   │  │
│   │ • Security standards and policy                           │  │
│   │ • Training and enablement                                 │  │
│   └────────────────────────────┬─────────────────────────────┘  │
│                                 │ Enables / supports             │
│            ┌────────────────────┼────────────────────┐          │
│            ▼                    ▼                    ▼          │
│   ┌─────────────────┐  ┌──────────────────┐  ┌──────────────┐  │
│   │  Team A         │  │  Team B          │  │  Team C      │  │
│   │  [Champion ★]   │  │  [Champion ★]    │  │  [Champion ★]│  │
│   │  Owns security  │  │  Owns security   │  │  Owns        │  │
│   │  in their team  │  │  in their team   │  │  security    │  │
│   └─────────────────┘  └──────────────────┘  └──────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

**Strengths:** Scales proportionally with engineering team growth; developers develop genuine security skills; security becomes embedded in team culture; security team focuses on high-value enabling work.
**Weaknesses:** Champions require ongoing investment; quality of security work varies by champion skill level; requires mature champions program management.

**Appropriate for:** Most organizations with 5+ development teams.

#### Pattern C: Platform Engineering Model

In this pattern, security is a first-class feature of the internal developer platform. Security controls are provided as platform services that development teams consume, rather than guidance they implement themselves.

```
┌──────────────────────────────────────────────────────────────────┐
│                  PLATFORM ENGINEERING MODEL                      │
│                                                                  │
│  Internal Developer Platform                                     │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  ┌─────────────┐  ┌────────────────┐  ┌──────────────┐  │   │
│  │  │ Secure       │  │ Pre-configured │  │ SBOM         │  │   │
│  │  │ Pipeline     │  │ Scanning       │  │ Generation   │  │   │
│  │  │ Templates    │  │ Gates          │  │ Service      │  │   │
│  │  └─────────────┘  └────────────────┘  └──────────────┘  │   │
│  │  ┌─────────────┐  ┌────────────────┐  ┌──────────────┐  │   │
│  │  │ Secrets      │  │ Policy-as-Code │  │ Evidence     │  │   │
│  │  │ Management   │  │ Enforcement    │  │ Archival     │  │   │
│  │  └─────────────┘  └────────────────┘  └──────────────┘  │   │
│  └──────────────────────────────────────────────────────────┘   │
│                         ▲ Platform teams consume                 │
│   Dev Team A    Dev Team B    Dev Team C    Dev Team D           │
└──────────────────────────────────────────────────────────────────┘
```

**Strengths:** Eliminates variation in security practice quality across teams; security teams focus on platform rather than team-level consulting; scales to large organizations.
**Weaknesses:** Requires significant upfront platform investment; developers may lose security understanding if controls become invisible.

**Appropriate for:** Large organizations (50+ development teams) with mature platform engineering capabilities.

---

## Governance Architecture

### Governance Levels

Effective DevSecOps governance operates at three levels:

**Level 1: Strategic Governance (Quarterly)**
- Audience: CISO, CTO, Engineering VPs, Compliance
- Topics: Program progress vs. milestones, maturity advancement, risk posture trends, compliance evidence review, budget and resource review
- Artifact: Quarterly Business Review (QBR) deck

**Level 2: Operational Governance (Monthly)**
- Audience: Security leads, engineering leads, platform team
- Topics: Metrics review, SLA compliance, tool and ruleset updates, emerging threats, blockers
- Artifact: Monthly security metrics report

**Level 3: Team-Level Governance (Weekly/Sprint)**
- Audience: Security champions, team engineers, scrum masters
- Topics: Open vulnerability backlog review, new findings triage, tool friction issues, upcoming compliance milestones
- Artifact: Security backlog in team's issue tracker

### Security Review Board

For larger organizations, establish a Security Review Board (SRB) that:
- Approves exceptions to security gates (with documented justification and time limits)
- Reviews and approves new tool introductions
- Reviews and approves changes to severity thresholds
- Arbitrates conflicts between security and engineering on remediation priorities

SRB membership: CISO (or delegate), CTO/Engineering VP (or delegate), Lead Security Architect, two rotating Development Team Leads.

---

## Toolchain Architecture Decision Framework

### Tool Selection Criteria

Evaluate tools against the following criteria matrix:

| Criterion | Weight | Description |
|---|---|---|
| Detection accuracy | 25% | True positive rate; false positive rate for the tool's target vulnerability class |
| Developer experience | 20% | Speed, clarity of output, actionable remediation guidance, IDE integration |
| Integration compatibility | 15% | Native support for the organization's CI/CD platforms, issue trackers, SIEMs |
| Maintenance burden | 15% | Update frequency, rule maintenance requirements, configuration complexity |
| Total cost | 10% | License cost + implementation cost + ongoing operational cost |
| Vendor stability | 10% | Financial health, community size, support quality |
| Compliance coverage | 5% | Mapping to required compliance controls (PCI, SOC2, NIST, etc.) |

### Build vs. Buy vs. Open Source Decision

| Scenario | Recommended Approach |
|---|---|
| Commodity function with mature OSS option (e.g., secrets scanning, container scanning) | Open source (Gitleaks, Trivy) |
| Commodity function with compliance reporting requirements | Commercial (often provides audit-ready reports) |
| Highly specialized or language-specific analysis | Commercial (Semgrep Pro, Checkmarx, Veracode) |
| Functions tightly integrated with developer workflow | Commercial (Snyk, GitHub Advanced Security) — developer adoption is often higher |
| Niche function not well-served by market | Build (rare — avoid unless truly necessary) |

### Toolchain Selection Checklist

Before finalizing tool selection, verify:
- [ ] Tool evaluated against a representative sample of the organization's actual codebase (not a benchmark dataset)
- [ ] False positive rate measured and acceptable for blocking gate use
- [ ] Integration with existing CI/CD platform confirmed working
- [ ] Output format compatible with existing SIEM/GRC tooling (or tolerable SIEM work estimated)
- [ ] Developer pilot feedback gathered (at least 5 developers used the tool for 2+ weeks)
- [ ] Vendor security assessed (e.g., SOC 2 Type II, penetration test results available)
- [ ] License reviewed by legal for open-source components
- [ ] Operational model agreed: who configures, who maintains, who responds to alerts?

---

## Metrics Architecture

### Leading vs. Lagging Indicators

The metrics program must include both leading indicators (measure process inputs that predict future security outcomes) and lagging indicators (measure past security outcomes). Organizations that measure only lagging indicators learn too late that their program is failing.

**Leading indicators (measure weekly/monthly):**

| Metric | What It Predicts |
|---|---|
| % repositories with all security pipeline stages active | Future vulnerability escape rate |
| Developer satisfaction score with security tooling (survey) | Future tool adoption and compliance |
| Mean time from vulnerability introduction to detection (MTTD) | Breach dwell time if exploited |
| Security training completion rate | Future developer security decision quality |
| Backlog aging — % of open findings older than SLA | Future risk accumulation |

**Lagging indicators (measure monthly/quarterly):**

| Metric | What It Measures |
|---|---|
| Vulnerability escape rate (found in prod / found in pipeline) | Historical program effectiveness |
| Security incidents attributable to software vulnerabilities | Historical program effectiveness |
| Mean time to fix by severity | Historical remediation velocity |
| Compliance evidence completeness | Historical audit readiness |
| SAMM score by business function | Historical maturity progression |

### Metrics Collection Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    METRICS DATA FLOW                            │
│                                                                 │
│  CI/CD Pipeline                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Scan results  →  Pipeline artifact store  →  SIEM/ASPM  │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                          │       │
│  Vulnerability Management Platform (Jira, DefectDojo)    │       │
│  ┌────────────────────────────────────┐                  │       │
│  │  Finding lifecycle tracking         │◀─────────────────┘       │
│  │  SLA compliance monitoring          │                          │
│  │  False positive management          │                          │
│  └──────────────┬─────────────────────┘                          │
│                 │                                                 │
│                 ▼                                                 │
│  Metrics Dashboard (Grafana, PowerBI, Tableau)                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Executive view: risk posture, program progress          │  │
│  │  Operational view: SLA compliance, tool health           │  │
│  │  Team view: open findings, backlog burn-down             │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Security Champions Program Design

The security champions program is the primary mechanism for scaling security expertise into development teams without proportionally scaling the security team.

### Champion Role Definition

| Dimension | Description |
|---|---|
| Selection | Volunteer (never mandate); developers with existing security interest perform best |
| Time allocation | Typically 10–20% of sprint capacity; must be formally protected by team lead |
| Reporting | Dual: reports to engineering team lead (delivery) and security team (security competency) |
| Recognition | Public recognition, conference attendance, security team access, role title |
| Tenure | Typically 12–18 months; rotation prevents champion burnout and spreads knowledge |

### Champion Responsibilities

Champions are not "mini security engineers" — they are security-aware developers who:
- Act as the first point of contact within their team for security questions
- Review pull requests for security issues before they reach the security team
- Coordinate vulnerability remediation within the team backlog
- Attend monthly security champions community of practice (CoP)
- Communicate security program updates to their team
- Provide feedback on tool friction and false positive rates to the security team

Champions should NOT:
- Be expected to do formal security architecture reviews
- Be the sole owner of security outcomes for their team (security is the whole team's responsibility)
- Work on security tooling maintenance (that is the platform security team's job)

### Champion Training Curriculum

| Module | Duration | Topics |
|---|---|---|
| Foundations | 4 hours | OWASP Top 10, secure coding principles, threat modeling basics |
| Tool Practicum | 4 hours | Using SAST/SCA/DAST tools, reading findings, triaging false positives |
| Code Review | 3 hours | Security-focused code review techniques for the team's primary language |
| Threat Modeling | 4 hours | STRIDE methodology, attack trees, threat modeling workshops |
| Incident Response | 2 hours | What to do when a security issue is found in production |
| Advanced Topics | 4 hours (per topic) | Cloud security, container security, API security, cryptography |

Champions complete Foundations, Tool Practicum, and Code Review in their first month. Advanced topics are delivered over the following 6 months.

---

## Organizational Design Patterns

### Dedicated Transformation Team

For organizations running a formal transformation program, a dedicated team with cross-functional membership is recommended:

| Role | Responsibility | FTE |
|---|---|---|
| Transformation Lead | Program management, stakeholder alignment, executive reporting | 1.0 |
| Security Architect | Technical design, toolchain selection, pipeline architecture | 1.0 |
| Platform Engineer | Tool deployment, CI/CD integration, automation | 0.5–1.0 |
| Change Management Lead | Developer communications, training, champions program | 0.5 |
| Embedded Security Engineer (per pilot team) | Hands-on support for pilot team security adoption | 1.0 per pilot team |

### Integration with Existing Security Team

The transformation team should not operate as a separate entity from the existing security team. Transformation roles should be staffed from within the security team (or backfilled externally), and transformation outputs — toolchains, pipelines, governance — should transfer to the security team for ongoing ownership before the transformation program concludes.

A transformation program that ends with the security team saying "we don't know how any of this works" has failed.

### Target Operating Model: Platform Security Team

The target state for most organizations is a Platform Security Team that:
- Owns the security toolchain and pipeline templates (technology platform)
- Owns the security champions program (people platform)
- Owns the security standards and governance (policy platform)
- Provides advisory services to development teams on request
- Does NOT review every piece of code or approve every deployment

This team is typically 3–8 people for a 50-200 person engineering organization, depending on risk profile.
