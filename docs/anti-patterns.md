# DevSecOps Anti-Patterns Reference Library

This document catalogs the most common anti-patterns observed in DevSecOps transformation programs. Each entry describes the anti-pattern, its observable symptoms, the underlying causes, and the remediation path. Use this reference to diagnose stalled transformations, brief new team members, and structure retrospectives.

Anti-patterns are grouped into five categories: organizational, process, toolchain, cultural, and measurement.

---

## Organizational Anti-Patterns

### AP-O1: Security as a Gate, Not a Guardrail

**Description:** The security team reviews and approves every change before it reaches production. Development teams must open tickets or schedule reviews to proceed. Security is a separate lane in the delivery process, not embedded in it.

**Observable symptoms:**
- Pull requests or deployments are blocked pending security sign-off
- Security team is the bottleneck for release velocity
- Development teams describe security as "the team that says no"
- Security backlog grows faster than it can be processed

**Root causes:**
- Security team lacks automation tooling and must perform manual reviews
- Security responsibilities are not shared with developers
- Organization has historically treated security as an audit function

**Remediation:**
- Shift security controls left: automate SAST, SCA, and secrets scanning in CI so findings surface in the developer's IDE and PR, not in a separate approval process
- Define clear severity thresholds — only CRITICAL severity issues require security team involvement; everything else is the developer's responsibility to fix
- Create a security champions program so embedded champions handle routine reviews
- Reserve security team bandwidth for threat modeling, architectural review, and policy definition — not line-by-line code review

---

### AP-O2: Separate DevSecOps Team

**Description:** The organization creates a dedicated "DevSecOps team" responsible for implementing DevSecOps across the organization. Other teams wait for this team to deliver tooling, pipelines, and training before changing their practices.

**Observable symptoms:**
- DevSecOps team is working; other teams are not
- The transformation has a single point of failure
- Teams report waiting for the DevSecOps team to "finish"
- Progress stops when DevSecOps team members leave

**Root causes:**
- Misunderstanding DevSecOps as a product to be delivered rather than a cultural and operational shift
- Organizational desire to isolate transformation work to avoid disruption to delivery teams
- Leadership believes tooling alone will produce the outcome

**Remediation:**
- Reframe the DevSecOps team as a Platform Engineering or Security Platform team — they build the platform and tooling; adoption is a shared responsibility
- Every development team should have a security champion who drives adoption for their team
- Set adoption KPIs for product engineering teams, not just the platform team

---

### AP-O3: Compliance Masquerading as Security

**Description:** The DevSecOps program is scoped to satisfying audit requirements rather than reducing actual risk. Controls are implemented at minimum viable levels to pass audits, not at levels that prevent real attacks.

**Observable symptoms:**
- Conversations about security always reference compliance frameworks
- Security KPIs are framed as "controls in place" rather than "mean time to detect" or "vulnerability resolution time"
- Controls pass audits but do not detect known attack patterns
- No threat modeling; controls are mapped from checklists

**Root causes:**
- Program originated from compliance pressure rather than a security incident or risk assessment
- Leadership measures success through audit reports
- Security team has a compliance background rather than an adversarial/red team background

**Remediation:**
- Supplement compliance-driven controls with risk-based controls derived from threat modeling
- Measure security outcomes (MTTD, MTTR, vulnerability age) alongside compliance status
- Conduct annual red team or penetration testing to validate that compliant controls are also effective controls

---

## Process Anti-Patterns

### AP-P1: Security Testing Only at the End of the Sprint

**Description:** Security testing — DAST, penetration testing, security review — is scheduled at the end of the sprint or at the end of a release cycle. Security findings discovered at this point are too late to be fixed without delaying the release.

**Observable symptoms:**
- Security findings are deferred to "future sprints" because fixing them would miss the current deadline
- DAST is run manually by the security team on a quarterly or release-gated basis
- "Security sprint" is a recognized concept — entire sprints devoted to security debt catchup

**Root causes:**
- Security testing requires a running environment, which was not available earlier in the cycle
- Security team capacity limits how often they can run tests
- No automated DAST or IAST in the CI/CD pipeline

