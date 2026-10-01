# GitHub Governance and Security Verification

Verification date: 2026-09-30 (America/New_York). Scope: Engineering issues #16–18, following the post-v2.3.3 hardening audit. GitHub is authoritative; this record distinguishes observed settings from proposed configuration and settings that could not be verified.

## Verified repository state

| Repository | Main branch | Required checks currently enforced | Release tags |
| --- | --- | --- | --- |
| `jl-mixing-studio` | Active ruleset 19107489; PRs required; deletion/non-fast-forward blocked; no bypass actors | `Required CI`, strict; GitHub Actions integration 15368 | Active ruleset 24244082; `refs/tags/v*` creation permitted, update/deletion/non-fast-forward blocked; no bypass actors |
| `jl-mixing-automation` | Active ruleset 24118548; PRs required; deletion/non-fast-forward blocked; no bypass actors | `Required tests` and `Required ShellCheck`, strict; integration 15368 | Active ruleset 24244138; same immutable `v*` restrictions and no bypass actors |
| `engineering` | Active ruleset 24118688; PRs required; deletion/non-fast-forward blocked; no bypass actors | No required check contexts in the current ruleset; new repository checks proposed in this remediation | No release tag ruleset needed for this documentation repository |
| `jl-brand` | No rulesets returned by GitHub at verification | New `Required repository checks` workflow proposed | No product releases |
| organization `.github` | No rulesets returned by GitHub at verification | New `Required repository checks` workflow proposed | No product releases |

The new lightweight check validates PNG chunk checksums/structure, SVG syntax, local Markdown link targets, and immutable Action references. Negative fixtures exercise corrupt/truncated PNGs, missing local targets, and mutable Action references. It runs on every PR targeting main and every main push, without documentation-only path skips. Image checks validate structure, not visual design or a complete image-decoder security audit.

Studio and Automation have Python/JavaScript-TypeScript/Rust CodeQL coverage as appropriate, with the verified Automation full main Python baseline recorded in Engineering #15. Their existing required checks remain unchanged by this review. CodeQL completion and findings review are separate verification obligations under the standing agreement.

## PR approval policy

Retain the current zero mandatory GitHub approving-review count while preserving PR-only changes, CI, resolution of review threads, and the applicable explicit user/maintainer approval in the development agreement. Chat approval is not a GitHub review. Requiring one GitHub approval would require a separate eligible reviewer identity; an author cannot satisfy that gate by self-approval. Revisit a minimum of one approving review when a second trusted reviewer is available and the workflow can sustain it. Do not weaken no-bypass rules to accommodate an unavailable reviewer.

## Ready-to-apply lightweight ruleset

[lightweight-main.json](security/rulesets/lightweight-main.json) defines active main protection with no bypass actors, blocked deletion/non-fast-forward updates, PR-required changes, strict `Required repository checks` from integration 15368, and zero mandatory GitHub reviews.

Apply to `jl-brand` and organization `.github` only after their workflow PRs are merged and the named check has passed on main. First confirm no ruleset has been created in the meantime:

```sh
gh api repos/jl-aspect-works/jl-brand/rulesets
gh api repos/jl-aspect-works/.github/rulesets
```

When those responses still show no existing ruleset, an authenticated administrator can create the rulesets from an Engineering checkout:

```sh
gh api --method POST repos/jl-aspect-works/jl-brand/rulesets --input docs/security/rulesets/lightweight-main.json
gh api --method POST repos/jl-aspect-works/.github/rulesets --input docs/security/rulesets/lightweight-main.json
```

Engineering already has a ruleset. After its new checks pass on main, add `Required repository checks` to ruleset 24118688 without replacing its other restrictions. Do not blindly POST duplicate rulesets or overwrite a changed ruleset. Re-read each resulting ruleset and verify active enforcement, default-branch target, strict checks, integration 15368, no bypasses, blocked deletion/non-fast-forward, and required PRs. Also review any additional rulesets and legacy branch protections for overlapping effects.

