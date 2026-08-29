---
name: architecture-docs
description: Use when the user asks to generate architecture documentation, draw architecture diagrams, document system topology, map dependency relationships, trace data flows, explain module partitioning, describe event systems, or document middleware chains. Triggers on:"架构文档", "architecture doc", "系统架构", "模块划分", "数据流", "依赖关系", "中间件链", "事件系统", "architecture overview", "system design doc", "技术架构".
---

# Generate Architecture Docs

## Overview

Systematic exploration → structured synthesis → written artifact. Produces a comprehensive architecture document covering all dimensions the user requests. You MUST write the document to a file, not just describe what it would contain.

## When to Use

- User says "生成架构文档", "画架构图", "架构全景", "architecture doc"
- User asks about dependency relationships, module partitioning, data flows
- User wants middleware chain execution order documented
- User asks to document event systems, async task flows

**Do NOT use for:** simple file structure overviews (use `explore` agent directly), code review, bug diagnosis.

## Core Process

### Phase 1: Quick Scan (read in parallel)
Read these files FIRST to understand project topology:
- `CLAUDE.md` / `CONTEXT.md` / `README.md` — domain vocabulary, conventions, stack
- Root configs: `Cargo.toml`, `package.json`, `pyproject.toml` — workspace members, dependencies
- `database.md` 或 schema 文件 — if exists

### Phase 2: Deep Dive (per service, parallel agents)
For EACH service/module, dispatch an `explore` agent to:
- Map source tree (`src/` structure)
- Identify middleware/filter chains
- Find async task / background job / event handlers
- Catalog API routes (HTTP endpoints)

### Phase 3: Synthesize
Cross-reference findings. Fill every section in the template below. If a section doesn't apply to this project, state why and skip it.

### Phase 4: Write
**CRITICAL: You MUST use the `Write` tool to produce a real file.** Do NOT output the document inline as conversation text — it must be a file artifact.

Output paths (by priority):
- `architecture.md` — 项目根目录，作为项目的主架构文档（单文件，不含日期后缀，可被 README/CI 直接引用）
- `docs/architecture-{date}.md` — 带日期的快照副本，保留架构变更历史

## Document Template

Every architecture doc MUST include these sections (skip only with explicit reason):

```
# Architecture: {Project Name}

## 1. Architecture Panorama (架构全景)
- ASCII topology diagram: all services, databases, caches, external systems, message queues
- Technology stack summary table (service | language/framework | port | role)
- Process inventory: how each component starts, health check endpoints

## 2. Dependency Matrix (依赖关系)
- Service call matrix: who calls whom, protocol (HTTP/gRPC/message queue), sync/async
- Data ownership: who reads/writes each database, cache, file store
- Library dependency overlap: shared packages, version constraints

## 3. Module Breakdown (模块划分)
Per service:
- Source tree with file paths and one-line responsibility
- Module grouping by concern (handlers, services, models, adapters)
- External interface each module exposes

## 4. Event System (事件系统)
- Async task catalog: trigger, payload, handler, completion signal
- Background job schedule: cron/timer, interval, idempotency guard
- Callback/polling/reconciliation flows with state machine
- Serialization / ordering guarantees

## 5. Data Flow (数据流)
- Primary request lifecycle: step-by-step from ingress to response
- Fund/data mutation flow: how state changes propagate across services
- Configuration sync flow: how config changes reach runtime

## 6. Middleware Chain (中间件链)
Per service, in execution order:
- Each middleware name, responsibility, and effect on request/response
- How middleware interacts with dependency injection / context propagation

## 7. Database Schema (数据库)
- Table inventory with key columns, indexes, constraints
- Ownership matrix: which service writes, which service reads each table
- Migration strategy (framework, versioning, rollback)

## 8. API Endpoint Inventory
- Complete list: method, path, handler function, auth required, rate limit

## 9. Key Technical Decisions
- ADR references with brief rationale
- Notable design tradeoffs and their consequences

## Appendix
- Redis / cache key patterns
- Protocol / adapter registry
- Environment variable reference (non-secret only)
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Only describing what the doc would contain, not writing it | Use `Write` tool to produce the file |
| Stopping after exploration without synthesizing | Always complete Phase 3 and 4 |
| Producing a flat bullet list instead of structured sections | Follow the template sections |
| Missing middleware chain order | Check every framework's middleware registration code |
| Confusing "who writes" vs "who reads" in data ownership | Create explicit read/write matrix, not just "uses" |
| Skipping async/event flows for synchronous services | Even CRUD apps have background tasks — check for cron, timers, queue consumers |
