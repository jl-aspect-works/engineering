# JL Audio Standing Development Agreement

This document defines the standing development workflow for JL Audio software projects.

It applies across JL Audio repositories unless a repository contains an explicitly documented project-specific exception. Technical implementation should also follow the [JL Audio Coding Standards](CODING_STANDARDS.md).

The purpose of this agreement is to make development predictable, auditable, efficient, and easy to resume across development sessions.

## 1. Source of truth

GitHub is the source of truth for development state.

Use repository content, issues, pull requests, CI results, release artifacts, and versioned documentation as authoritative rather than relying on conversation history.

Important decisions that affect future work should be recorded in the appropriate repository rather than existing only in a development conversation.

Where applicable:

- issues define planned work and accepted scope;
- pull requests define proposed implementation;
- repository documentation defines durable requirements, architecture, and process;
- release/acceptance documentation records verification results;
- GitHub Actions is the authoritative source for CI status.

Conversation history is working context, not the permanent project record.

## 2. Branch and pull-request workflow

Do not make development changes directly to `main`.

Normal development flow is:

1. Start from the current `main` branch.
2. Create a focused feature, fix, documentation, or release branch.
3. Make the approved changes on that branch.
4. Run appropriate local validation where available.
5. Push the branch.
6. Open a pull request against `main`.
7. Allow CI to complete.
8. Review and resolve failures or review comments.
9. Obtain user approval when required by the high-level issue workflow in Section 5.
10. Merge through the repository's normal merge process.

Keep pull requests reasonably focused. Avoid combining unrelated cleanup or refactoring with feature work unless explicitly approved.

## 3. Scope and decision control

Before implementing a material feature or design change, establish enough scope to avoid unnecessary implementation/rework cycles.

Once the user explicitly approves or says to **lock in** a decision:

- treat that decision as the current requirement;
- do not silently revisit it;
- do not substitute a different design merely because another approach appears preferable;
- raise the decision again only when new evidence creates a meaningful technical, usability, compatibility, cost, or safety concern.

When new information conflicts with a locked decision, explain the conflict and recommend a resolution before changing the approved behavior.

Small implementation details that do not materially affect approved behavior may be resolved during implementation without unnecessary approval cycles.

## 4. Issues and implementation tracking

Material work should normally be represented by a GitHub issue.

An issue should contain enough information for future development sessions to understand:

- the problem or opportunity;
- expected behavior;
- important UI or workflow requirements;
- acceptance criteria;
- dependencies or related issues;
- known constraints;
- explicitly deferred behavior where relevant.

If detailed design belongs in a separate document, keep a concise authoritative summary in the issue and link to the design document.

Do not close issues solely because code was written. Close or mark work complete when the agreed implementation and required verification are complete.

## 5. Development sequencing

Development should normally proceed **one high-level issue at a time**.

A high-level issue represents a meaningful feature, defect, enhancement, release task, or other independently verifiable unit of work. Subtasks may be created or handled as necessary to complete that issue, but they do not normally become separate user approval gates.

The normal development sequence is:

1. Select the next high-level issue.
2. Review its requirements, dependencies, affected code, and existing tests.
3. Resolve any material design decisions.
4. Implement the issue completely.
5. Run appropriate automated validation and CI.
6. Perform required user verification or acceptance testing.
7. Obtain user approval when applicable.
8. Complete/merge the issue.
9. Move to the next high-level issue.

Do not routinely work ahead on multiple unrelated high-level issues while the current issue is still awaiting implementation, verification, or approval.

Exceptions are appropriate when:

- another issue is required to unblock the current issue;
- work is intentionally grouped because the issues are technically inseparable;
- an unrelated small fix is necessary to restore CI or development tooling;
- the user explicitly approves parallel or reordered work.

### Design decisions requiring user input

Ask the user for input when a decision has meaningful:

- end-user-facing behavior or UX impact;
- architectural impact;
- public API, schema, CLI, or compatibility impact;
- workflow or product-policy impact;
- scope or release-impact implications;
- material tradeoffs where several reasonable approaches would produce meaningfully different results.

When practical, present a recommended option and explain the important tradeoffs rather than asking an open-ended technical question.

### Implementation decisions that do not require user approval

Minor internal implementation decisions do **not** require separate user approval when they preserve the approved behavior and architecture.

Examples include:

