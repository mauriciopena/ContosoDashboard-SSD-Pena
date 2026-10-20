# ContosoDashboard Constitution

## Core Principles

### I. Local-First Training Scope

ContosoDashboard MUST remain an educational application, not a production reference.
New features MUST run locally without cloud accounts, paid services, or external
application-service dependencies. Restore-time package downloads are permitted.
Mock user-selection login MUST remain explicitly labeled training-only; documentation
MUST NOT describe it as production security. New runtime network dependencies MUST be
identified and given a local alternative before acceptance. This keeps exercises
available without provisioning infrastructure.

### II. Clear Service and Infrastructure Boundaries

Blazor pages and shared components MUST delegate business rules and data access to
services; they MUST NOT access `ApplicationDbContext` or storage directly. Services
MUST expose interfaces and use constructor injection, with implementations registered
in `Program.cs`. EF Core entities and mappings MUST remain in `Models/` and `Data/`.
New infrastructure capabilities MUST have interface-backed local implementations.
Changes MUST reuse these boundaries rather than introduce speculative frameworks,
so learners can trace behavior and replace infrastructure without rewriting the UI.

### III. Authorization at Every Data Boundary

Protected pages and endpoints MUST require authentication. Each service operation
that reads or changes protected data MUST verify the requesting user's role,
ownership, or project membership against the feature's explicit permission rules.
The requesting identity MUST come from authenticated server-side context, not an
editable form value. Hiding UI controls or accepting a resource ID is not authorization.
List, search, detail, and mutation paths MUST enforce the same access policy.
Changes MUST include allowed and denied access checks to prevent cross-user leakage,
even though authentication is deliberately mocked for training.

### IV. Explicit Data Integrity and Failure Handling

New database I/O MUST use EF Core asynchronous APIs. Changes MUST define required
fields, relationships, deletion behavior, and applicable uniqueness constraints.
Multi-step writes MUST use transactions where possible; database-and-file operations
MUST specify cleanup or compensation for partial failure. Schema changes MUST state
how an existing local database is upgraded or explicitly recreated; destructive resets
MUST require user consent. Expected failures MUST produce actionable UI messages;
unexpected failures MUST be logged without secrets or document contents.
These rules prevent silent data loss and make training failures diagnosable.

### V. Specified and Verifiable Changes

Feature work MUST start with a specification containing observable acceptance
scenarios, permissions, error cases, and scope exclusions, followed by a plan and
dependency-ordered tasks. Stakeholder documents are requirements inputs, not proof
that a capability exists or authorization to implement it outside the agreed scope.
Changed business rules and authorization paths MUST receive automated regression
tests; persistence changes MUST be checked against SQLite behavior. UI changes MUST
include a repeatable manual acceptance check. Verification reports MUST distinguish
passed checks, failures, and checks not run; absent tests MUST NOT be reported as passing.

## Technology and Data Constraints

- The baseline is ASP.NET Core 8 / .NET 8, Blazor Server, Razor Pages for login/logout,
  EF Core with SQLite, nullable reference types, and Bootstrap-based styling.
  Framework or persistence replacements MUST be justified in the implementation plan
  and reviewed for their training impact before implementation.
- Local startup MUST retain a documented path to create and seed a disposable database.
  The current `EnsureCreated()` approach is not a schema migration mechanism.
- Existing role policies (`Employee`, `TeamLead`, `ProjectManager`, `Administrator`)
  MUST be reused unless the specification explicitly defines a reviewed permission change.
- New file storage MUST stay outside `wwwroot`, use generated storage names rather
  than user-supplied paths, validate size and allowed types server-side, and serve
  protected content only through authorized application operations.
- Changes MUST NOT introduce credentials, real personal data, or uploaded user content
  into version control. Training limitations and any simulated security control MUST
  be documented without claiming production guarantees.

## Development Workflow and Quality Gates

1. Before implementation, review this constitution, `README.md`, relevant
   `StakeholderDocs/` inputs, and the affected services and pages. Use the Spec Kit
   specify → plan → tasks workflow and record unresolved requirement conflicts.
2. Keep changes bounded to the approved task. Record deviations and their rationale;
   do not add cloud integrations or unrelated refactors as incidental work.
3. For application changes, run
   `dotnet build ContosoDashboard/ContosoDashboard.csproj`, the relevant automated
   tests, and the specification's manual acceptance checks. Record commands and outcomes.
   The initial repository has no automated test project; the first change requiring
   regression tests MUST add the necessary focused test infrastructure.
4. Authentication or permission changes MUST verify unauthenticated denial, permitted
   access, and cross-user or non-member denial with the seeded training identities.
   Data changes MUST verify local creation and the documented upgrade/reset path
   using a disposable database.
5. Before merge, a reviewer MUST check constitution compliance and acceptance evidence.
   Required checks MUST pass; an environment blocker MUST be documented and resolved
   before merge, not counted as success. Documentation-only changes require document
   validation rather than an application build.

## Governance

This constitution governs new and modified work. Existing training limitations are
not claims of compliance and do not authorize new violations. Conflicting README,
stakeholder, or feature guidance MUST be reconciled before implementing the affected
work; feature scope alone cannot override a constitutional rule.

Amendments MUST be proposed as a reviewed constitution change with rationale, affected
principles, compatibility impact, and any transition work. Maintainer approval is
required before adoption. A feature-specific exception MUST be explicit, bounded,
and approved through the same amendment process.

Versions MUST follow semantic versioning: MAJOR for incompatible principle removals
or redefinitions, MINOR for new principles or materially expanded guidance, and PATCH
for non-semantic clarifications. Amendments MUST preserve the original ratification
date and update the last-amended date. Initial adoption is version 1.0.0.

Plans and pull requests MUST include a constitution compliance check. Maintainers
MUST review compliance when approving changes and reconsider this document when the
training scope, technology baseline, or security model changes. Amendment reviews
MUST include a temporary Sync Impact Report; remove that review comment before commit.
Dependent templates and commands read the constitution at runtime and are not changed
by this constitution workflow.

**Version**: 1.0.0 | **Ratified**: 2026-10-20 | **Last Amended**: 2026-10-20
