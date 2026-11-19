<!-- Sync Impact Report
- Version change: unversioned scaffold → 1.0.0
- Modified principles: all placeholder principles replaced with Grainpure principles
- Added sections: Technology and Experience Standards; Quality and Delivery Workflow
- Removed sections: none
- Deferred TODOs: RATIFICATION_DATE is unknown; set it when project adoption is formally recorded
-->

# Grainpure Constitution

## Core Principles

### I. Human-Centered Order Tracking

The product MUST make order status, ownership, timing, and next actions immediately understandable. Interfaces MUST provide clear hierarchy, concise labels, visible feedback, recoverable errors, keyboard access, and accessible contrast. Every screen MUST support responsive layouts and preserve context across mobile, tablet, and desktop sizes.

### II. Software Engineering Fundamentals

The implementation MUST use explicit responsibilities, modular boundaries, validated inputs, predictable state transitions, and least-privilege access. Features MUST be independently testable and avoid hidden side effects. Public contracts MUST be versioned or documented when behavior changes.

### III. Test-First Delivery (NON-NEGOTIABLE)

New behavior MUST begin with an executable acceptance or unit test that demonstrates the requirement. Tests MUST fail before implementation, then pass before the change is considered complete. Critical order-tracking journeys MUST include integration coverage across UI, API, and persistence boundaries.

### IV. Clean Code and Sustainable Evolution

Code MUST favor small functions, clear names, consistent formatting, and meaningful abstractions over cleverness or premature generalization. Duplicate behavior MAY be extracted when it reduces risk or clarifies intent. Unrelated refactoring MUST NOT be combined with behavior changes without explicit review.

### V. Professional Visual Quality and Observability

The user experience MUST feel polished, consistent, and intentional through restrained design, consistent spacing and typography, meaningful motion, and reliable loading and empty states. Production behavior MUST emit structured logs and actionable traces for failures, while avoiding secrets or unnecessary personal data.

## Technology and Experience Standards

The project MUST use maintainable, accessible web technologies and responsive components. The UI MUST achieve consistent behavior across supported browsers and screen sizes, with semantic HTML, keyboard navigation, visible focus states, and support for assistive technologies. Data presentation MUST distinguish loading, empty, success, warning, and failure states. Performance-sensitive views MUST avoid blocking interactions and unnecessary rerenders.

## Quality and Delivery Workflow

Every change MUST pass automated formatting, linting, type checks, unit tests, and relevant integration tests before merge. Pull requests MUST identify scope, demonstrate user-visible behavior, and document tradeoffs or migrations. Security review is REQUIRED for authentication, authorization, payment, address, and other sensitive order data. Complexity MUST be justified in writing, and the simplest maintainable solution MUST be preferred unless requirements demand otherwise.

## Governance

This constitution supersedes informal project practices. Amendments require a documented proposal, version update, review, and migration guidance where applicable. A MINOR bump adds or materially expands principles or standards; a MAJOR bump removes or redefines existing governance; a PATCH bump clarifies wording or corrects non-semantic defects. The maintainers MUST verify constitution compliance during design review and pull-request review. Exceptions MUST state scope, reason, reviewer approval, and an expiry or removal plan.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): record formal project adoption date | **Last Amended**: 2026-11-19
