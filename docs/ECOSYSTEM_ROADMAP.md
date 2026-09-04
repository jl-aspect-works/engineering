# JL Aspect Works Ecosystem Roadmap

**Status:** Proposed for review  
**Scope:** JL Mixing Automation and JL Mixing Studio

## Purpose

This roadmap defines how the JL Mixing products evolve together without forcing their release numbers or schedules to match.

## Product roles

### JL Mixing Automation

JL Mixing Automation is the workflow engine and command-line product. It owns:

- project and client creation rules;
- project lifecycle and delivery behavior;
- metadata validation and schema support;
- filesystem mutations and rollback behavior;
- machine-readable workflow operations;
- compatibility guarantees for its public Automation API.

### JL Mixing Studio

JL Mixing Studio is the desktop user experience. It owns:

- navigation, visualization, search, filtering, and reporting;
- guided interaction with supported Automation operations;
- local presentation preferences and rebuildable indexes;
- progress, notification, and error presentation;
- operating-system integration that does not change Automation business rules.

Studio must not duplicate Automation workflow logic or become a competing source of project state.

## Independent version tracks

The ecosystem uses three independent version tracks:

1. **Automation application version** — the released CLI/engine package, such as `1.4.0`.
2. **Automation API version** — the stable machine contract consumed by Studio, such as `1.0`.
3. **Metadata schema version** — the persisted workspace document contract, currently `1.1.0`.

Studio has its own application version and declares supported Automation API and metadata schema ranges. Product versions do not need to match.

## Compatibility policy

- Studio depends on an Automation API range, not an exact Automation application release.
- Backward-compatible API additions increment the API minor version.
- Breaking API changes require a new API major version.
- Metadata schema changes are separate from API and application releases.
- Studio may use capability detection for optional features and should degrade gracefully when an optional capability is unavailable.
- A coordinated release is required only when a Studio feature depends on a newly released API capability or metadata schema.

## Feature ownership test

Every proposed feature must be classified before release planning.

| Classification | Primary owner | Required coordination |
|---|---|---|
| Studio-only presentation or local preference | Studio | None beyond compatibility tests |
| Existing Automation API consumer | Studio | API regression coverage |
| New workflow or filesystem behavior | Automation | New API capability plus Studio integration |
| Persisted project information | Metadata schema and Automation | Schema design, writers, readers, compatibility plan |
| Breaking contract change | Automation API major | Coordinated migration and Studio support |

## Roadmap themes

### Near term

- Finish and stabilize JL Mixing Studio 1.0 against the existing Automation 1.3.0 command contract.
- Define and implement Automation API 1.0 as a machine-readable compatibility layer.
- Add startup compatibility discovery to Studio.
- Begin Studio post-1.0 usability work that does not require new workflow semantics.

### Medium term

- Add backward-compatible API capabilities for structured project discovery, health checks, reports, and batch operations when justified by approved Studio features.
- Expand Studio search, reporting, batch workflows, and project visibility.
- Keep metadata changes conservative and tied to information that must persist with the project.

### Long term

- Develop Studio into a studio-aware workspace through session intelligence, recall management, backup awareness, and asset management.
- Preserve Automation as the stable, scriptable engine beneath Studio and other future clients.

## Release planning rule

Roadmaps describe direction. A feature enters a release only after its scope is approved and represented by linked GitHub issues. Cross-repository work must have separate implementation issues linked to one parent design issue.

## Out of scope

The ecosystem should not become a DAW, accounting system, general CRM, cloud file-sync service, or mandatory hosted collaboration platform.
