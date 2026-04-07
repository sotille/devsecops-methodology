# Security Champion Program: Curriculum and Implementation Guide

A security champion is a software engineer or technical lead who takes on a part-time, voluntary role as the security focal point for their team. Security champions do not replace security engineers — they amplify the security team's reach across the organization by embedding security awareness, practices, and tooling within individual engineering teams.

This guide provides a complete program curriculum, onboarding structure, ongoing engagement model, and measurement framework for a security champion program.

---

## Program Rationale

At the core of DevSecOps is the principle that security is a shared responsibility. Security teams cannot review every pull request, attend every design meeting, or respond to every developer question. A security champion program creates a distributed network of trained practitioners who:

- Conduct first-pass security reviews of code, design decisions, and architecture proposals within their team
- Facilitate threat modeling sessions without requiring a security engineer to be present
- Escalate real security concerns early, before they become production vulnerabilities
- Act as a feedback channel for security tooling — surfacing false positives, friction points, and adoption blockers
- Promote security culture through example, not mandate

The return on investment is high: a well-run security champion program delivers security leverage at a fraction of the cost of expanding the security headcount proportionally.

---

## Program Structure

### Eligibility and Selection

Security champions should be self-selected, not assigned. Mandatory participation undermines the program's effectiveness — a reluctant champion does more harm than no champion.

**Ideal candidate profile:**
- Experienced engineer with credibility on their team (typically 2+ years at the organization)
- Demonstrated interest in security topics (has flagged security concerns before; attends security events; reads security content voluntarily)
- Good communicator — can translate security concepts to non-security colleagues
- Not already overcommitted — has capacity for approximately 10–20% time commitment

**Selection process:**
1. Program sponsor announces the security champion program and its purpose to all engineers
2. Interested engineers self-nominate or are nominated by their manager
3. Security team conducts a brief discussion with each nominee to assess fit and interest
4. One security champion per engineering team (10–15 person teams as a typical size)
5. Review champion nominations annually; champions can step down without stigma

### Time Commitment

| Activity | Approximate Time |
|----------|-----------------|
| Initial onboarding curriculum | 16 hours (over 4 weeks) |
| Monthly security champion meeting | 1 hour / month |
| Ongoing learning (articles, training, CTF) | 2–3 hours / month |
| Team-level security activities (reviews, threat modeling) | 2–4 hours / month |
| **Total ongoing commitment** | **5–8 hours / month (~10–20%)** |

This time commitment must be formally acknowledged by the champion's manager and protected from other demands. A security champion program that is not given protected time will fail within 6 months.

---

## Onboarding Curriculum

The onboarding curriculum is structured as a 4-week program delivered as self-paced modules plus live sessions. Total effort: approximately 16 hours.

### Week 1: Foundations

**Self-paced (4 hours):**