- localized bug fixes;
- straightforward refactoring required to implement the issue cleanly;
- naming or organization of internal helpers;
- minor test adjustments or additional regression coverage;
- lint, formatting, compiler-warning, or type-check fixes;
- internal error handling improvements that do not change the intended user experience;
- small defensive fixes discovered while working within the current issue;
- other implementation details that do not materially affect architecture, compatibility, scope, or end-user behavior.

These changes should still follow the coding standards and remain within the reasonable scope of the current issue.

If a seemingly minor fix begins to alter product behavior, architecture, compatibility, or issue scope, stop treating it as an internal implementation detail and raise the decision with the user.

### Issue completion and approval gate

The normal expectation is that a high-level issue is **implemented, tested, and approved before development proceeds to the next high-level issue**.

For issues requiring user acceptance:

1. complete implementation;
2. obtain passing automated validation/CI;
3. provide the build or behavior for user testing;
4. record any defects found during verification against the current issue;
5. correct and retest those defects;
6. obtain user approval;
7. complete/merge the issue;
8. proceed to the next high-level issue.

This keeps development focused and prevents a backlog of partially completed or unverified changes from accumulating.

User approval is not required for every internal code edit. Approval applies to the completed high-level issue or to material design decisions identified during its implementation.

Where an issue can be completely verified through automated testing and does not require meaningful user acceptance, it may be completed without a separate manual test cycle when that is consistent with the project's established verification policy.

## 6. Testing and CI

Automated verification is part of implementation, not a separate afterthought.

Follow the JL Audio Coding Standards for test quality and project-specific validation requirements.

Before a pull request is considered ready:

- run relevant local checks when practical;
- add or update regression tests for reproducible defects;
- verify important success and failure paths;
- avoid changing tests simply to make an incorrect implementation pass;
- investigate warnings or failures rather than assuming they are unrelated.

### CI completion rule

When development work triggers CI, do not treat the work as complete merely because the build was started.

Poll/check CI until the relevant run reaches a meaningful terminal state or until further progress requires user action.

If CI fails:

1. identify the actual failing job or test;
2. inspect the relevant logs;
3. determine root cause before applying speculative fixes;
4. correct related failures together where practical;
5. rerun the minimum appropriate validation;
6. continue checking until the result is known.

Avoid repeated blind build/fix cycles.

If an external or transient failure is strongly indicated, distinguish it from a code failure and rerun only the necessary jobs when possible.

## 7. User verification and acceptance

Automated tests do not replace user acceptance for behavior that requires real application, platform, installer, audio, workflow, or visual verification.

Maintain an explicit verification record for release-critical testing.

For cross-platform products, track platform results independently unless the user explicitly directs that equivalent results may be copied between platforms.

Valid states should be clear, for example:

- Pass
- Fail
- Not Run
- Deferred
- Blocked

Do not report an unexecuted test as passed.

When a test is intentionally deferred, preserve that decision in the release/acceptance record.

## 8. Release discipline

Release creation should be repeatable and should minimize release-only code changes.

Prefer a single authoritative version source, such as a repository `VERSION` file, when supported by the project architecture.

Tests should generally validate version consistency rather than contain unnecessary hard-coded release numbers.

Manual release steps that cannot reasonably be automated should be documented in a release checklist.

Before publishing a stable release:

- release scope is locked;
- required implementation PRs are merged;
- required CI is passing;
- acceptance/verification requirements are satisfied;
- deferred tests or known issues are explicitly recorded;
- release documentation reflects the final stable state rather than candidate language;
- release artifacts are generated from the approved commit/tag;
- artifact presence and basic validity are verified.

Do not change application behavior merely to produce a release unless a release-blocking defect is discovered.

## 9. Documentation responsibilities

Durable information belongs in version-controlled documentation.

Use the appropriate level:

- **Engineering repository** — organization-wide engineering standards, shared architecture decisions, process, compatibility policy, and cross-project conventions.
- **Product repository** — product-specific architecture, behavior, development status, acceptance results, release notes, and implementation details.
- **GitHub issue** — scoped feature/defect requirements and acceptance criteria.
- **Pull request** — implementation-specific explanation and review history.
- **Development conversation** — active working context and decision discussion.

Avoid maintaining multiple independent copies of the same authoritative policy.

When a shared engineering policy changes, update the canonical Engineering document and reference it from product repositories as needed.

## 10. Development status

