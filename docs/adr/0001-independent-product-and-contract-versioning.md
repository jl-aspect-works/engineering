# ADR-0001: Version products and contracts independently

- **Status:** Proposed
- **Date:** 2026-07-26
- **Decision owners:** JL Aspect Works maintainers

## Context

JL Mixing Studio currently integrates with a specific JL Mixing Automation release and command contract. Continuing to align product release numbers would couple unrelated UI and engine work, increase coordinated release overhead, and obscure the difference between application releases, machine interfaces, and persisted data formats.

## Decision

Use independent versioning for:

- JL Mixing Studio application releases;
- JL Mixing Automation application releases;
- the public Automation API;
- metadata schemas.

Studio shall declare a supported Automation API range and supported metadata schema versions. It shall not infer compatibility from matching application release numbers.

Automation API compatibility follows major/minor rules:

- minor versions may add optional fields, operations, or capabilities without breaking existing clients;
- existing field meanings, required fields, operation identifiers, and documented result semantics remain stable within one major version;
- removals or incompatible semantic changes require a new major version.

## Consequences

### Positive

- Studio can release UI improvements without an Automation release.
- Automation can fix or extend the engine without forcing a Studio release.
- Compatibility becomes explicit and testable.
- Metadata evolution remains deliberate and separate from application packaging.

### Negative

- The ecosystem must maintain compatibility declarations and contract tests.
- Optional capabilities require graceful unavailable states in Studio.
- Cross-repository features need linked planning and release coordination.

## Transition

Studio 1.0 remains tied to the released Automation 1.3.0 command contract. Automation API 1.0 will be introduced as a new formal contract; a later Studio release will adopt API discovery and range-based compatibility.

## Rejected alternatives

- Matching Studio and Automation release numbers.
- Treating metadata schema versions as application versions.
- Letting Studio call undocumented command output and infer compatibility.
