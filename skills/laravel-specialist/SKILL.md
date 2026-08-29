---
name: laravel-specialist
description: >
  Use when building or maintaining Laravel applications — Eloquent ORM, Blade, Livewire,
  queues, Pest testing, middleware, service providers, migrations.
  Trigger conditions: Laravel project setup, Eloquent model design, Blade or Livewire
  component creation, queue/job implementation, Pest test writing, middleware configuration,
  migration authoring, route definition, Form Request validation, policy authorization,
  Sanctum/Passport authentication, Horizon queue monitoring.
---

# Laravel Specialist

## Overview

Design, build, and maintain production-grade Laravel applications following the framework's conventions and best practices. This skill covers the full Laravel ecosystem: Eloquent ORM with advanced relationship patterns, Blade templating and Livewire interactivity, queue and event systems, middleware pipelines, service providers, Pest testing at every layer, and Artisan tooling for migrations, seeders, and factories.

Apply this skill whenever Laravel is the application framework, whether greenfield or brownfield.

## Multi-Phase Process

### Phase 1: Context Discovery

1. Identify Laravel version (`composer.json` -> `laravel/framework`)
2. Scan `config/` for enabled packages and custom configuration
3. Map existing models, relationships, and migration history
4. Review `routes/` for API, web, console, and channel definitions
5. Catalog installed first-party packages (Sanctum, Horizon, Telescope, Pulse, Pennant, Scout, Cashier)
6. Check for Livewire, Inertia, or Blade-only frontend stack

> **STOP — Do NOT begin architecture review without knowing the Laravel version and installed packages.**

### Documentation Verification Protocol

**[HARD-GATE]** When uncertain about any Laravel API — verify, don't guess. Use `mcp__context7__resolve-library-id` then `mcp__context7__query-docs` (preferred). Fallback: fetch from `https://github.com/laravel/docs`. For Livewire, Pest, Inertia — resolve each via context7 separately. Returned docs override memorized knowledge.

### Phase 2: Architecture Review

1. Verify directory structure follows Laravel conventions (see section below)
2. Assess service provider registrations and deferred loading
3. Review middleware stack ordering and grouping
4. Evaluate queue connection configuration and worker topology
5. Check caching strategy (config, route, view, application-level)

> **STOP — Do NOT begin implementation until architecture gaps are documented.**

### Phase 3: Implementation

1. Write migrations first — schema is the source of truth
2. Build Eloquent models with relationships, scopes, casts, and accessors
3. Implement business logic in dedicated Action or Service classes
4. Create controllers (single-action or resourceful) bound to routes
5. Add Form Requests for validation, Policies for authorization
6. Wire events, listeners, and jobs for asynchronous workflows

> **STOP — Do NOT skip Form Requests and Policies. Inline validation and authorization are anti-patterns.**

### Phase 4: Testing

1. Unit tests for isolated logic (Actions, Value Objects, Casts)
2. Feature tests for HTTP endpoints and middleware behavior
3. Browser tests with Laravel Dusk for critical user flows
4. Database assertions with `assertDatabaseHas`, `assertSoftDeleted`
5. Queue and event fakes for side-effect verification

> **STOP — Do NOT proceed to optimization without passing tests at all layers.**

### Phase 5: Optimization

1. Apply eager loading to eliminate N+1 queries
2. Cache expensive computations and config/route/view
3. Index frequently-queried columns; use `EXPLAIN` to verify
4. Profile with Telescope or Debugbar in development
5. Configure Horizon for production queue monitoring

## Eloquent Patterns

### Relationships

| Relationship | Method | Inverse | Use Case |
|---|---|---|---|
| One-to-One | `hasOne` | `belongsTo` | User -> Profile |
| One-to-Many | `hasMany` | `belongsTo` | Post -> Comments |
| Many-to-Many | `belongsToMany` | `belongsToMany` | User <-> Roles (pivot) |
| Has-Many-Through | `hasManyThrough` | — | Country -> Posts (through Users) |
| Polymorphic | `morphMany` / `morphTo` | `morphTo` | Comments on Posts and Videos |
| Many-to-Many Polymorphic | `morphToMany` | `morphedByMany` | Tags on Posts and Videos |

### Scopes

