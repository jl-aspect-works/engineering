# GitHub Governance and Security Verification

Verification date: 2026-09-30 (America/New_York). Scope: Engineering issues #16–18 following the post-v2.3.3 hardening audit. GitHub is authoritative. API read-back evidence and owner-confirmed administrator settings are distinguished below.

## Verified repository state

| Repository | Active main ruleset | Required checks | Release tags |
| --- | --- | --- | --- |
| `jl-mixing-studio` | 19107489 | `Required CI` | Active immutable `v*` ruleset 24244082 |
| `jl-mixing-automation` | 24118548 | `Required tests`, `Required ShellCheck` | Active immutable `v*` ruleset 24244138 |
| `engineering` | 24118688 | `Required repository checks` | No product releases |
| `jl-brand` | 24281087 | `Required repository checks` | No product releases |
| organization `.github` | 24280997 | `Required repository checks` | No product releases |

The main rulesets require PR-based changes and resolved review conversations, dismiss stale approvals, block deletion/non-fast-forward updates, enforce strict up-to-date branches and required checks from GitHub Actions integration 15368, and have no bypass actors. Engineering's newly required check and the final Brand/organization settings were independently read back through the GitHub API. Product protections were verified during the audit. Product tag rulesets block update/deletion/non-fast-forward with no bypass actors.

The lightweight repository check runs on every main PR and push. It validates PNG structure/chunk checksums, SVG syntax, local Markdown links and immutable Action references. Negative fixtures cover corrupt/truncated PNGs, missing link targets and mutable Action pins. It does not validate visual design or perform a complete decoder security audit.

Studio has JavaScript/TypeScript and Rust CodeQL; Automation has Python CodeQL. Automation's full main baseline and zero-finding review are recorded in Engineering #15. CodeQL completion and findings review remain obligations under the standing development agreement.

## PR approval policy

Retain zero mandatory GitHub approving reviews until a second trusted eligible reviewer is available. Preserve PR-only changes, CI, review-thread resolution, no bypasses and the explicit user/maintainer approval required by the development agreement. Chat approval is not a GitHub review; authors cannot self-approve to satisfy a one-review gate. Revisit one required review when the workflow can sustain it.

## Administrator security verification

The owner confirmed manual checklist items 1–5 completed on 2026-09-30. These security settings were manually verified by the owner, not independently queried through the App, whose administrator endpoints are unavailable.

| Control | Evidence / disposition |
| --- | --- |
| Organization 2FA enforcement | Owner confirmed verification/completion |
| Secret scanning and repository push protection | Owner confirmed available controls verified/enabled across the five audited repositories |
| Dependency graph and Dependabot alerts | Owner confirmed verification/completion; weekly version-update YAML separately verified for Studio Actions/npm/Cargo and Automation Actions/pip |
| Private vulnerability reporting | Owner confirmed verification/completion across the audited repositories; shared policy presence alone is not evidence of enablement |
| Organization security defaults for new repositories | Owner reported unavailable on current plan; documented plan limitation, no paid upgrade required |
| Signing/notarization | Explicitly deferred unless separately reprioritized |

Apply the same available baseline manually when onboarding new repositories. Record unavailable controls separately and do not implicitly purchase paid protection or replace the product CodeQL workflows.

## Release defense in depth

Both published `v2.3.3` releases reported `immutable: false` during the audit. Protected tags and the #14 create-only release guards are verified. Native server-side release immutability remains a separate review, outside completion of #16–18. No native locking change or retroactive protection is claimed. Do not replace artifacts or move tags to retrofit protection; corrections require a new version/tag.

## Completion and implementation references

- #16 complete: Brand and organization main rulesets applied and final strict-check/stale-review settings independently verified.
- #17 complete: shared security/contribution policies, issue/PR templates and accurate README/profile are merged.
- #18 complete: governance review, owner-confirmed security verification and current-plan exception recorded. Signing/notarization remains deferred.

Implementation PRs:
- [Engineering #19](https://github.com/jl-aspect-works/engineering/pull/19) and [#20](https://github.com/jl-aspect-works/engineering/pull/20): governance evidence, checks and administrator checklist.
- [Brand #2](https://github.com/jl-aspect-works/jl-brand/pull/2): checks and accurate asset inventory.
- [Organization .github #4](https://github.com/jl-aspect-works/.github/pull/4): shared policies/templates, profile corrections and checks.

All implementation PRs and their main pushes passed CI. Temporary implementation patches were removed after target merges. The [lightweight ruleset JSON](security/rulesets/lightweight-main.json) remains a reusable configuration template, not a substitute for reading active settings. Inspect existing protections before applying it to a new repository; do not create duplicate rulesets or overwrite changed settings blindly.