**Pending administrator action:** the current connection can read public rulesets and change repository files/PRs, but exposes no settings-write operations. The workspace GitHub CLI is installed but unauthenticated. These configuration files are proposals, not evidence of active protection. The organization GitHub App installation 147188566 selects only Studio, Automation, and Engineering; writes to `jl-brand` and organization `.github` were rejected with HTTP 403, `Resource not accessible by integration`. Add those two repositories to the existing installation before attempting their PRs again.

## Security/account settings still requiring verification

| Setting | Current evidence / state | Administrator verification and action |
| --- | --- | --- |
| Organization 2FA requirement | Unverified; organization settings are not exposed by the current connection | Inspect organization authentication-security settings and membership consequences before changing enforcement. Record whether 2FA is required. |
| Secret scanning and push protection | Unverified; repository metadata did not expose `security_and_analysis`. Omission is not evidence of enabled or disabled status. | Inspect Code security settings for all five repositories. Enable available scanning/protection where needed; record unsupported features or plan constraints separately. Never place found secrets in the audit record. |
| Dependabot alerts / dependency graph | Weekly version-update configuration verified for Studio Actions/npm/Cargo and Automation Actions/pip. Alert and dependency-graph settings remain unverified. | Confirm dependency graph and Dependabot alerts for applicable repositories; empty asset/config repositories may have no dependency graph to populate. Version-update YAML alone does not prove alerts are enabled. |
| Private vulnerability reporting | Product/Engineering policies are present; the shared fallback policy is proposed in #17. The reporting setting remains unverified. | Confirm the private reporting setting independently for every repository. Policy file presence does not enable it. |
| Native release immutability | Both current `v2.3.3` releases report `immutable: false`. Protected tags and #14 create-only release workflows are verified; server-side immutability configuration is unverified. | Review the immutable-releases setting and its effect on future publication. Do not republish or replace existing artifacts/tags to retrofit protection. Record any native locking decision and preserve the workflow's draft upload-before-publication lifecycle. |
| Signing / notarization | Explicitly deferred | Remains deferred unless separately reprioritized; this remediation does not add signing credentials or change release qualification. |

Use an authenticated administrator session to verify settings. Record observed values, repository/organization scope, date, and approved changes; keep unknowns open. The inability to verify a setting is not a pass, a failure, or evidence that GitHub disabled it.

## Completion state

- #16: check workflows and a concrete ruleset prepared; requires administrator application and read-back verification before closure.
- #17: community defaults and accurate README/profile supplied by the organization repository remediation; exact merge and CI verification tracked in its PR/issue.
- #18: accessible governance evidence reviewed and recorded; administrator settings above remain unverified and prevent full closure.

PR/issue records carry exact merge commits and CI results. Do not describe #16 or #18 as complete until their administrator-dependent work is verified.

## Prepared implementation checkpoints

The following reviewed local implementations are retained here until GitHub App access to their target repositories is available:

- [jl-brand patch](security/prepared/jl-brand.patch), based on `538446cdcea388fe06c85722504065e12549fba4`
- [Organization .github patch](security/prepared/org-github.patch), based on `8031318900c2c25f376d538b8105dbc8a2b23fe0`

Both add always-present repository checks with negative fixtures; the organization patch also adds default security/contribution guidance, bug/feature/PR templates, and corrected README/profile content. Local checkers, negative tests, workflow/template YAML parsing, and whitespace checks passed. No target PR was created because integration writes were rejected. Apply only on focused branches after reviewing the target's current main; if it has moved, reconcile changes rather than assuming these bases remain current. GitHub CI must pass before merge. User pre-approval of #16–18 and their associated PRs is recorded in the issue discussions. These patch files are implementation checkpoints, not active configuration or a second long-term source of truth. Remove them after the target PRs land, retaining the PR references.