| Module | Content | Resource |
|--------|---------|---------|
| 1.1 | Threat modeling introduction — STRIDE method, threat identification, control selection | [Threat Modeling: Designing for Security](https://www.threatmodelingbook.com/) — Chapters 1–3 |
| 1.2 | The OWASP Top 10 (2021) — understanding each risk category with real examples | OWASP Top 10 documentation |
| 1.3 | Secure coding principles — input validation, output encoding, least privilege, fail securely | Organization-specific secure coding guidelines |
| 1.4 | Our DevSecOps pipeline — how SAST, SCA, and DAST are configured, what they catch, and what they miss | [DevSecOps Framework](../../devsecops-framework/docs/framework.md) |

**Live session (1 hour):** Welcome meeting with the security team. Overview of the program, introductions, Q&A.

**Week 1 checkpoint:** Champions should be able to name the STRIDE categories and explain the OWASP Top 10 risks in their own words.

---

### Week 2: Application Security Practicals

**Self-paced (4 hours):**

| Module | Content | Resource |
|--------|---------|---------|
| 2.1 | Authentication and authorization deep dive — JWT, OAuth 2.0 / OIDC, RBAC, ABAC, BOLA | OWASP Authentication Cheat Sheet; [API Security Guide](../../devsecops-framework/docs/api-security.md) |
| 2.2 | Injection vulnerabilities — SQL injection, command injection, template injection, SSRF | OWASP Injection Cheat Sheet |
| 2.3 | Secrets management — why secrets in code happen and how to prevent them; secrets manager setup | [Software Supply Chain Security Framework](../../software-supply-chain-security-framework/docs/framework.md) — Section: Secrets |
| 2.4 | Cryptography for developers — what algorithms to use and why; TLS; hashing vs. encryption | OWASP Cryptographic Storage Cheat Sheet |

**Hands-on exercise (1 hour):** Participate in a Capture The Flag (CTF) exercise focusing on injection and authentication vulnerabilities. Use OWASP WebGoat or a similar intentionally vulnerable application.

**Week 2 checkpoint:** Champions can identify common injection vulnerabilities in a code review and explain why a given secrets management approach is secure or insecure.

---

### Week 3: Supply Chain Security and Infrastructure

**Self-paced (4 hours):**

| Module | Content | Resource |
|--------|---------|---------|
| 3.1 | Software supply chain risks — SolarWinds, Log4Shell, XZ Utils: what happened and why | [Software Supply Chain Security Framework — Introduction](../../software-supply-chain-security-framework/docs/introduction.md) |
| 3.2 | SBOM and dependency management — reading an SBOM, interpreting SCA results, triage | [SBOM Format and Tool Selection Guide](../../software-supply-chain-security-framework/docs/sbom-guide.md) |
| 3.3 | Pipeline security — what our CI/CD pipeline protects and what it doesn't | [Secure CI/CD Reference Architecture](../../secure-ci-cd-reference-architecture/docs/architecture.md) |
| 3.4 | Cloud and Kubernetes security basics — IAM least privilege, network segmentation, secrets | [Cloud Security DevSecOps Framework](../../cloud-security-devsecops/docs/framework.md) |

**Hands-on exercise (1 hour):** Review the SCA output for one of the team's applications. Identify the top 3 highest-severity findings and draft a triage decision (fix, accept with justification, or false positive) with rationale.

**Week 3 checkpoint:** Champions can read a CycloneDX or SPDX SBOM and identify potentially risky components. They can explain the security controls in the CI/CD pipeline to a colleague.

---

### Week 4: Facilitation and Communication Skills

**Self-paced (3 hours):**

| Module | Content | Resource |
|--------|---------|---------|
| 4.1 | How to run a threat modeling session — preparation, facilitation, output documentation | OWASP Threat Modeling Playbook; internal threat model template |
| 4.2 | How to communicate security risk to non-security stakeholders | Adam Shostack: "Threat Modeling" — Chapter on communication |
| 4.3 | How to do a security-focused code review — what to look for, how to give feedback | Internal secure code review checklist |
| 4.4 | Working with the security team — escalation paths, how to engage, what constitutes an incident | Internal security team operating model |

**Live session (1 hour):** Facilitated threat modeling exercise on a real or synthetic design proposal. Security engineer observes and gives feedback.

**Final assessment:**
- Facilitate a threat modeling session for a real feature on their team, with a security engineer observing (not leading)
- Submit a completed threat model document
- Complete a 20-question knowledge assessment (passing score: 80%)

---

## Ongoing Program Activities

### Monthly Security Champion Meeting

A 60-minute meeting for all security champions and the security team. Format:

| Time | Content |
|------|---------|
| 0–10 min | Security news: relevant vulnerabilities, incidents, or policy changes from the past month |
| 10–30 min | Champion showcase: one champion presents a security issue they identified and resolved in their team |
| 30–45 min | Skill development: deep-dive on a specific topic (rotating presenter — security engineer or external speaker) |
| 45–60 min | Open Q&A and feedback: champions raise blockers, tooling issues, or questions |

The meeting notes are published to a shared channel so non-attending engineers can stay informed.

### Annual Training Refresh

Each year, champions complete an updated training module covering:
- New vulnerabilities or attack patterns relevant to the organization's technology stack
- Changes to security tooling, policies, or processes
- Review of incidents from the past year and lessons learned

Champions who miss the annual refresh without a documented exception are removed from the active champion list.

### Capture The Flag (CTF) Participation

Organize an annual internal CTF event, or sponsor champion participation in an external CTF (picoCTF, OWASP WebGoat, HackTheBox). Practical attack simulation is the most effective way to build and maintain security intuition.

---

## Champion Responsibilities

### Routine Responsibilities (Monthly)

| Responsibility | Detail |
|----------------|--------|
| Security review of PRs | Review at least one PR per sprint for security issues; use the secure code review checklist |
| SCA triage | Review new SCA findings for their team's repositories; triage and escalate Critical findings within 24 hours |
| Threat model facilitation | Facilitate a threat modeling session for any significant new feature or architectural change |
| Champion meeting attendance | Attend monthly security champion meeting |
| Security questions | First point of contact for security questions from team members (escalate to security team if outside expertise) |

### On-Demand Responsibilities

| Trigger | Champion Action |
|---------|----------------|
| New feature design with security implications | Facilitate threat model; involve security team if risk is significant |
| Security scanner finding in PR | Review finding; provide team context; make accept/reject recommendation |
| Potential security incident | Escalate immediately to security team; do not investigate alone |
| Security policy update | Communicate policy change to team; answer initial questions |
| New team member joins | Include security champion introduction in onboarding |

### What Champions Do Not Do

Clarifying the boundaries of the security champion role prevents burnout and scope creep:

- **Champions do not own security incidents.** The security team owns incident response; champions escalate and assist.
- **Champions are not security gatekeepers.** They provide input; final security decisions rest with the security team.
- **Champions do not audit their own team.** External review is required for formal compliance assessments.
- **Champions do not work unlimited hours on security.** The 10–20% time commitment is the limit; more than this indicates a team staffing problem, not a champion problem.

---

## Recognition and Incentives

Recognition is essential for a voluntary program. Without visible recognition, the program signals that the organization values the work but does not reward it.

**Recommended recognition mechanisms:**

| Mechanism | Implementation |
|-----------|---------------|
| **Public acknowledgment** | Name security champions in all-hands presentations; publish the champions list in the engineering handbook |
| **Performance review credit** | Security champion work counts toward "impact beyond your team" criteria in performance reviews — document this explicitly in the review rubric |
| **Conference attendance** | Security champions receive priority access to security conference attendance budget (AppSec, BSidesLV, DEF CON) |
| **Certification sponsorship** | Organization sponsors one security certification per year for active champions (GWEB, GSEC, eWPT) |
| **Champion-to-role pathway** | Security champion experience is a meaningful qualification for transitioning into a security engineering role — document this explicitly |

---

## Measurement Framework

Track program health using these metrics:

| Metric | Target | Collection Method |
|--------|--------|------------------|
| Champion coverage | 100% of engineering teams have an active champion | Team-to-champion mapping (monthly review) |
| Onboarding completion rate | 100% complete within 60 days of nomination | Training platform completion records |
| Monthly meeting attendance | ≥ 80% attendance across program | Meeting records |
| Threat models facilitated | ≥ 1 per champion per quarter | Threat model repository |
| Security PR review rate | ≥ 1 security-flagged PR per champion per month | PR label tracking |
| Champion-identified issues | Track count of security issues first identified by champions | Issue tracker label: "champion-identified" |
| Champion tenure | Average time champions stay in the role | Champion roster dates |
| Champion-to-security-role conversion | Count of champions who transitioned to security roles | Career tracking |

Review these metrics quarterly with program leadership. Champions who have not completed training, do not attend meetings, or are not active in security reviews should be contacted to understand if they need support or should transition out of the role.

---

## Program Launch Playbook

For organizations launching a security champion program for the first time:

**Month 1 — Foundation:**
- Secure executive sponsorship (CTO or CISO must visibly endorse the program)
- Define program structure, responsibilities, and recognition mechanisms
- Create champion selection process
- Develop onboarding curriculum (use this guide as a template)
- Identify a security team member as program coordinator

**Month 2 — Pilot:**
- Recruit 3–5 initial champions from volunteer-forward teams
- Run pilot cohort through onboarding curriculum
- Collect feedback on curriculum and process
- Refine before broader rollout

**Month 3–6 — Rollout:**
- Roll out to all engineering teams
- Run first monthly meeting
- Establish champion community channel (Slack, Teams)
- Begin measuring champion coverage metric

**Month 6+ — Sustain:**
- Annual curriculum refresh
- Introduce advanced topics (red team exercises, advanced threat modeling)
- Build champion-to-security-role pathway
- Publish program metrics quarterly

---

## Advanced Specialization Tracks

After completing the core onboarding curriculum and serving as a champion for 6+ months, experienced champions can pursue a specialization track. Specialization deepens expertise in a high-value domain and provides a clearer career signal for champions interested in moving toward security roles.

### Track 1: AppSec Specialist

**Focus:** Application-layer security, secure code review, and vulnerability research

**Additional curriculum (8 hours self-paced):**

| Module | Content |
|--------|---------|
| AST-1 | Advanced SAST rule authoring (Semgrep, CodeQL) — writing custom rules for your codebase's patterns |
| AST-2 | Vulnerability research basics — reading CVE advisories, assessing exploitability in your context |
| AST-3 | Advanced authentication and authorization — OAuth 2.0 flows, mTLS, JWT pitfalls |
| AST-4 | API security in depth — BOLA, mass assignment, graphQL security, API gateway configuration |

**Responsibilities added:**
- Lead security design review for all major new API surfaces
- Author team-specific SAST rules for recurring vulnerability classes
- Serve as point of contact for external penetration test coordination

### Track 2: Supply Chain and Pipeline Security Specialist

**Focus:** CI/CD security, artifact integrity, and dependency security

**Additional curriculum (8 hours self-paced):**

| Module | Content |
|--------|---------|
| SCS-1 | SLSA framework deep dive — levels 1-4, implementation guidance | [SLSA Advancement Guide](../../software-supply-chain-security-framework/docs/slsa-level-advancement.md) |
| SCS-2 | SBOM generation, management, and vulnerability correlation | [SBOM Guide](../../software-supply-chain-security-framework/docs/sbom-guide.md) |
| SCS-3 | Pipeline threat modeling — MITRE ATT&CK for CI/CD | [Secure CI/CD: Threat Model](../../secure-ci-cd-reference-architecture/docs/threat-model.md) |
| SCS-4 | Dependency vetting and open source component assessment | [OSS Component Assessment](../../software-supply-chain-security-framework/docs/open-source-component-assessment.md) |

**Responsibilities added:**
- Own team-level SBOM program and dependency review process
- Review pipeline configuration changes before they are merged
- Triage and escalate supply chain security advisories within 24 hours

### Track 3: Cloud and Infrastructure Security Specialist

**Focus:** Cloud posture management, Kubernetes security, and infrastructure as code

**Additional curriculum (8 hours self-paced):**

| Module | Content |
|--------|---------|
| CIS-1 | Cloud security fundamentals — IAM, network, workload security across AWS/Azure/GCP | [Cloud Security: Introduction](../../cloud-security-devsecops/docs/introduction.md) |
| CIS-2 | Kubernetes security hardening — CIS Benchmarks, Pod Security Standards, admission control | [Cloud Security: Framework](../../cloud-security-devsecops/docs/framework.md) |
| CIS-3 | Zero trust architecture in cloud-native environments | [Zero Trust Architecture Guide](../../cloud-security-devsecops/docs/zero-trust-architecture.md) |
| CIS-4 | IaC security — Checkov, tfsec, Terrascan rule review and exception management | [Compliance Automation: Framework](../../compliance-automation-framework/docs/framework.md) |

**Responsibilities added:**
- Review Terraform and Kubernetes manifest changes for security issues
- Own team-level cloud resource inventory and access review
- Monitor CSPM findings for team-owned cloud resources; coordinate remediation

---

## Distributed and Remote Team Considerations

Security champion programs face additional challenges in distributed organizations. The following adaptations maintain program effectiveness across time zones, remote teams, and large organizations.

### Asynchronous Program Delivery

| Challenge | Adaptation |
|-----------|-----------|
| Monthly meetings across 3+ time zones | Rotate meeting time to avoid one time zone bearing all inconvenience; record all meetings with transcripts |
| Live threat modeling sessions | Use asynchronous threat modeling tools (Miro, Confluence templates); run live sessions at sprint boundaries for high-risk features |
| CTF participation | Use asynchronous CTF platforms (PicoCTF, HackTheBox) that champions can complete over a week |
| Champion showcase presentations | Pre-record 5-minute video walkthroughs; discuss synchronously for 15 minutes |

### Scaling Beyond 20 Teams

For organizations with more than 20 engineering teams (typically 300+ engineers), a two-tier program structure reduces security team coordination overhead:

```
Security Team (Central)
        │
        │  Manages, trains, and supports
        │
        ▼
Senior Champions (1 per business unit or domain)
        │
        │  Coordinate, mentor, and escalate to
        │
        ▼
Champions (1 per team)
```

**Senior Champion role:**
- Experienced champion with 12+ months in the program
- Serves as first escalation point for junior champions in their domain
- Leads domain-specific security initiatives (e.g., all backend teams align on authentication patterns)
- Represents their domain at program steering meetings

### Champion Program at the Enterprise Scale

For enterprises with 50+ engineering teams:
- Dedicate a 0.5 FTE program coordinator within the security team to administer the champion program
- Create a self-service onboarding portal (LMS integration) so security team does not manually onboard each champion
- Establish a champion community forum (asynchronous) as the primary knowledge sharing channel
- Run quarterly in-person or virtual champion summits in addition to monthly meetings
- Create tiered champion badges (Associate, Champion, Senior Champion) tied to specific competencies

---

## Integration with Maturity Model

The security champion program directly supports multiple domains of the [DevSecOps Maturity Assessment](../../devsecops-maturity-model/docs/assessment-scorecard.md). Champions can accelerate maturity advancement across several domains simultaneously:

| Scorecard Practice | Champion Program Contribution |
|--------------------|------------------------------|
| 1.2: Security champions program exists with active participation | This program document defines the complete structure |
| 1.3: Security requirements discussed at project inception | Champions facilitate threat modeling at design phase |
| 1.5: Security training provided at least annually | Champion curriculum + annual refresh |
| 3.1: Threat modeling performed for significant features | Champions own this in their teams |
| 1.6: Engineering OKRs include security objectives | Champion time is formally recognized in performance reviews |
| 3.3: Security-focused code review performed regularly | Champions conduct monthly PR security reviews |
| 4.3: Security findings triaged promptly | Champions triage SCA and SAST findings within their teams |
| 2.2: Abuse cases documented for high-risk features | Champions trained in threat modeling and abuse case development |

For a complete map of how assessment scores translate to specific implementation actions (including the champion program's role in each domain), see the [Framework Navigation Guide](../../devsecops-maturity-model/docs/framework-navigation-guide.md).

---

## Related Documents

- [Anti-Patterns Reference Library](anti-patterns.md) — Common champion program failure modes (AP-O2: Separate DevSecOps Team; AP-C1: Security Champions Without Authority)
- [Resistance Management](resistance-management.md) — Handling resistance from managers and developers to champion program adoption
- [DevSecOps Maturity Model: Framework Navigation Guide](../../devsecops-maturity-model/docs/framework-navigation-guide.md) — How champion program maturity translates to assessment scores and advancement actions
- [DevSecOps Framework: Developer Experience](../../devsecops-framework/docs/developer-experience.md) — Tools and practices champions use to improve security DX for their teams

---

*Part of the Techstream DevSecOps Methodology. Licensed under Apache 2.0.*
