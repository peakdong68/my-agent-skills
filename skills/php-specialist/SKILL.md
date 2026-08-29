---
name: php-specialist
description: >
  Use when writing modern PHP 8.x code — enums, fibers, readonly properties, PSR standards,
  Composer, static analysis, SOLID patterns.
  Trigger conditions: PHP code authoring, enum design, readonly DTO creation, PSR-4
  autoloading setup, PHPStan or Psalm configuration, PHP CS Fixer or Pint setup,
  Composer dependency management, SOLID principle application, type safety improvements,
  custom exception hierarchies, interface-driven design.
---

# PHP Specialist

## Overview

Write modern, type-safe, and maintainable PHP 8.x code adhering to PSR standards and SOLID principles. This skill covers the full modern PHP toolchain: language features introduced in PHP 8.0 through 8.4, PSR interoperability standards, Composer dependency management, static analysis with PHPStan and Psalm, coding style enforcement with PHP CS Fixer and Pint, and architectural patterns that leverage the type system for correctness at compile time rather than runtime.

Apply this skill whenever PHP code is being written, reviewed, or refactored in any framework or standalone context.

## Multi-Phase Process

### Phase 1: Environment Assessment

1. Identify PHP version from `composer.json` -> `require.php`
2. Review `composer.json` for autoloading strategy (PSR-4 namespaces)
3. Check for static analysis configuration (`phpstan.neon`, `psalm.xml`)
4. Identify coding standard tool (`pint.json`, `.php-cs-fixer.php`)
5. Catalog existing patterns: enums, DTOs, value objects, interfaces

> **STOP — Do NOT write code without knowing the PHP version and autoloading strategy.**

### Phase 2: Design

1. Define interfaces and contracts before implementations
2. Design value objects and DTOs with readonly properties
3. Map domain concepts to backed enums where applicable
4. Plan exception hierarchy for the domain
5. Identify seams for dependency injection

> **STOP — Do NOT implement without interfaces defined for key boundaries.**

### Phase 3: Implementation

1. Write interfaces first — contracts before concrete classes
2. Implement with constructor promotion, readonly properties, union/intersection types
3. Use match expressions over switch; named arguments for clarity
4. Leverage first-class callable syntax for functional composition
5. Apply SOLID principles at every class boundary

> **STOP — Do NOT skip strict_types declaration in any PHP file.**

### Phase 4: Quality Assurance

1. Run PHPStan at maximum achievable level (target level 9)
2. Enforce coding style with PHP CS Fixer or Laravel Pint
3. Verify type coverage — no `mixed` without justification
4. Review for SOLID violations and code smells
5. Confirm Composer autoload is optimized (`--classmap-authoritative`)

## PHP Version Feature Decision Table

| Feature | Minimum Version | Use When |
|---|---|---|
| Constructor promotion | 8.0 | Any class with constructor parameters |
| Named arguments | 8.0 | Functions with 3+ params or boolean flags |
| Match expressions | 8.0 | Any switch statement (strict, returns value) |
| Union types | 8.0 | Parameter accepts multiple types |
| Backed enums | 8.1 | Any set of named constants with values |
| Readonly properties | 8.1 | Immutable DTOs, value objects |
| Fibers | 8.1 | Async frameworks (rarely used directly) |
| First-class callables | 8.1 | Functional composition, array_map/filter |
| Readonly classes | 8.2 | All-readonly DTOs (shorthand) |
| DNF types | 8.2 | Complex union + intersection combinations |
| Override attribute | 8.3 | Overriding parent methods (safety check) |
| Property hooks | 8.4 | Computed properties without separate methods |

## Modern PHP 8.x Features

