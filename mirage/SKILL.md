---
name: mirage
metadata:
  version: "1.0.0"
description: Add or improve a development-only Mirage mode for an existing application, with coherent fixtures and working local interactions. Use when building UI without a running backend or database, adding a mock-data toggle, or exercising populated, empty, loading, and error states.
---

# Mirage

Make the actual application usable with local mock data while preserving its real backend path.

## Discover the contracts

Read project guidance and relevant project documentation, then inspect the code before inventing fixtures or choosing an interception point. Discover the declared database schema, models, relationships, and API contracts from source; do not connect to a live database merely to discover data shapes. Trace the requested screens from their UI hooks and actions to data providers, endpoints, serialization, and domain definitions. Follow the structural sources already in the project rather than duplicating them in documentation.

- For Laravel, inspect migrations, models, relationships, casts, resources, and the relevant routes and controllers. Match the serialized data the UI receives, including loaded relationships and pagination, rather than merely copying database columns.
- For Convex, inspect schema, validators, queries, mutations, and their UI callers. Preserve function references, argument and return shapes, subscription behavior, and IDs as required by the existing client contract.
- For other stacks, find the equivalent schema, operations, serialization, and consumer contracts.

Identify which operations the requested flow actually uses and the smallest existing seam that can supply them through a thin mock behavior layer. Prefer a provider, client, transport, or data-access boundary that keeps normal UI callers intact. Do not build a generic mocking framework or change the app's architecture to resemble another project.

## Implement the local mode

Add a clearly labeled development-only toggle using the project's environment configuration and UI conventions; select the mock or real provider at the existing data boundary. Name the environment flag `MIRAGE` and enable it only when its value is exactly `true` (`MIRAGE=true`) and the app is in a development environment. Where frontend exposure requires a framework prefix, preserve the name, for example `NEXT_PUBLIC_MIRAGE=true`; do not substitute `MOCK_MODE` or `DEV_MODE`. Gate activation with the stack's trusted development environment check; a browser preference alone must not enable Mirage mode in production. When off, use the existing real backend path. Keep mock state and caches isolated so switching modes cannot send fixture IDs or queued writes to the real backend.

Mirage mode must boot and serve the scoped flow without a database, backend connection, backend credentials, or external side effects. Select the mock path before real clients, subscriptions, authentication lookups, or server data loaders initiate those dependencies. Keep any simulated user identity local to Mirage mode; preserve real authentication and authorization when off.

For apps with role-based access, discover the actual roles from the project. In development Mirage mode, replace the normal authentication form with small buttons, one per relevant role. Selecting a button sets a local mock identity and enters that role's UI; let the user return to the existing authentication page to choose another role. Keep real authentication unchanged when Mirage mode is off, and avoid adding an elaborate role switcher.

Use deterministic, fictional fixtures with stable identifiers and coherent relationships, statuses, dates, totals, and ownership. Include enough variation for the actual UI. Prefer related JSON seed fixtures when they fit the project, adapting to existing fixture conventions; implement behavior in ordinary code using existing domain patterns. Keep a resettable local store where interactions need state.

Implement the operations needed to complete the scoped flow: filters, search, sorting, pagination, detail lookup, and local create/update/delete actions as applicable. Apply mutations consistently across related views and derived values, and notify existing consumers through the selected seam. Do not silently report success for unsupported operations; expose an honest mock limitation or implement the local behavior. Never fall through to the real backend from a missing mock handler.

Provide selectable populated, empty, loading, and error scenarios through the same boundary, preserving the UI's expected asynchronous and error contracts. Keep delays and failures controlled rather than random. Make resetting fixtures and returning to real mode predictable; use in-memory state unless persistence serves the requested development workflow.

## Verify the behavior

Use appropriate existing checks and exercise the scoped screens and interactions. Confirm Mirage mode works with the backend unavailable and makes no real requests or writes, mutations update their consumers, scenarios render correctly, and disabling the toggle restores real data access without mock state leakage. Check that production cannot activate the mock path. Add tests only when the user asks; distinguish static checks from behavior actually exercised.

After implementation, tell the user which environment file to edit and the exact flag to manually add or set (`MIRAGE=true` or the framework-required prefixed variant), including a dev-server restart if needed. Do not edit real environment files or claim Mirage mode is activated. Report the toggle location, supported flow and scenarios, reset behavior, checks performed, and any unimplemented operation or verification blocker. Keep changes limited to the requested application scope.