For active products, maintain a concise development-status document when useful.

The status record should identify at minimum:

- current release target;
- completed work;
- active work;
- remaining release work;
- important open issues/PRs;
- known blockers;
- verification state;
- deferred items;
- immediate next action.

The status document should summarize current state rather than become a chronological development diary.

GitHub issues, PRs, and history remain the detailed record.

## 11. Development chat/thread management

Development conversations are temporary working context and should not be allowed to grow indefinitely when most of their contents are no longer relevant.

Proactively recommend starting a new development chat when either:

- approximately **70–80% of the current conversation has become historical context** rather than information needed for the next task; or
- the project reaches a natural major boundary such as:
  - planning → implementation;
  - one major implementation workstream → another;
  - implementation → verification;
  - verification → release;
  - one release → the next release.

Do not create a new thread for every issue. Excessively small threads require unnecessary context reconstruction.

The goal is to retain enough continuity for efficient development while avoiding the cost and ambiguity of very large historical conversations.

## 12. Development chat handoff

Whenever recommending a new development thread, provide a compact handoff prompt that can be pasted directly into the new chat.

The handoff should contain only the context needed to resume accurately:

### Required handoff content

- **Repository/product**
- **Release target**
- **Current phase**
- **Locked decisions relevant to remaining work**
- **Merged PRs relevant to current state**
- **Open PRs/issues**
- **Test/verification status**
- **Known defects, blockers, or deferred items**
- **Exact next action**

The handoff should point the new development session to this standing agreement and the project's current status documentation rather than reproducing lengthy historical process rules.

Example:

> Continue JL Mixing Studio v2.2 development.
>
> Follow `engineering/docs/DEVELOPMENT_AGREEMENT.md` as the standing development agreement and use the Studio repository/GitHub state as the source of truth.
>
> **Release:** v2.2.0  
> **Phase:** Verification  
> **Locked decisions:** [relevant decisions only]  
> **Merged:** [relevant PRs]  
> **Open:** [remaining PRs/issues]  
> **Verification:** [concise current matrix/status]  
> **Known/deferred:** [items]  
> **Next action:** [single clear next step]

Do not copy the complete prior conversation into the handoff.

## 13. Development-session startup

At the beginning of a resumed development session:

1. read the handoff;
2. consult this standing agreement when needed;
3. inspect the current repository/GitHub state;
4. consult the product's current development-status documentation;
5. verify active PR/issue/CI state rather than assuming the handoff is still current;
6. continue from the stated next action.

Repository state supersedes stale conversation summaries.

## 14. Usage and context efficiency

Development work should minimize unnecessary context and tool usage without sacrificing correctness.

Prefer:

- canonical repository documentation over reconstructing policy from old conversations;
- compact handoffs over carrying very large development threads;
- targeted repository reads over repeatedly loading entire files;
- coherent implementation batches over unnecessary build cycles;
- focused CI reruns over rerunning successful jobs;
- concise current-state documentation over chronological transcripts;
- references to locked decisions over repeatedly restating detailed design rationale.

Efficiency must not override correctness, required verification, or user approval.

## 15. Handling uncertainty and failures

Do not guess about repository, CI, release, or test state when it can be verified from GitHub or the relevant source.

When information is uncertain:

- inspect the authoritative source;
- distinguish confirmed facts from assumptions;
- call out uncertainty that could affect implementation;
- avoid destructive or irreversible actions based on uncertain state.

When an operation may have partially succeeded, verify actual repository state before retrying.

## 16. Project-specific exceptions

Individual projects may need exceptions to this agreement.

Project-specific rules should:

- be documented in that repository;
- clearly identify the exception;
- explain why it is necessary when the reason is not obvious;
- override only the relevant portion of this shared agreement.

The remaining provisions of this agreement continue to apply.

---

## Summary

The standing development model is:

**GitHub is the source of truth.  
Engineering documentation holds shared policy.  
Product repositories hold product state and detail.  
Issues define work.  
Development proceeds one high-level issue at a time.  
Only architectural or end-user-impacting design decisions require user input.  
Minor internal implementation fixes do not require separate approval.  
Branches and PRs implement the work.  
CI verifies it.  
High-level issues are normally tested and approved before moving to the next issue.  
Locked decisions remain locked.  
Development chats remain compact working context.  
New threads receive concise, authoritative handoffs.**