**Remediation:**
- Run SAST, SCA, and secrets scanning on every PR — findings are visible when the code is written, not weeks later
- Run automated DAST (OWASP ZAP baseline scan) against the staging environment on every deployment
- Reserve manual security testing for deep assessments of new features; automate regression testing
- Treat security findings with the same severity triage as production bugs — CRITICAL blocks release; HIGH has a 30-day SLA

---

### AP-P2: Shared Pipeline Credentials

**Description:** Multiple teams, services, or pipelines share a single set of credentials (deploy keys, service account tokens, cloud IAM keys) to access deployment targets or secrets.

**Observable symptoms:**
- A single deploy key is used across dozens of repositories
- Cloud IAM access keys are hard-coded in CI/CD environment variables shared across teams
- A credential rotation event affects all teams simultaneously
- When a credential is compromised, the blast radius is unknown

**Root causes:**
- Operational convenience — setting up per-service credentials is more work
- Lack of OIDC federation knowledge
- Legacy CI systems that do not support dynamic credential issuance

**Remediation:**
- Migrate to OIDC federation — each pipeline gets a short-lived, scoped cloud credential based on the pipeline's identity (repository, branch, workflow)
- Eliminate shared service accounts; every service should have its own identity
- Audit credential usage with cloud IAM access logs — any credential that hasn't been used in 90 days should be revoked

---

### AP-P3: Security Findings Are Logged, Not Resolved

**Description:** The CI pipeline runs security scans and reports findings, but there is no defined ownership, SLA, or escalation process for resolving them. Findings accumulate in scan reports without being addressed.

**Observable symptoms:**
- SCA dashboard shows 300+ open vulnerabilities, growing over time
- Teams suppress or accept findings to unblock deployments without proper review
- SAST findings are dismissed as false positives without investigation
- "Known vulnerabilities" list in security dashboard has items from 18 months ago

**Root causes:**
- No defined SLA for vulnerability resolution
- No ownership assignment — findings are surfaced in a shared dashboard that no one is responsible for
- Teams treat security scan pass/fail as a checkbox, not a queue of work

**Remediation:**
- Define severity-based SLAs (CRITICAL: 7 days, HIGH: 30 days, MEDIUM: 90 days) and measure compliance
- Assign vulnerability ownership to the team that owns the affected service, not the security team
- Block CI builds on CRITICAL severity — treat it as a breaking test, not an advisory
- Require formal risk acceptance with an owner, justification, and expiry date for any finding that cannot be resolved within SLA

---

### AP-P4: Immature Threat Modeling Process

**Description:** Threat modeling exists on paper but is not integrated into the development workflow. It is performed infrequently, by security specialists only, and does not result in actionable control changes.

**Observable symptoms:**
- Threat models are produced by security team at project start and never revisited
- Developers are not involved in threat modeling
- Threat model outputs are not linked to user stories, test cases, or security controls
- Architecture changes are deployed without a corresponding threat model update

**Root causes:**
- Threat modeling requires specialized skills not distributed across engineering
- No tooling to make threat modeling accessible to developers
- No defined trigger for when to perform or update a threat model

**Remediation:**
- Adopt lightweight threat modeling practices (STRIDE-per-element, LINDDUN) that developers can apply with 2–4 hours of training
- Use threat modeling tools (OWASP Threat Dragon, IriusRisk, Threatspec) to make the process structured and trackable
- Define triggers: any change touching authentication, authorization, data handling, external integrations, or trust boundaries requires a threat model update
- Link threat model findings to security test cases — every identified threat should have at least one automated test

---

## Toolchain Anti-Patterns

### AP-T1: Tool Sprawl Without Integration

**Description:** The organization has acquired many security tools — SAST, DAST, SCA, CSPM, container scanning, secrets detection — but they operate independently. Findings are siloed in separate dashboards, there is no unified view of risk, and deduplication is manual.

**Observable symptoms:**
- Security team maintains 8+ separate security tool dashboards
- The same vulnerability appears in three different tools with no cross-reference
- No single place to answer "what is our current vulnerability count across all services?"
- Tooling evaluation and procurement happens faster than adoption

