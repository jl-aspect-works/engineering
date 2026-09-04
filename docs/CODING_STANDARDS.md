# JL Aspect Works Coding Standards

These standards define the default engineering expectations for JL Aspect Works software projects.

They are intended to keep the codebase readable, maintainable, testable, and approachable for contributors while avoiding unnecessary ceremony. Individual repositories may add stricter project-specific rules, but should not silently weaken these standards without a documented reason.

## 1. Core principles

JL Aspect Works code should favor:

- clarity over cleverness;
- small, cohesive modules over large multipurpose files;
- explicit behavior over hidden side effects;
- simple control flow over deeply nested logic;
- stable public interfaces over unnecessary churn;
- automated verification over manual confidence;
- comments that explain intent and constraints rather than restating code;
- free or near-free open-source tooling unless a paid dependency is explicitly approved.

Refactoring should preserve behavior unless the change is explicitly intended to alter behavior and is reviewed as such.

## 2. File and module size

File size is a maintainability signal, not an absolute quality metric. Large files should be reviewed for opportunities to separate independent responsibilities.

### Default thresholds

- **Target:** keep implementation files at approximately **300 lines or fewer** where practical.
- **Review threshold:** files approaching **500 lines** should trigger an explicit review of module boundaries and responsibilities.
- **Strong refactor signal:** files above **750 lines** normally should be split unless there is a clear reason that keeping the code together improves maintainability.

Generated code, large declarative tables, schemas, fixtures, snapshots, or intentionally centralized configuration may exceed these thresholds.

A file should be split when it contains multiple independently understandable responsibilities, even if it is below the numerical threshold.

Do not split a cohesive implementation merely to satisfy a line count.

## 3. Function and procedure size

Functions should normally perform one conceptual operation at one level of abstraction.

### Default thresholds

- **Target:** most functions should remain below approximately **40 lines** of executable logic.
- **Review threshold:** functions approaching **60 lines** should be reviewed for extraction opportunities.
- **Strong refactor signal:** functions above **100 lines** normally should be decomposed unless the logic is inherently sequential and splitting it would reduce clarity.

Blank lines, comments, simple data declarations, and formatting should not be treated as meaningful complexity when evaluating these thresholds.

A short function can still be too complex. Refactor earlier when a function has:

- multiple distinct responsibilities;
- deeply nested conditionals;
- repeated validation or error mapping;
- numerous boolean flags controlling unrelated paths;
- significant setup mixed with business logic;
- difficult-to-test internal branches.

Prefer small helpers with descriptive names when they make the main workflow easier to read.

## 4. Control-flow complexity

Prefer early returns and guard clauses where they reduce nesting.

As a general rule:

- avoid nesting beyond **3 levels** when practical;
- split complex conditionals into named predicates or helpers;
- avoid long `if`/`else if` chains when a clearer data-driven or dispatch structure exists;
- avoid boolean parameters whose meaning is unclear at the call site;
- use enums or explicit operation types when behavior has meaningful named modes.

Do not introduce abstractions solely to reduce a complexity metric. The resulting code must be easier to understand.

## 5. Naming

Names should describe domain intent rather than implementation mechanics.

- Use nouns for data types and values.
- Use verbs or verb phrases for operations.
- Prefer domain terminology already used by JL Aspect Works products and APIs.
- Avoid unexplained abbreviations.
- Avoid names such as `data`, `temp`, `thing`, `handler`, or `manager` when a more specific name is available.
- Boolean names should read naturally as true/false statements where possible.

Public names should be especially stable and descriptive because they become part of the project's conceptual interface.

## 6. Comments and documentation

Comments are required where they add information that cannot be learned easily from the code itself.

### Comments should explain

- **why** a non-obvious decision was made;
- important invariants or assumptions;
- compatibility constraints;
- platform-specific behavior;
- non-obvious error handling or retry restrictions;
- security, safety, or data-integrity concerns;
- intentionally unusual implementation choices;
- temporary workarounds and the condition under which they can be removed.

### Comments should not

- restate what the next line of code obviously does;
- compensate for unclear naming or unnecessarily complex code;
- preserve obsolete explanations after the implementation changes;
- contain commented-out code that belongs in version control history.

Complex algorithms or workflows should have a short overview comment or documentation block describing the approach before implementation details begin.

Public APIs, externally consumed data structures, configuration formats, and non-obvious modules should have appropriate documentation comments.

There is no required comment-to-code ratio. The standard is sufficient explanation for a competent contributor to understand the intent and constraints without reverse-engineering historical decisions.

## 7. Module boundaries and visibility

Modules should represent cohesive responsibilities.

Prefer:

- workflow-specific modules;
- shared helpers only when behavior is genuinely shared;
- narrow visibility by default;
- explicit re-exports when preserving a stable public surface.

Do not make an item `public` merely to simplify tests or bypass module design. Use the narrowest visibility that satisfies the requirement, such as package/module-scoped visibility where the language supports it.

Avoid generic utility modules that become collections of unrelated helpers.

## 8. Duplication and abstraction

Small amounts of obvious duplication are preferable to premature abstraction.

Extract shared code when:

- the behavior represents the same domain concept;
- multiple implementations must change together;
- duplication creates a realistic maintenance risk;
- the abstraction has a clear and specific name.

Do not create generic frameworks for hypothetical future reuse.

