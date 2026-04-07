# Legacy Application DevSecOps Integration

Adopting DevSecOps is straightforward for greenfield applications built with modern toolchains. Integrating legacy applications — applications with multi-year codebases, minimal test coverage, monolithic architectures, long manual release cycles, and accumulated technical debt — requires a structured approach that manages risk without destabilizing production systems.

This guide provides a phased integration strategy for legacy applications, decision frameworks for common blockers, and a minimum viable security posture that can be reached without a full application rewrite.

---

## Defining Legacy in This Context

For the purpose of this guide, a legacy application is one that exhibits three or more of the following characteristics:

- Release cycle longer than two weeks (typically monthly or quarterly)
- Test coverage below 30% (or unknown)
- Build process is manual, scripted without version control, or dependent on developer workstations
- Dependencies managed without a lockfile or with pinned versions that have not been reviewed in more than 12 months
- Deployment requires manual SSH access, FTP, or a custom deployment script without rollback capability
- No SAST, SCA, or secrets scanning in the delivery pipeline
- Deployed on infrastructure that is not managed as code

An application does not need to use old technology to be a legacy application by this definition. A five-year-old Node.js service with manual deployments and no tests qualifies.

---

## Why Legacy Integration Fails

Common failure modes when integrating legacy applications into DevSecOps programs:

| Failure Mode | Cause | Mitigation |
|---|---|---|
| Scanner alert fatigue | Running SAST/SCA on a legacy codebase generates hundreds of findings; teams disable scanners | Triage before enabling — define what will block vs. what will be tracked |
| Test suite introduction breaks CI | Adding automated tests to code with no tests fails immediately | Start with smoke tests and golden-path integration tests, not unit tests |
| Secret scanning detects years of embedded secrets | Pre-existing secrets in history trigger scanner; team cannot remediate all at once | Use `--no-history` mode first; establish a remediation backlog |
| Deployment pipeline introduction causes instability | Automated deployments reveal undocumented dependencies and configuration drift | Shadow-run the automated pipeline alongside manual process before cutting over |
| Resistance from operations team | Legacy systems are often "owned" by ops; change feels threatening | Include ops in design; treat them as co-authors of the new pipeline |

---

## Integration Phases

### Phase 0: Baseline Assessment (1–2 weeks)

Before making any changes, establish the current state. Do not skip this phase — changes made without a baseline cannot be evaluated for regression.

**Assessment checklist:**

| Area | Questions to Answer |
|------|---------------------|
| Codebase | What language(s) and runtimes? What package manager? Is there a lockfile? |
| Build | How is the application built today? What machine/environment? What dependencies are installed at build time? |
| Test coverage | What tests exist? What percentage of code is covered? |
| Secrets | Are any credentials, API keys, or certificates hardcoded or stored in configuration files? |
| Dependencies | What are the direct dependencies? What is the age distribution of versions? Any known CVEs? |
| Deployment | How is the application deployed? Who does it? How long does it take? How is rollback performed? |
| Change control | What is the current release process? How long does it take from code change to production? |
| Infrastructure | How is infrastructure provisioned? Is it documented? Is it reproducible? |
| Logging | What logs does the application produce? Where do they go? Is there structured logging? |

Run the [DevSecOps Maturity Assessment Scorecard](../../devsecops-maturity-model/docs/assessment-scorecard.md) against the application at this point. The score establishes your baseline and informs which phase targets are realistic.

---

### Phase 1: Observe Without Blocking (Weeks 1–4)

Introduce security tooling in observation mode. Do not block the delivery pipeline on findings yet. The goal is to understand the full scope of technical debt before establishing gates.

**Step 1.1 — Source Control Hygiene**

If the application code is not yet in a version control system (or is in an unmaintained VCS), migrate it to Git with full history. This is non-negotiable — all subsequent steps require Git.

- Create a repository with a branch protection policy
- Establish a contribution workflow: no direct commits to main
- Document the repository in a central application inventory

**Step 1.2 — Introduce Secret Scanning (Non-Blocking)**

```bash
# Run Gitleaks in observe mode on current HEAD only (no history scan initially)
gitleaks detect --source . --no-git --report-format json --report-path secrets-report.json
# Review results before enabling history scan
```

Review findings with the application team. Create a remediation backlog for each confirmed secret:
- Rotate the credential immediately if it is still active
- Remove from code (requires commit or, for history-embedded secrets, a git history rewrite plan)
- Document accepted risk for any finding that cannot be immediately remediated, with an owner and target date

**Step 1.3 — Introduce SAST (Non-Blocking)**

Run SAST against the current codebase and capture the baseline finding count by severity. This baseline is the starting point — every future sprint should trend it downward:

```bash
# Example: Semgrep with auto ruleset on a Python legacy app
semgrep scan --config auto --json --output sast-baseline.json .
python -c "import json; r=json.load(open('sast-baseline.json')); print(f'CRITICAL: {sum(1 for f in r[\"results\"] if f[\"extra\"][\"severity\"]==\"ERROR\")}')"
```