> See [REFERENCE.md](./REFERENCE.md#scopes) for code examples.

### Accessors, Mutators, and Casts

> See [REFERENCE.md](./REFERENCE.md#accessors-mutators-and-casts) for code examples.

### Query Optimization with Eager Loading

> See [REFERENCE.md](./REFERENCE.md#eager-loading) for code examples.

## Blade Templates and Livewire Components

### Blade Conventions
- Layouts: `resources/views/layouts/app.blade.php` using `@yield` / `@section` or component-based `<x-app-layout>`
- Components: `resources/views/components/` — anonymous or class-based
- Partials: `@include('partials.sidebar')` for reusable fragments
- Use `{{ }}` for escaped output, `{!! !!}` only when HTML is explicitly trusted
- Prefer Blade directives (`@auth`, `@can`, `@env`) over raw PHP conditionals

### Livewire Patterns

> See [REFERENCE.md](./REFERENCE.md#livewire-patterns) for code examples.

### Frontend Stack Decision Table

| Decision | Choose Livewire | Choose Inertia |
|---|---|---|
| Existing Blade codebase | Yes | No |
| SPA-like experience required | Partial (with wire:navigate) | Yes |
| Team has Vue/React expertise | No | Yes |
| Server-side rendering priority | Yes | Depends on adapter |
| Real-time reactivity | Yes (polling, streams) | Requires Echo setup |
| SEO-critical pages | Either works | Either works (SSR adapter) |

## Queue, Job, and Event Patterns

### Job Design

> See [REFERENCE.md](./REFERENCE.md#job-design) for code examples.

### Event / Listener Pattern

> See [REFERENCE.md](./REFERENCE.md#event-listener-pattern) for code examples.

### Sync vs Async Decision Table

| Task | Queued | Synchronous |
|---|---|---|
| Sending emails / notifications | Yes | Never in request cycle |
| PDF generation | Yes | Only if < 2s and user waits |
| Payment processing | Depends — webhook-driven preferred | If gateway responds < 5s |
| Cache warming | Yes | Never |
| Audit logging | Yes (high-volume) or Sync (low-volume) | If guaranteed delivery needed |
| Search indexing | Yes | Never |

## Middleware and Service Providers

### Middleware Stack Ordering

Middleware order matters. The default stack in `bootstrap/app.php` (Laravel 11+):

> See [REFERENCE.md](./REFERENCE.md#middleware-stack) for code examples.

### Service Provider Best Practices
- Register bindings in `register()`, never resolve from the container there
- Boot logic (event listeners, route model binding, macros) goes in `boot()`
- Use deferred providers for bindings that are not needed on every request
- Avoid heavy logic in providers — delegate to dedicated classes

## Testing with Pest

> See [REFERENCE.md](./REFERENCE.md#pest-tests) for code examples (Unit Test, Feature Test, Queue and Event Fakes, Browser Test).

## Artisan Commands, Migrations, Seeders, Factories

### Migration Conventions

> See [REFERENCE.md](./REFERENCE.md#migration-conventions) for code examples.

### Factory Patterns

> See [REFERENCE.md](./REFERENCE.md#factory-patterns) for code examples.

## Laravel Directory Structure Conventions

> See [REFERENCE.md](./REFERENCE.md#directory-structure) for the full directory layout.

## Decision Tables

### Authentication Strategy

| Scenario | Recommended Approach |
|---|---|
| SPA + same domain | Sanctum (cookie-based, CSRF) |
| SPA + different domain | Sanctum (token-based) |
| Mobile app | Sanctum (token-based) |
| Third-party API consumers | Passport (OAuth2) |
| Simple API tokens | Sanctum (plaintext hash) |
| Social login | Socialite + Sanctum |

### Caching Layer

| Data Type | Cache Driver | TTL | Invalidation |
|---|---|---|---|
| Config / routes / views | File (artisan cache) | Until next deploy | `artisan optimize:clear` |
| Database query results | Redis / Memcached | 5-60 min | Event-driven or TTL |
| Full-page / fragment | Redis | 1-15 min | Cache tags |
| Session data | Redis | Session lifetime | Automatic |
| Rate limiting | Redis | Window duration | Automatic |

### File Storage

| Scenario | Disk | Driver |
|---|---|---|
| User uploads (production) | `s3` | Amazon S3 / compatible |
| User uploads (local dev) | `local` | Local filesystem |
| Public assets | `public` | Local with symlink |
| Temporary files | `local` | Local, pruned by schedule |

## Anti-Patterns / Common Mistakes

| Anti-Pattern | Why It Fails | What To Do Instead |
|---|---|---|
| Fat controllers | Untestable, unmaintainable business logic | Move logic to Action or Service classes |
| Raw SQL in controllers | SQL injection risk, not portable | Use Eloquent or Query Builder |
| Missing mass-assignment protection | Data manipulation vulnerabilities | Always define `$fillable` or `$guarded` |
| Inline validation in controllers | Couples validation to HTTP layer | Use Form Requests |
| Jobs without retry/backoff config | Silent failures, no recovery | Configure `$tries`, `$backoff`, `failed()` |
| Over-using global scopes | Hidden query behavior surprises developers | Prefer local scopes |
| Storing money as floats | Floating-point precision errors | Use integer cents, convert at presentation |
| Missing database indexes | Slow queries at scale | Add composite indexes for WHERE + ORDER BY |
| Secrets in config files | Credential leaks in version control | Use `.env` exclusively |
| Testing against production DB | Data corruption, unreliable tests | Use SQLite in-memory or dedicated test DB |
| Lazy loading in API responses | N+1 queries, slow API responses | Enable `preventLazyLoading()` in dev |

## Anti-Rationalization Guards

- Do NOT skip migrations and edit the database directly -- migrations are the source of truth.
- Do NOT put business logic in controllers because "it's faster" -- use Action classes.
- Do NOT skip Form Requests because "the validation is simple" -- it always grows.
- Do NOT disable `preventLazyLoading()` because "it's annoying" -- fix the N+1 queries.
- Do NOT store money as floats because "the amounts are small" -- precision errors compound.

## Integration Points

| Skill | How It Connects |
|---|---|
| `php-specialist` | Modern PHP 8.x patterns underpin all Laravel code |
| `laravel-boost` | AI-assisted development guidelines and MCP tooling |
| `senior-backend` | API design, caching strategies, event-driven architecture |
| `test-driven-development` | Pest testing workflow with RED-GREEN-REFACTOR |
| `database-schema-design` | Migration planning, indexing strategy, data modeling |
| `security-review` | Sanctum/Passport configuration, CSRF, input validation |
| `performance-optimization` | Query profiling, cache tuning, queue worker scaling |
| `deployment` | Forge/Vapor/Envoyer deployment, `artisan optimize` |
| `context7 MCP` | Fetches up-to-date Laravel docs when information is uncertain |
| `laravel/docs` GitHub | Authoritative source for Laravel API reference |

## Skill Type

**FLEXIBLE** — Adapt the multi-phase process to the scope of work. A single model change may skip Phase 2 entirely, while a new module should follow all five phases. Core conventions (eager loading, Form Requests, Pest tests, migration-first schema changes) are non-negotiable regardless of scope.