## 9. Error handling

Errors should preserve useful context and distinguish expected user-facing failures from unexpected or uncertain outcomes.

- Never silently ignore meaningful errors.
- Avoid unchecked assumptions where failure is plausible.
- Do not automatically retry destructive or potentially non-idempotent operations unless retry safety is established.
- Preserve enough context for diagnostics without exposing secrets or sensitive values.
- User-facing messages should describe the problem and, when practical, the next useful action.

When an operation may have completed but confirmation failed, report the result as uncertain rather than encouraging an automatic retry.

## 10. Data and API compatibility

Public interfaces, serialized data, schemas, CLI behavior, and inter-product APIs are contracts.

Changes to those contracts require explicit compatibility consideration and should follow the JL Aspect Works versioning and architecture decisions documented in the engineering repository.

Structural refactors should not change externally observable behavior unless that behavior change is part of the approved scope.

## 11. Tests

Behavior that matters should be covered by automated tests at the lowest useful level.

Every bug fix should normally include a regression test when the failure can be reproduced automatically.

Tests should cover:

- normal success paths;
- important validation failures;
- meaningful boundary conditions;
- error mapping and uncertain outcomes;
- compatibility-sensitive behavior;
- destructive or irreversible operations where applicable.

Tests should be deterministic and independent of execution order.

Avoid tests that merely duplicate implementation details without validating behavior.

## 12. Test organization

Test code is production engineering code and should remain readable and maintainable.

- Reuse focused fixtures and helpers when they improve clarity.
- Avoid giant all-purpose test setup functions.
- Name tests after observable behavior or the condition being verified.
- Keep mocks and fakes narrow and intentional.
- Prefer explicit test inputs and expected outputs over hidden global state.

Large test modules should be split using the same cohesion principles as implementation code.

## 13. Formatting, linting, and static checks

Repositories should use the standard formatter and linter for their language/toolchain whenever practical.

Warnings that indicate code quality or correctness issues should be treated as build failures in CI where the toolchain supports that policy.

For Rust projects, the default JL Aspect Works expectation is:

- `cargo fmt --check`;
- `cargo clippy --all-targets --all-features -- -D warnings`;
- `cargo test --all-features`.

Equivalent project-appropriate checks should be used for other languages.

## 14. Dependencies

Dependencies should be added deliberately.

Before adding a dependency, consider:

- whether the standard library or an existing dependency already solves the problem;
- project activity and maintenance health;
- license compatibility;
- security history;
- binary size and runtime impact;
- platform support;
- whether the dependency introduces a paid service or proprietary lock-in.

Avoid dependencies for trivial functionality that is clearer to implement locally.

## 15. Platform-specific code

Platform-specific behavior should be isolated where practical and clearly identified.

Code intended to behave consistently across macOS, Windows, and Linux should avoid relying on platform-specific path rules, shell behavior, line endings, executable conventions, or filesystem semantics unless handled explicitly.

Cross-platform projects should validate compilation or behavior on all supported release platforms through CI at an appropriate level.

## 16. Security and sensitive data

Never commit:

- credentials;
- API keys;
- signing secrets;
- private certificates or keys;
- personal access tokens;
- private customer or user data.

Logs and error messages should avoid exposing sensitive information.

Security-sensitive code should prefer well-established libraries and platform facilities over custom cryptographic or authentication implementations.

## 17. Git and pull-request workflow

JL Aspect Works repositories should use protected development practices:

- do not make normal development changes directly on `main`;
- use focused branches and pull requests;
- keep PRs limited to one coherent change when practical;
- separate structural refactoring from behavior changes when possible;
- require relevant automated checks to pass on the exact PR head before merge;
- review significant architectural or behavioral changes before implementation or merge;
- use descriptive commits and PR summaries that explain intent and scope.

Minor cleanup directly related to an approved change may be included when it does not expand behavioral scope.

## 18. Refactoring expectations

Refactor when structure is making the code harder to understand, test, or safely modify.

Common signals include:

- oversized files or functions;
- repeated domain logic;
- unrelated responsibilities in the same module;
- tests requiring broad internal visibility;
- frequent merge conflicts in a central file;
- changes that routinely require understanding unrelated code;
- difficult-to-isolate platform or API behavior.

Prefer incremental, behavior-preserving refactors over large rewrites.

## 19. Exceptions

These standards are defaults, not mechanical rules.

An exception is acceptable when following the rule would make the code less clear, less safe, or less maintainable. Significant exceptions should be apparent in code review and, when the tradeoff is likely to recur, documented in the repository or an Architecture Decision Record.

The objective is maintainable engineering, not compliance with arbitrary metrics.

## 20. Review checklist

Before merging a change, reviewers and authors should ask:

- Is the change doing one coherent thing?
- Are file and function sizes still reasonable for their responsibilities?
- Is complex logic decomposed enough to understand and test?
- Are names clear and domain-specific?
- Are comments present where intent or constraints are non-obvious?
- Are stale or redundant comments absent?
- Is visibility as narrow as practical?
- Are errors handled explicitly and safely?
- Are compatibility implications understood?
- Are meaningful success and failure paths tested?
- Do formatter, linter, tests, and required CI checks pass?
- Has unnecessary dependency or architectural complexity been avoided?
- Would a new contributor be able to understand the change without reconstructing its history?