Do not attempt to fix all findings immediately. Categorize:
- **New findings gate:** All findings introduced by new code changes will block merge after Phase 2
- **Existing debt queue:** Existing findings are tracked, prioritized by severity and exploitability, and addressed in a dedicated remediation sprint

**Step 1.4 — Introduce SCA (Non-Blocking)**

Run a dependency scan and capture the vulnerability inventory:

```bash
# Trivy for filesystem scan (language-agnostic)
trivy fs . --format json --output sca-baseline.json

# Generate first SBOM for the application
syft dir:. -o cyclonedx-json=sbom-baseline.cdx.json
```

Triage the findings:
- Critical/High CVEs: add to a remediation sprint within 30 days
- Medium CVEs: add to backlog with 90-day target
- Low CVEs: tracked; addressed opportunistically when upgrading for other reasons

**Step 1.5 — Formalize the Build Process**

If the build is manual or machine-specific, codify it in a repeatable build script:

```bash
# Create a reproducible build script — example for a Java Maven app
#!/bin/bash
set -euo pipefail

echo "Building application version $APP_VERSION"
mvn clean package \
  -Dmaven.test.skip=false \
  -Dmaven.repo.local=.m2/repository \
  --no-transfer-progress

echo "Build complete: target/app-${APP_VERSION}.jar"
```

Commit this script to the repository. Run it in a Docker container to ensure portability:

```dockerfile
FROM maven:3.9.6-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:resolve --no-transfer-progress
COPY src ./src
RUN mvn clean package -Dmaven.test.skip=false --no-transfer-progress

FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/app.jar .
USER 1001
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### Phase 2: Introduce Blocking Gates on New Code (Weeks 5–12)

In Phase 2, security gates are enabled for new code changes only. Existing findings do not block — they continue to be addressed via the remediation backlog. This approach prevents alert fatigue and keeps delivery velocity intact.

**Gate 1: Secrets detection (blocking on new commits)**

Configure Gitleaks as a pre-commit hook and as a CI check on every PR. Findings in commits that are part of the current PR (not in history) block merge:

```yaml
# .github/workflows/security.yml — secrets gate
- name: Secret Scanning (new commits only)
  run: |
    gitleaks detect \
      --source . \
      --log-opts "origin/main..HEAD" \
      --exit-code 1
```

**Gate 2: SAST on new code (blocking on regressions)**

Block merges if the PR introduces NEW findings of Critical or High severity. Do not block on findings that existed before the PR:

```yaml
- name: SAST Delta Analysis
  run: |
    semgrep scan --config auto --json --output pr-scan.json .
    semgrep scan --config auto --json --output main-scan.json --baseline-commit origin/main .
    # Count new findings introduced by this PR
    python scripts/sast_delta.py main-scan.json pr-scan.json --fail-on-new-critical
```

**Gate 3: SCA blocking on new Critical CVEs**

Block merges if the PR adds a new dependency with a Critical CVE:

```yaml
- name: Dependency Check (new dependencies)
  run: |
    trivy fs . --exit-code 1 --severity CRITICAL --ignore-unfixed \
      --skip-dirs vendor --skip-files "*.lock"
```

**Gate 4: Build reproducibility verification**

All builds must run from the committed build script. No ad-hoc builds. CI now owns the build:

```yaml
- name: Build
  run: docker build -t app:${{ github.sha }} .
```

---

### Phase 3: Harden the Pipeline (Months 3–6)

With gates in place for new code, address systemic weaknesses in the delivery process.

**3.1 — Migrate Credentials to a Secrets Manager**

For each hardcoded credential identified in Phase 1:

1. Provision a secret in HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault
2. Update the application to retrieve the secret at startup using the environment variable pattern or a secrets SDK
3. Remove the hardcoded value from code and configuration files
4. Rotate the original credential

Track migration progress:

| Credential | Current Location | Target | Owner | Target Date | Status |
|------------|-----------------|--------|-------|-------------|--------|
| Database password | `config/database.yml` | Vault: `secret/payments/db` | Payments team | 2026-05-01 | In progress |
| Third-party API key | `src/integrations/stripe.py` | AWS Secrets Manager | Platform team | 2026-04-15 | Done |

**3.2 — Introduce a Staging Environment**

If no staging environment exists, establish one before Phase 4. The staging environment must:

- Be provisioned identically to production (use the same IaC templates)
- Use separate credentials from production
- Be the mandatory deployment target before any production release
- Run integration and smoke tests on every deployment

**3.3 — Test Coverage Expansion**

Target: reach 40% test coverage by the end of Phase 3. Focus on:

1. **Smoke tests:** Does the application start? Do the critical paths return expected responses?
2. **Integration tests:** Do the most important user journeys work end-to-end against a real database?
3. **Regression tests for known bugs:** For every bug fixed, add a test that would have caught it.

Do not try to reach high unit test coverage immediately — this requires significant refactoring of legacy code and is a Phase 4+ goal.

**3.4 — Formalize the Release Process**

Document the release process as code:

```yaml
# release.yml — release process checklist (automated where possible)
release:
  pre-flight:
    - run: make test           # All tests pass
    - run: make lint           # No new linting errors
    - check: security-gates    # All security gates pass in CI
    - manual: staging-smoke    # QA team verifies staging is healthy
  deployment:
    - run: make deploy-staging  # Deploy to staging
    - wait: 15m                 # Soak period
    - run: make smoke-test      # Automated smoke tests against staging
    - manual: production-approval  # Release manager signs off
    - run: make deploy-production
  post-deployment:
    - run: make smoke-test-prod   # Smoke tests against production
    - run: make notify-channels   # Notify Slack #releases