**Root causes:**
- Tool acquisition driven by compliance requirements or vendor relationships without a unified platform strategy
- Each team chose their own security tools without coordination
- No integration layer connecting tools to a common vulnerability management platform

**Remediation:**
- Adopt a vulnerability management platform (Dependency-Track, DefectDojo, Wiz, Snyk) as the aggregation point for all scanner outputs
- Standardize scanner output on SARIF or CycloneDX for tools that support it
- Rationalize the toolchain — if two tools detect the same issue class, keep one
- Measure tool signal-to-noise ratio; tools with >40% false positive rate require tuning or replacement

---

### AP-T2: Security Tools as Bureaucracy Makers

**Description:** Security tooling adds friction to the development workflow without adding proportionate value. Developers spend significant time triaging false positives, filing exceptions, and waiting for scans to complete.

**Observable symptoms:**
- Build times have tripled since security scanning was added, with no reduction in security incidents
- Security scan false positive rate is above 30%
- Developers routinely bypass security gates using exception flags or configuration overrides
- Engineering leadership views security tooling as a productivity tax

**Root causes:**
- Tools configured with default settings not tuned to the codebase
- All findings treated as equal priority regardless of exploitability or context
- Scanning added to the critical path without optimizing for speed (parallelization, caching, incremental scanning)

**Remediation:**
- Tune scanner rules to the language and framework — disable rules that don't apply, add suppression for known false positives with documented justification
- Run scanners in parallel, not serial; move long-running scans (DAST, container scanning) to non-blocking asynchronous jobs that report results without blocking the build
- Block only on CRITICAL and HIGH severity with high confidence; treat MEDIUM and LOW as informational
- Measure and publish scanner metrics: false positive rate, median fix time, blocked builds per week

---

### AP-T3: Configuration Drift Between Environments

**Description:** Security controls are applied differently across development, staging, and production environments. Findings that would be caught in production are missed in staging because the environments are configured differently.

**Observable symptoms:**
- Production incidents reveal vulnerabilities that were not found in staging
- Pod Security Admission is enforced in production but not in development
- Network policies exist in production but are absent in staging
- IaC scanning runs in CI but infrastructure is modified manually in production

**Root causes:**
- Infrastructure was built incrementally, with production receiving hardening that was never backported to lower environments
- Developer experience prioritized over environment consistency
- No drift detection between environments

**Remediation:**
- Enforce identical policy baselines across all environments using a shared Kyverno/OPA policy library; only allow environment-specific overrides for resource limits and replica counts, never for security controls
- Use IaC for all environments — Terraform or Pulumi modules should produce the same security configuration regardless of target environment
- Run automated compliance checks in all environments, not just production

---

## Cultural Anti-Patterns

### AP-C1: Security Champions in Name Only

**Description:** A security champions program exists on paper — champions are designated and announced — but they receive no training, tooling, time allocation, or recognition. Champions cannot answer security questions for their team and are not consulted in design decisions.

**Observable symptoms:**
- Security champions list is out of date; several champions have left or changed roles
- Champions report spending less than 30 minutes per week on security activities
- Teams still route all security questions to the central security team, bypassing the champion
- Champions were assigned, not volunteers

**Root causes:**
- Program was launched for optics without a sustainable model
- Champions receive no dedicated time in their sprint allocation
- Security team does not invest in champion enablement (training, escalation paths, tools)

**Remediation:**
- Champions should volunteer, not be assigned
- Allocate 10–20% of a champion's sprint capacity to security work
- Provide a structured training curriculum, access to security tooling, and a direct escalation path to senior security engineers
- Recognize champions in team-level and org-level communications; tie champion activity to career development conversations

---

### AP-C2: Blameful Incident Culture

**Description:** Security incidents result in investigation of individual responsibility and attribution of blame rather than systemic analysis of how the incident occurred and what controls failed.

**Observable symptoms:**
- Post-mortems conclude with personnel disciplinary actions
- Teams hide near-misses to avoid blame
- Engineers fear deploying to production because they will be held personally accountable for incidents
- Security incidents are classified as confidential and not shared broadly

**Root causes:**
- Leadership has not adopted blameless post-mortem culture
- Security incidents are treated differently from reliability incidents
- Regulatory or legal pressure to identify responsible individuals