### Enums (PHP 8.1+)
> See [REFERENCE.md](./REFERENCE.md#enums) for code examples.

### Readonly Properties and Classes (PHP 8.1 / 8.2)
> See [REFERENCE.md](./REFERENCE.md#readonly-properties-and-classes) for code examples.

### Constructor Promotion
> See [REFERENCE.md](./REFERENCE.md#constructor-promotion) for code examples.

### Named Arguments
> See [REFERENCE.md](./REFERENCE.md#named-arguments) for code examples.

### Match Expressions
> See [REFERENCE.md](./REFERENCE.md#match-expressions) for code examples.

### Union and Intersection Types
> See [REFERENCE.md](./REFERENCE.md#union-and-intersection-types) for code examples.

### First-Class Callable Syntax (PHP 8.1+)
> See [REFERENCE.md](./REFERENCE.md#first-class-callable-syntax) for code examples.

### Fibers (PHP 8.1+)
> See [REFERENCE.md](./REFERENCE.md#fibers) for code examples.

## PSR Standards

| PSR | Name | Relevance |
|---|---|---|
| PSR-1 | Basic Coding Standard | Baseline: `<?php` tag, UTF-8, namespace/class conventions |
| PSR-4 | Autoloading | Map namespaces to directories in `composer.json` — mandatory |
| PSR-7 | HTTP Message Interfaces | Immutable request/response objects for middleware pipelines |
| PSR-11 | Container Interface | Dependency injection container interoperability |
| PSR-12 | Extended Coding Style | Supersedes PSR-2: formatting, spacing, declarations |
| PSR-15 | HTTP Server Middleware | `MiddlewareInterface` and `RequestHandlerInterface` |
| PSR-17 | HTTP Factories | Create PSR-7 objects (RequestFactory, ResponseFactory) |
| PSR-18 | HTTP Client | `ClientInterface` for interoperable HTTP clients |

### PSR-4 Autoloading
> See [REFERENCE.md](./REFERENCE.md#psr-4-autoloading) for code examples and the directory-mapping rule.

## Composer Dependency Management

### Essential Commands

| Command | Purpose |
|---|---|
| `composer require package/name` | Add production dependency |
| `composer require package/name --dev` | Add development dependency |
| `composer update --dry-run` | Preview what would change |
| `composer why package/name` | Show why a package is installed |
| `composer audit` | Check for known security vulnerabilities |
| `composer bump` | Update version constraints to installed versions |
| `composer validate --strict` | Validate `composer.json` and `composer.lock` |

### Best Practices
- Always commit `composer.lock` — reproducible installs across environments
- Use `^` (caret) constraints: `"laravel/framework": "^12.0"` allows minor/patch updates
- Separate dev dependencies: testing, static analysis, and debug tools go in `require-dev`
- Run `composer audit` in CI to catch known vulnerabilities
- Use `composer dump-autoload --classmap-authoritative` in production for speed

## Static Analysis

### PHPStan Levels

| Level | What It Checks |
|---|---|
| 0 | Basic: undefined variables, unknown classes, wrong function calls |
| 1 | + possibly undefined variables, unknown methods on `$this` |
| 2 | + unknown methods on all expressions (not just `$this`) |
| 3 | + return types verified |
| 4 | + dead code, always-true/false conditions |
| 5 | + argument types of function calls |
| 6 | + missing typehints reported |
| 7 | + union types checked exhaustively |
| 8 | + nullable types checked strictly |
| 9 | + `mixed` type is forbidden without explicit handling |

### PHPStan Configuration
> See [REFERENCE.md](./REFERENCE.md#phpstan-configuration) for code examples.

### PHP CS Fixer / Pint
> See [REFERENCE.md](./REFERENCE.md#php-cs-fixer--pint) for code examples.

For Laravel projects, use Pint with a `pint.json` preset — it wraps PHP CS Fixer with Laravel-specific defaults.

## SOLID Principles in PHP

| Principle | Guideline | PHP Mechanism |
|---|---|---|
| **S** — Single Responsibility | One reason to change per class | Action classes, small services |
| **O** — Open/Closed | Extend behavior without modifying source | Interfaces, strategy pattern, enums |
| **L** — Liskov Substitution | Subtypes must be substitutable for base types | Covariant returns, contravariant params |
| **I** — Interface Segregation | Clients depend only on methods they use | Small, focused interfaces |
| **D** — Dependency Inversion | Depend on abstractions, not concretions | Constructor injection, interface bindings |

### Dependency Inversion Example
> See [REFERENCE.md](./REFERENCE.md#dependency-inversion-example) for code examples.

## Error Handling Patterns

### Custom Exception Hierarchy
> See [REFERENCE.md](./REFERENCE.md#custom-exception-hierarchy) for code examples.

### Result Pattern (Error as Value)
> See [REFERENCE.md](./REFERENCE.md#result-pattern) for code examples.

## Type Safety Patterns

### Branded / Opaque Types via Readonly Classes
> See [REFERENCE.md](./REFERENCE.md#branded--opaque-types-via-readonly-classes) for code examples.

### Generic Collections via PHPStan Annotations
> See [REFERENCE.md](./REFERENCE.md#generic-collections-via-phpstan-annotations) for code examples.

## Anti-Patterns / Common Mistakes

| Anti-Pattern | Why It Fails | What To Do Instead |
|---|---|---|
| Using `mixed` as escape hatch | Holes in type safety net | Narrow with union types or generics |
| Stringly-typed code | Runtime errors from typos | Use backed enums for named constants |
| God classes (many responsibilities) | Untestable, high coupling | Split into Action classes |
| Suppressing static analysis | Hides real bugs | Fix the issue, add `@phpstan-ignore` only with explanation |
| Missing `declare(strict_types=1)` | Silent type coercion bugs | Add to every PHP file |
| Array-shaped domain data | No IDE support, no type safety | Use readonly DTOs or value objects |
| Service locator (`app()` in logic) | Hidden dependencies, untestable | Constructor injection |
| Catching `\Exception` broadly | Swallows unexpected errors | Catch specific exception types |
| Mutable value objects | Shared state bugs | Use `readonly` classes, return new instances |
| Ignoring `composer audit` | Known vulnerabilities in production | Run in CI, treat as build failure |
| Deep inheritance (3+ levels) | Fragile base class problem | Prefer composition and interfaces |
| Classes not marked `final` | Unintended extension | Default to `final`, open only when designed for it |

## Anti-Rationalization Guards

- Do NOT skip `declare(strict_types=1)` because "it's just a small script" -- add it everywhere.
- Do NOT use `mixed` without a comment justifying why a narrower type is impossible.
- Do NOT suppress PHPStan errors without a written explanation of why the code is correct.
- Do NOT use the service locator pattern (`app()`) in business logic, even in Laravel.
- Do NOT skip interfaces for key boundaries because "there's only one implementation" -- there will be two.

## Documentation Lookup (Context7)

Use `mcp__context7__resolve-library-id` then `mcp__context7__query-docs` for up-to-date docs. Returned docs override memorized knowledge.
- `php` — for language features, built-in functions, or PHP 8.x syntax
- `composer` — for package management, autoloading, or scripts configuration

---

## Integration Points

| Skill | How It Connects |
|---|---|
| `laravel-specialist` | PHP 8.x features power Eloquent casts, enums, readonly DTOs, and typed collections |
| `senior-backend` | SOLID architecture, interface-driven design, error handling patterns |
| `test-driven-development` | PHPUnit/Pest testing with strong type assertions |
| `clean-code` | SOLID, DRY, code smell detection at the PHP level |
| `security-review` | Input validation, type coercion risks, dependency vulnerabilities |
| `laravel-boost` | AI-generated PHP code quality via guidelines and MCP tools |

## Skill Type

**FLEXIBLE** — Adapt the process phases to the scope of work. A single function may need only Phase 3 and 4. A new module or package should follow all four phases. Non-negotiable regardless of scope: `declare(strict_types=1)`, PHPStan compliance at the project's configured level, and PSR-4 autoloading.