```

---

### Phase 4: Continuous Improvement (Month 6+)

**Target maturity state (Maturity Level 3):**
- All security scanners running in blocking mode
- Test coverage ≥ 60%
- Deployment pipeline fully automated, including staging and production
- Credentials managed entirely through a secrets manager
- SBOM generated for every release
- Release cycle < 2 weeks

**Ongoing improvement areas:**

| Area | Target | Method |
|------|--------|--------|
| Dependency freshness | All dependencies within 2 major versions | Dependabot or Renovate automated PRs |
| SAST findings | Zero Critical open > 30 days | Weekly remediation sprint |
| Test coverage | Increasing trend toward 70%+ | Add tests with each bug fix or feature |
| Infrastructure | 100% IaC | Terraform/Pulumi for all environments |
| Container adoption | Application runs in a container | Containerize as part of Phase 4 |

---

## Common Blockers and Solutions

### "We cannot run the application in CI — it depends on X"

Legacy applications often depend on:
- A specific version of a runtime that is difficult to install in CI
- External services (databases, mainframes, proprietary APIs)
- Manual configuration performed by an operations team

**Solutions:**
- Use Docker to package the exact runtime: `FROM legacy-runtime:4.2.1`
- Use service containers or TestContainers for database dependencies in tests
- Use contract tests or mocks for external proprietary services — test the integration separately
- Pair the pipeline introduction with the infrastructure-as-code initiative

### "We have thousands of existing SAST findings and can't address them all"

Do not try to fix all findings before enabling the pipeline. Use a two-track approach:

1. **Ignore list:** Document all existing findings in a `semgrep.ignore` or equivalent file, with a comment explaining the status. This allows the scanner to run cleanly and detect new issues.
2. **Remediation queue:** Track existing findings in your issue tracker. Assign a severity-based SLA (Critical: 30 days; High: 90 days; Medium: 180 days) and address them in dedicated remediation sprints.

### "The operations team won't accept automated deployment"

This is a change management challenge, not a technical one. Common underlying concerns:
- Fear of losing control over a system they are accountable for
- Previous incidents caused by automation failures
- Unclear accountability model in the new process

Approach:
1. Keep the operations team as approvers in the deployment pipeline — their approval is required; the automation handles the mechanics
2. Run the automated pipeline in shadow mode alongside manual deployments for 2–4 weeks, demonstrating that it produces identical results
3. Start with staging automation only; keep production manual until confidence is established

---

## Integration Checklist

Use this checklist to track progress through the legacy integration journey:

**Phase 1 (Observe)**
- [ ] Application code is in Git with branch protection
- [ ] Secret scanning baseline established; remediation backlog created
- [ ] SAST baseline scan completed; findings inventoried
- [ ] SCA baseline scan completed; SBOM generated
- [ ] Build process codified as a repeatable script/Dockerfile
- [ ] Maturity assessment scorecard baseline completed

**Phase 2 (Gate New Code)**
- [ ] Secret scanning gate active on all PRs
- [ ] SAST delta gate active (new Critical/High findings block merge)
- [ ] SCA gate active on new dependencies
- [ ] All builds run from CI pipeline (no manual builds)

**Phase 3 (Harden Pipeline)**
- [ ] All credentials migrated to secrets manager
- [ ] Staging environment exists and deployment is automated
- [ ] Test coverage ≥ 40%
- [ ] SBOM generated for every release
- [ ] Release process documented as code

**Phase 4 (Continuous Improvement)**
- [ ] Full SAST blocking mode enabled
- [ ] Test coverage ≥ 60% and improving
- [ ] Release cycle ≤ 2 weeks
- [ ] Maturity assessment score at Level 3 or above

---

## Related Documentation

- [DevSecOps Maturity Assessment Scorecard](../../devsecops-maturity-model/docs/assessment-scorecard.md) — use for baseline and progress measurement
- [Metrics and KPIs Guide](../../devsecops-maturity-model/docs/metrics-kpis.md) — instrument progress metrics from Phase 1
- [Pipeline Security Hardening Checklist](../../secure-pipeline-templates/docs/hardening-checklist.md) — apply when the pipeline reaches Phase 2+
- [DevSecOps Transformation Anti-Patterns](anti-patterns.md) — common pitfalls to avoid during legacy integration
- [Software Supply Chain Security Framework](../../software-supply-chain-security-framework/docs/framework.md) — SBOM and supply chain controls for Phase 3+

---

*Part of the Techstream DevSecOps Methodology. Licensed under Apache 2.0.*