**Remediation:**
- Apply the same blameless post-mortem process to security incidents as to reliability incidents
- Focus analysis on contributing factors, system design, and control gaps rather than individual actions
- Publish incident post-mortems internally (within the company) to normalize transparency and learning
- Reserve personal accountability for deliberate policy violations, not for accidents in complex systems

---

## Measurement Anti-Patterns

### AP-M1: Vanity Metrics

**Description:** The DevSecOps program measures and reports metrics that are easy to collect but do not reflect the security posture or business risk. Metrics improve on paper while actual security outcomes do not change.

**Observable symptoms:**
- Primary metrics are "number of scans run" or "number of tools deployed"
- Dashboard shows 0 CRITICAL vulnerabilities — because all CRITICAL findings are accepted as risk exceptions
- Time-to-fix metrics exclude deferred findings
- Security score improves quarter-over-quarter but security incidents also increase

**Root causes:**
- Metrics were chosen based on what was easy to measure, not what was meaningful
- Program is evaluated by the team that runs it; no independent validation
- Leadership requests positive trend lines; the program delivers them by adjusting methodology

**Remediation:**
- Measure outcomes, not activity: mean time to detect (MTTD), mean time to remediate (MTTR), percentage of findings resolved within SLA, security incident rate
- Include aging analysis — what percentage of open findings are beyond SLA age?
- Report risk exception count and average exception age alongside resolved vulnerability counts
- Conduct external validation (penetration test, red team exercise) at least annually to provide an independent view of security posture

---

### AP-M2: Measuring Process Compliance Instead of Risk Reduction

**Description:** Security metrics confirm that processes were followed — scans ran, reviews were completed, training was attended — but do not measure whether the program is reducing actual security risk.

**Observable symptoms:**
- Security scorecard shows 100% compliance with all process requirements
- No correlation between process metrics and security incident rate
- Mean time to detect a production security incident is unknown
- Vulnerability density (findings per 1,000 lines of code) is not tracked

**Root causes:**
- Metrics framework was derived from compliance frameworks that measure control existence, not effectiveness
- Program does not have a baseline to measure improvement against

**Remediation:**
- Add a risk reduction layer to all process metrics: "What is the expected risk reduction if this process is followed correctly?"
- Establish baselines at program inception: vulnerability density, mean time to patch, incident rate, scan coverage percentage
- Track metrics over time against these baselines, not just point-in-time snapshots
- Include adversarial metrics: "Would our current controls have prevented the top 5 attack techniques in the MITRE ATT&CK CI/CD matrix?"

---

## Quick Reference Table

| Anti-Pattern | Category | Risk Level | Common Trigger |
|-------------|----------|------------|----------------|
| Security as a Gate | Organizational | High | Legacy security team model |
| Separate DevSecOps Team | Organizational | Medium | Transformation isolation strategy |
| Compliance Masquerading as Security | Organizational | High | Audit-driven program origin |
| Security Testing Only at End | Process | High | Late-stage quality model |
| Shared Pipeline Credentials | Process | Critical | Operational convenience |
| Findings Logged, Not Resolved | Process | High | No SLA or ownership |
| Immature Threat Modeling | Process | Medium | Skill gap, no tooling |
| Tool Sprawl | Toolchain | Medium | Vendor-driven procurement |
| Security Tools as Bureaucracy | Toolchain | Medium | Untune default configurations |
| Config Drift Between Environments | Toolchain | High | Incremental hardening pattern |
| Champions in Name Only | Cultural | Medium | Top-down program launch |
| Blameful Incident Culture | Cultural | High | Punitive accountability model |
| Vanity Metrics | Measurement | High | Reporting pressure |
| Process Compliance Metrics | Measurement | Medium | Compliance framework origin |

---

## Related Documents

- [DevSecOps Methodology Framework](framework.md) — Transformation methodology and implementation playbook
- [Implementation Guide](implementation.md) — 90-day transformation roadmap
- [DevSecOps Maturity Model](../../devsecops-maturity-model/docs/framework.md) — Maturity assessment framework
- [DevSecOps Framework Best Practices](../../devsecops-framework/docs/best-practices.md) — Positive practices catalog
