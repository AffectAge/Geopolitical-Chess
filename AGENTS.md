# AGENTS.md — Mobile Persistent Hex Strategy

> **Status:** approved technical baseline for a **new** repository, dated 2026-11-09. This file describes a proposed project, **not an existing implementation**. Paths, scripts, modules, tests, and CI workflows below are **target contracts to create during bootstrap**. Do not claim that they already exist or have been tested.
>
> **Evidence policy:** distinguish requirements explicitly confirmed by the owner, engineering proposals supported by primary sources, and game rules still awaiting a decision. Never attribute undocumented internal mechanics to Travian or *The Third World: War of Kings*.

## 1. Purpose

This is the primary operating manual for AI coding agents building a mobile-browser multiplayer strategy game with one persistent shared hexagonal world. Follow existing code and Architecture Decision Records (ADRs) once available. Before making non-trivial architectural changes, review official documentation and verifiable source implementations.

### Confirmed owner requirements

1. **One shared world for all players.** Do not create player-specific worlds, separate matches, game rooms, or multiple instances of the game world. Multiple technical processes may still support the same single world.
2. **Continuous real time.** Action completion and server-side timers are governed by authoritative server time and persistent data, independently of the Phaser rendering frame rate.
3. **Mobile-first interface.** Client technologies: Phaser, React, Vite, and TypeScript.
4. **Target scale:** thousands of concurrently connected players. Initial load-test scenarios should include **1,000 and 5,000 concurrent users**; these are test targets, not verified capacity claims.
5. **SQLite initially**, with a migration path to a different database if measured requirements justify the change.
6. **Data-driven content and rules:** entity types, characteristics, balancing parameters, constraints, and visual definitions are versioned data rather than scattered source-code constants.
7. **Genre references:** Travian and *The Third World: War of Kings*. Their specific gameplay rules must not be copied or assumed without evidence and the owner's approval.

### Not yet approved — ask before implementing

- Combat, diplomacy, territory, construction, troop training, trading, production, economy, victory conditions, seasons, PvP, and specific resources.
- World boundaries, world expansion, map generation, and the starting playable area.
- Action durations, costs, production rates, combat coefficients, or any other numerical balance settings.
- **Server downtime semantics:** whether real time continues to elapse during an outage or active timers are paused and shifted after recovery.
- Distribution platform beyond mobile web/PWA. Native wrappers and app-store publication require separate approval.

## 2. Project Snapshot

| Field | Value |
| --- | --- |
| Project name | Mobile Persistent Hex Strategy — **working technical name**, not approved branding |
| Project type | New monorepo: web client, game server, shared contracts, and game data |
| One-line description | A persistent, mobile-first online strategy game with one authoritative real-time hexagonal world |
| Primary users | Mobile-browser players; game administrators and developers |
| Domain | Persistent-world MMO strategy |
| Lifecycle stage | Pre-development / architecture planning; **not production** |
| Maintainer | Mark — requirements owner; development team not yet designated |
| Default branch | Planned `main` after repository creation |
| Repository status | New project; no implemented modules, executable scripts, migrations, or CI yet |

## 3. Agent Principles

Priorities: **world-state correctness and integrity → security → measured performance → maintainability → development speed**.

### MUST

- Read this file, relevant `docs/`, neighboring code, tests, and contracts before changing a module. During bootstrap, create the missing minimum documentation and test foundation.
- Prefer the smallest safe change consistent with a single world, authoritative server time, and a single source of truth.
- For non-trivial engineering decisions, consult official documentation and at least one primary source (official example, source code, or project-maintained documentation); record references in the PR or ADR.
- **Before writing custom infrastructure**, search for reusable repository code, then assess existing dependencies, then evaluate documented third-party libraries. Implement a custom solution only when justified by requirements and measured tradeoffs.
- Before adding or replacing a major library, framework, architectural pattern, database, queue, rendering system, or service, present the owner with the problem, viable alternatives, benefits, limitations, compatibility, maintenance costs, and migration costs. **Obtain approval first.** Suggest better alternatives when justified.
- Prefer typed, versioned, data-driven configuration over repeated type-specific branching. Validate configuration before the server starts and in CI.
- Add or update tests whenever behavior changes, especially for transactions, time calculations, and event contracts.
- Explicitly label statements as **verified in code**, **documented by a source**, **proposed**, or **not load-tested**.

### MUST NOT

- Create separate `world_id` values per player or gameplay rooms/matches for the main map.
- Make Phaser or React authoritative for resources, movement, combat, building completion, or ownership.
- Use one in-memory `setTimeout`/`setInterval` per building, unit, action, or player as the sole timer storage.
- Treat 5,000 idle WebSocket connections as evidence that the game can support 5,000 active players. Test command throughput, SQLite writes, and event fan-out separately.
- Introduce Redis, Kafka, RabbitMQ, microservices, ECS, GraphQL, native mobile frameworks, or a different game engine by default. Introduce new systems only after evidence and explicit approval.
- Change game balance, authentication rules, production infrastructure, or live data without authorization.
- Perform speculative rewrites or hide failing checks relevant to changed behavior.

## 4. Sources of Truth

### Planned in-repository references

| Source | Target path | Use | Current status |
| --- | --- | --- | --- |
| Game design | `docs/product/game-design.md` | Gameplay rules approved by the owner | Create before implementing mechanics |
| Architecture | `docs/architecture/overview.md` | Module boundaries and single-world command flow | Create during bootstrap |
| ADRs | `docs/adr/` | Reasons for database, protocol, rendering, and scaling decisions | Create during bootstrap |
| API and protocol contracts | `packages/protocol/src/` | HTTP/WS schemas and message versions | Not created |
| Content schemas | `packages/game-data/src/schemas/` | Allowed game-data formats and semantics | Not created |
| UI design | `docs/ui/mobile.md` | Screens, devices, and map interactions | Not created |
| Security | `docs/security.md` | Sessions, authorization, secrets, rate limits | Not created |
| Operations | `docs/ops/` | Deployment, backup, restoration, monitoring, rollback | Not created |
| Performance | `docs/performance/` | Load profiles, SLOs, benchmark results | Not created |

If code and documentation materially disagree, **do not silently choose one**. Investigate tests and contracts; notify the owner and ask for a decision when the mismatch concerns game rules, persisted data, security, or compatibility.

### External primary sources

- [Official Phaser documentation](https://docs.phaser.io/) and [Phaser 4.2.1 release](https://phaser.io/download/release/v4.2.1).
- [Official Phaser + React + TypeScript + Vite template](https://github.com/phaserjs/template-react-ts): example of integration and the React–Phaser event bridge, **not an MMO backend**.
- [Phaser Tilemap API](https://docs.phaser.io/api-documentation/class/tilemaps-tilemap): documents hexagonal tilemap support; suitability for very large worlds requires benchmarking.
- [React 19.3](https://react.dev/blog/2026/09/09/react-19-3) and [Vite 8 release](https://vite.dev/blog/announcing-vite8).
- [Node.js LTS releases](https://nodejs.org/en/about/previous-releases), [Fastify v5](https://fastify.dev/docs/latest/Guides/Migration-Guide-V5/), and [@fastify/websocket](https://github.com/fastify/fastify-websocket).
- [SQLite WAL](https://www.sqlite.org/wal.html), [SQLite over a network](https://www.sqlite.org/useovernet.html), [SQLite PRAGMAs](https://www.sqlite.org/pragma.html), and [SQLite Online Backup API](https://www.sqlite.org/backup.html).
- [Kysely getting started](https://kysely.dev/docs/getting-started): typed SQL and SQLite driver; SQL-dialect support **does not** make migration of existing data automatic.
- [Red Blob Games: hexagonal grids](https://www.redblobgames.com/grids/hexagons/): primary algorithm reference for axial/cube geometry.
- [Screeps standalone server](https://github.com/screeps/screeps): an open-source persistent-world MMO and example of separating engine logic from the runtime; **do not automatically adopt its tick model**.
- [Luanti map blocks](https://docs.luanti.org/for-engine-devs/basic-data-structures/) and [engine architecture](https://docs.luanti.org/for-engine-devs/structure/): examples of chunked world handling; its voxel format is not directly applicable to 2D hex maps.
- [OWASP WebSocket Security](https://cheatsheetseries.owasp.org/cheatsheets/WebSocket_Security_Cheat_Sheet.html) and [Grafana k6 WebSocket testing](https://grafana.com/docs/k6/latest/using-k6/protocols/websockets/).

Fan-written descriptions of Travian or *The Third World* are not evidence of their server internals. No source code for these commercial game servers was supplied.

## 5. Tech Stack

These are **target versions and ranges**, not installed dependencies. During bootstrap, confirm compatibility and security advisories, then pin exact resolved versions through `package.json`, `pnpm-lock.yaml`, the runtime image, and CI. Never write an unqualified `latest` dependency.

| Area | Choice | Target version/range | Purpose and constraints |
| --- | --- | --- | --- |
| Language | TypeScript | `6.0.x`, strict | Web, server, shared contracts |
| Runtime | Node.js | `26.x` LTS | API, WebSocket, authoritative logic |
| Package manager | pnpm | `11.x` | Workspace management and lockfile |
| Rendering | Phaser | `4.2.1` | Client rendering, camera, animation, input only |
| UI | React + React DOM | `19.3.x` | Login, HUD, menus, game controls |
| Frontend bundler | Vite | `8.1.x` | Web build and development server |
| Backend framework | Fastify | `5.x` | HTTP API, authentication boundary, routes |
| Realtime networking | `@fastify/websocket` + `ws` | `@fastify/websocket` `11.3.x`, `ws` `8.x`; verify compatible patches | Commands, subscriptions, events |
| SQL query layer | Kysely | `0.29.6`; recheck advisories at bootstrap | Keep SQL details out of domain logic |
| SQLite driver | `better-sqlite3` | Secure Node-26-compatible release | Local SQLite; do not block the network event loop with synchronous queries |
| Validation | Zod | `4.x` | Content definitions, API/WS messages, configuration |
| Client UI state | Zustand | `5.x` | UI-only state, not authoritative world state |
| Unit/integration testing | Vitest | `4.x` | Domain, protocol, persistence |
| E2E testing | Playwright | `1.x` | Mobile browser and touch workflows |
| Load testing | Grafana k6 | Explicitly pinned compatible CLI release | HTTP and WebSocket simulations |
| Formatting/linting | Prettier / ESLint | `3.x` / `9.x` | Code conventions |
| Database | SQLite | Actual library version reported by `sqlite_version()` | Initial source-of-truth storage |
| External queue/cache | None initially | N/A | Local command queue and SQLite-backed jobs |
| Hosting | Not selected | N/A | Do not invent a deployment provider |

Official Phaser templates demonstrate a React integration pattern; Fastify provides WebSocket support; Kysely supports SQLite and other SQL dialects; SQLite WAL allows only one simultaneous writer. The built-in `node:sqlite` module was still documented as a release candidate in Node.js 26: do not replace the selected driver without review and testing.

### Version policy

- At bootstrap, check patch versions, security advisories, Node ABI support, peer dependencies, and CI behavior. Record **exact resolved versions** in the lockfile.
- Get approval before dependencies outside this table are introduced. Update this table and the corresponding ADR.
- Security updates still require a normal reviewed PR and tests. Major upgrades require separate approval.
- Confirm the supported Android Chrome / iOS Safari matrix through actual device testing before promising a minimum browser version.

## 6. Architecture

**Starting style:** modular monolith with Clean Architecture boundaries around the game domain and event-driven command processing. This is an **engineering proposal**, not a claim that every MMO uses this design.

### Logical components

1. **Web client:** React UI, Phaser map, HTTP/WS network client, disposable local visualization caches.
2. **Gateway/API:** Fastify routes and WebSocket upgrade, sessions, authentication, validation, connection limits, rate limits. TLS terminates at an approved edge.
3. **Application:** command handling, per-action authorization, use-case orchestration, transaction coordination.
4. **Domain/world:** authoritative world rules, timers, geometry, ownership, and subsequently approved gameplay systems. No dependencies on Phaser, React, Fastify, Kysely, SQLite, or browser APIs.
5. **Persistence/scheduler:** SQLite adapters, migrations, durable jobs, transactional outbox, and restart recovery.
6. **Distribution:** viewport/chunk subscriptions, permission-aware visibility filters, per-client event delivery.

**Initial runtime:** one logical world authority backed by one database. A separate worker thread or process may own serialized SQLite access so the synchronous driver cannot stall network connections. The precise physical split must be benchmarked and recorded in an ADR. Multiple gateways, partitions, or brokers are later options, **not separate game worlds**.

### Main data flow

```text
React/Phaser (player intent and display)
    -> HTTP/WS gateway (session, validation, limits)
    -> application command (ownership, invariants)
    -> world domain (server time, approved rules)
    -> SQLite transaction (state, jobs, outbox)
    -> committed events and visibility filter
    -> viewport subscriptions -> client cache -> Phaser/React
```

### 6.1 Single-world invariants

- Production has one **authoritative game universe**. A fixed/technical `world_id` must never become a mechanism for per-player sessions or separate matches.
- Every state-changing action must pass server-side checks for authorization, affordability, entity state, and applicable time rules. Treat client-supplied values and coordinates as untrusted.
- Every modifying command has a `command_id` or idempotency key, `actor_id`, protocol version, and explicit outcome. Redelivery must not charge resources twice.
- Persist state changes and corresponding outbox events atomically; never broadcast success before commit.
- Do not send complete world state to every connection. Limit snapshots, queries, and updates by viewport, authorization, and level of detail.
- The database is the source of truth. In-memory caches need clear invalidation and restart recovery semantics.

### 6.2 Server time and durable jobs

- Persist long-running operations with `started_at`, `due_at` (UTC milliseconds or an equivalent formally specified format), status, owner, type, and validated payload.
- Index due jobs by `status, due_at`; process bounded batches, with transactional duplicate protection.
- A `setTimeout` may wake a scheduler but is **not** the authoritative record. Recover pending jobs from SQLite after crashes/restarts.
- Where game rules permit, calculate passive production using `last_calculated_at` and an approved rate instead of updating every settlement each second. Test caps, rate changes, rounding, and outage behavior.
- Test clock boundaries, retries, crashes before/after commit, delayed execution, and duplicate delivery.
- **Stop and ask the owner** how downtime affects elapsed game time before implementing recovery semantics.
- Do not promise exactly-once WebSocket delivery. Support recovery through snapshots, versioned updates, and idempotent commands.

### 6.3 Hexagonal world

- Store canonical positions as signed-integer **axial `(q, r)`** coordinates; derive `s = -q - r` for cube operations. Pixels and offset row/column coordinates must not be authoritative world keys.
- Centralize `neighbor`, `distance`, `ring`, `line`, `pixelToHex`, and `hexToPixel` in `packages/hex-core`; verify against [Red Blob Games](https://www.redblobgames.com/grids/hexagons/) with unit/property-based tests. Evaluate [honeycomb-grid](https://github.com/flauwekeul/honeycomb) before maintaining custom grid infrastructure.
- Ask the owner to choose flat-top or pointy-top orientation, visual scale, and world density before finalizing assets and persisted coordinates.
- Keep **hex/terrain data** separate from optional visual layers (rivers, roads, resources, decorations, buildings, units, fog, selection). Implement a layer only when its gameplay/content requirements are approved.
- Spatially partition persisted and streamed map data into chunks using `chunk_q`, `chunk_r`, and local coordinates. Choose chunk size from measurements, not an arbitrary universal constant.
- Stream only visible/authorized chunks. Use client caching and eviction, discard offscreen objects, update changed elements, and avoid creating a Phaser Sprite for every tile in the entire world.
- Prototype both Phaser's documented hexagonal Tilemap API and, if necessary, chunk-level batched rendering. Choose based on FPS, memory, load times, and visual requirements on actual mobile devices. Do not write a custom renderer before comparative testing.
- Version texture atlases and content manifests. If seasons, forest density, or damage variants are approved, express mappings in validated game data rather than rendering conditionals scattered across Scenes.
- Preserve previously generated and modified chunks across generation algorithm upgrades. Store seed, generator version, and persisted modifications. Approve any world-expansion policy separately.

### 6.4 Data-driven content

- Store game definitions under `packages/game-data/content/**/*.json`; store schemas and validators under `packages/game-data/src/schemas/`. Use YAML only if its parser and workflow are approved.
- Give every content entry a stable ID and content version. Validate uniqueness, reference integrity, numeric ranges, and cyclic dependencies during build/startup.
- Definitions may contain validated `id`, `type`, parameters, requirements, assets, and rule references **only as required by an approved mechanic**. Do not invent building/unit catalogs or numerical balance.
- Data supplies parameters; authoritative server domain functions interpret them. Never execute scripts from JSON, call `eval`, or accept client-uploaded rules as authoritative.
- Represent resources/currency using documented integer scaling or another exact numerical policy rather than accumulating floating-point errors.
- Semantic changes to existing IDs require explicit content migration and compatibility decisions. Client and server must agree on `content_version`.
- Do not mix balance with React components or spread `if (buildingType === ...)` across unrelated modules.

### 6.5 Networking and state distribution

- Use HTTPS for authentication, sessions, initial snapshots, and non-push requests. Use WebSocket for commands, acknowledgements, subscriptions, and world events. Handle intermittent mobile connectivity with bounded backoff and resynchronization.
- Messages include `type`, `protocol_version`, and `request_id`/`command_id` when applicable. Server events carry a documented sequence/version **within a defined ordering scope**; do not promise a global total order across future distributed processes.
- Limit payload size, per-player command rate, event fan-out, and per-connection send queues. Apply coalescing, backpressure, or disconnect/resync policies to slow consumers.
- Authorize viewport subscriptions server-side. Never leak hidden objects, other players' private state, or fog-of-war information through chunk snapshots or deltas.
- Do not scan/serialize entire SQLite world tables for each camera move. Use bounded chunk snapshots and deltas.
- Internal broadcast groups by chunk are acceptable; **gameplay rooms or distinct world instances are not**.

### 6.6 SQLite and future migration

- Start with one SQLite database on a durable **local** disk controlled by the server. Do not assume ephemeral hosting disks are persistent. Do not place the WAL database on an arbitrary network filesystem.
- Enable and verify `PRAGMA foreign_keys=ON` on every connection, `journal_mode=WAL`, a finite `busy_timeout`, and `synchronous=FULL` for strong durability of game transactions; measure its performance cost. Any weaker durability setting requires an ADR and owner approval.
- **SQLite WAL permits only one writer at a time.** Keep write transactions short. Monitor `SQLITE_BUSY`, checkpoint behavior, WAL growth, writer-queue depth, and p95/p99 query latency.
- Isolate the DB adapter behind domain repository ports. Neither Kysely nor SQLite types may leak into the domain or browser client.
- Persist mutations, jobs, and outbox events in the same transaction. For contested entities, protect against lost updates with version conditions/atomic SQL and verify affected row counts.
- Run only reviewed, versioned schema migrations with backup and tests against representative data. Never automatically apply schema-push development shortcuts to production.
- Back up live databases using the [SQLite Online Backup API](https://www.sqlite.org/backup.html) or a documented safe equivalent; test restoration and integrity. Plainly copying an active `game.db` while WAL is in use is not a verified backup procedure.
- Consider PostgreSQL or another persistence architecture when measured write throughput, lock contention, multi-server write requirements, or operations justify it. Concurrent user count **alone** does not determine the database choice.

## 7. Repository Structure — Planned

```text
repo-root/
├── AGENTS.md
├── README.md
├── package.json                  # workspace scripts; to be created
├── pnpm-workspace.yaml           # apps/*, packages/*
├── pnpm-lock.yaml                # generated by pnpm; commit it
├── .env.example                  # safe example names/values only
├── apps/
│   ├── web/
│   │   └── src/
│   │       ├── app/              # React shell, providers, routing
│   │       ├── features/         # screens and UI capabilities
│   │       ├── game/             # Phaser Scenes, renderer, camera, input
│   │       └── network/          # HTTP/WS, reconnect, resync
│   └── server/
│       └── src/
│           ├── api/              # Fastify HTTP/WS, authentication
│           ├── application/      # commands, authorization, use cases
│           ├── domain/           # pure authoritative rules
│           ├── infrastructure/   # SQLite, scheduler, outbox
│           └── bootstrap/        # configuration and dependency wiring
├── packages/
│   ├── protocol/src/            # DTOs, schemas, versioned messages
│   ├── hex-core/src/            # hex math and spatial keys
│   └── game-data/
│       ├── content/             # versioned JSON definitions
│       └── src/schemas/         # validation and types
├── db/
│   ├── migrations/              # versioned SQL/Kysely migrations
│   └── seeds/                   # development/test only
├── tests/
│   ├── e2e/                     # Playwright
│   └── load/                    # k6 HTTP/WS scenarios
├── docs/
│   ├── adr/
│   ├── architecture/
│   ├── product/
│   ├── ops/
│   ├── performance/
│   ├── ui/
│   └── security.md
└── .github/workflows/           # CI after bootstrap
```

### Directory responsibilities

| Path | Must contain | Must not contain |
| --- | --- | --- |
| `apps/web/src/game/` | Phaser Scenes, rendering, camera/input | Authoritative resources, combat, SQL, economy timers |
| `apps/web/src/features/` | React mobile UI and controls | Direct authoritative world mutations |
| `apps/server/src/api/` | HTTP/WS boundaries, authentication, validation | Embedded combat/economy rules |
| `apps/server/src/application/` | Commands, queries, transactions, use cases | Phaser, DOM, UI state |
| `apps/server/src/domain/` | Pure rules and invariants | Fastify, SQLite, wall-clock timers, WebSockets |
| `apps/server/src/infrastructure/` | DB, scheduler, event adapters | Unchecked public write interfaces |
| `packages/protocol/` | Shared DTOs and message schemas | Passwords, secrets, private DB entities |
| `packages/hex-core/` | Coordinate math and spatial helpers | Phaser GameObjects |
| `packages/game-data/` | Definitions, schemas, validation | Arbitrary executable scripts in JSON |
| `db/` | Migrations and non-production seeds | Production DB files, private backups |
| `tests/load/` | Reproducible stress scenarios | Production secrets, real personal data |

**Placement rules:** features belong in cohesive feature modules; create shared abstractions only for demonstrated multiple consumers. Store migrations in `db/migrations`. Never hand-edit `dist`, coverage, or generated output. Do not commit them. Add nested `AGENTS.md` files only when concrete local conventions justify them.

## 8. Environment Setup

**No project-specific commands exist until `package.json` is created.** The first bootstrap PR must implement the command contract below and verify every relevant script. Never claim those commands currently work in an empty repository.

Target local tooling: Node.js 26 LTS, pnpm 11, Git, a compatible native `better-sqlite3` build on Linux/macOS/Windows, and optional SQLite CLI for diagnostics. Docker is **optional** initially. Install k6 and Playwright browsers when their tests are introduced.

After bootstrap, use this sequence:

```bash
npm install --global pnpm@11
pnpm install --frozen-lockfile
pnpm db:migrate
pnpm dev
```

During the initial bootstrap, use `pnpm install` once to create the lockfile, then commit it. Subsequently use `pnpm install --frozen-lockfile` in CI. `pnpm db:seed` must be repeatable and limited to local/test data. Keep real secrets in ignored local environment files or an approved secrets manager.

The future `.env.example` should define at least `NODE_ENV`, `HOST`, `PORT`, `DATABASE_PATH`, `WEB_ORIGIN`, and `SESSION_SECRET` (empty placeholder, **never a real secret**). Do not invent hosting credentials or production addresses.

## 9. Development Commands — Bootstrap Contract

**These are required future script names, not verified commands.** The initial PR must create them, make them executable from the repository root, and replace this table with confirmed commands and a verification date.

| Task | Planned command | Scope/notes |
| --- | --- | --- |
| Install dependencies | `pnpm install --frozen-lockfile` | From committed lockfile |
| Start development | `pnpm dev` | Web and server |
| Start web | `pnpm --filter @game/web dev` | Client only |
| Start server | `pnpm --filter @game/server dev` | Server only |
| Build | `pnpm build` | All relevant packages/apps |
| Typecheck | `pnpm typecheck` | Strict TypeScript everywhere |
| Lint | `pnpm lint` | ESLint |
| Check formatting | `pnpm format:check` | Prettier, no writes |
| Format | `pnpm format` | Prettier writes changed files |
| Unit/integration tests | `pnpm test` | Vitest |
| One test file | `pnpm exec vitest run packages/hex-core/src/hex.test.ts` | Example once the file exists |
| One test case | `pnpm exec vitest run -t "axial distance"` | Example once the case exists |
| End-to-end tests | `pnpm test:e2e` | Playwright mobile flows |
| Load tests | `pnpm test:load` | k6 scripts under `tests/load/`, never production |
| Apply migrations | `pnpm db:migrate` | Versioned migrations |
| Seed local/test data | `pnpm db:seed` | Never production by default |
| Validate content | `pnpm data:validate` | Schemas and semantic references |

Do not invent a code-generation command; add one only when a real generator exists. Builds and checks must return nonzero exit codes on failures.

## 10. Testing Guide

**Planned frameworks:** Vitest, Playwright, Grafana k6. **Planned locations:** source-adjacent `*.test.ts` / `*.test.tsx`, `tests/e2e/*.spec.ts`, `tests/load/*.js`. **Planned CI:** `.github/workflows/ci.yml`, created during bootstrap.

| Test type | Trigger | Required examples |
| --- | --- | --- |
| Unit | Every algorithm/rule | Six neighbors, hex distance, numerical boundaries, rounding, pure economy logic |
| Data/schema | Content or contract changes | Duplicate IDs, invalid references, cycles, wrong versions |
| SQLite integration | Storage/scheduler changes | Transactions, foreign keys, uniqueness, WAL behavior, restart, idempotency |
| HTTP/WS contract | Protocol changes | Unauthorized command, invalid payload, duplicate request, version mismatch |
| Mobile E2E | Player workflow changes | Login → one world → camera → command → reconnect |
| Recovery | Jobs, crashes, migrations | Crash before/after commit, redelivery, backup restoration |
| Performance | Map, SQL, WebSocket, fan-out | Concurrent connections **and** realistic read/write rates |
| Security | Auth, ownership, visibility | Forbidden object, hidden data, Origin, replay, flood protection |

### Load testing rules

- Test **1,000 and 5,000 concurrent users** across multiple activity profiles: idle, camera movement/chunk subscription, chunk inspection, command scheduling, bursts of due jobs, and mass reconnects.
- Record CPU/RAM, event-loop lag, SQLite writer-queue depth, `SQLITE_BUSY`, WAL/checkpoints, SQL latency, WebSocket bytes per connection, and client FPS/memory — not just HTTP p95.
- **Never invent pass/fail SLOs.** The owner must approve latency, reliability, and hardware budgets in `docs/performance/slo.md`; until then, report raw measurements rather than claiming the architecture supports 5,000 active players.
- For domain changes, run focused unit tests, then SQLite integration, protocol tests, and the broader suite as relevant.
- For rendering changes, profile on representative low-end Android and supported iOS Safari devices, not only desktop DevTools.

## 11. Code Style and Naming

- TypeScript: `strict: true`, `noUncheckedIndexedAccess: true`. Document any exception. Avoid using `any` to bypass contracts; use `unknown` and validate input boundaries.
- Formatter: Prettier 3. Linter: ESLint 9. Establish workspace-specific configuration at bootstrap.
- ESM by default; explicit, stable imports. Do not cross module boundaries through deep internal imports or introduce cyclic domain dependencies.
- Use typed domain errors/`Result` objects for expected rejections; application/API layers map them to safe responses. Log unexpected failures without secrets or sensitive payloads.
- Use structured logs correlated by `request_id` and `command_id`. Handle player identifiers appropriately for privacy and external observability.
- Comments explain intent, invariants, and the source of unusual algorithms — not obvious syntax.

| Item | Preferred | Avoid | Example |
| --- | --- | --- | --- |
| Files | `kebab-case.ts[x]` | Mixed casing | `hex-chunk-loader.ts` |
| Directories | `kebab-case` | Inconsistent naming | `world-state/` |
| Components/classes | `PascalCase` | `snake_case` | `WorldMapView` |
| Functions/variables | Intent-revealing `camelCase` | `data1`, `doIt` | `calculateHexDistance` |
| Module constants | `UPPER_SNAKE_CASE` | Magic numbers | `MAX_MESSAGE_BYTES` |
| Types/schemas | `PascalCase` | Redundant `I` prefixes | `WorldEvent` |
| Tests | Behavior-based descriptions | `works correctly` | `rejects duplicate build command` |
| Branches | `feat/`, `fix/`, `docs/`, `perf/` | Arbitrary names | `feat/chunk-streaming` |

Keep modules cohesive. Do not create dumping-ground `utils` folders, hide side effects in vague helpers, or invent abstractions before a demonstrated second use case.

## 12. Preferred Patterns and Reference Implementations

**There are no good or bad in-repository examples yet: the repository does not exist. Do not fabricate paths to implemented modules.** Replace this section with real paths after the first verified modules are merged.

External reference implementations — study only the relevant concepts:

1. [`phaserjs/template-react-ts`](https://github.com/phaserjs/template-react-ts): Phaser initialization, React bridge, and Vite configuration. Template versions may differ from those chosen here.
2. [`screeps/screeps`](https://github.com/screeps/screeps) and [`screeps/engine`](https://github.com/screeps/engine): environment-independent game engine logic. Do not automatically adopt its world-tick model or infrastructure.
3. [`luanti-org/luanti`](https://github.com/luanti-org/luanti): map blocks, client/server boundaries, caching. C++ and voxel storage are **not** drop-in solutions for this 2D hex game.
4. [Phaser Tilemap API](https://docs.phaser.io/api-documentation/class/tilemaps-tilemap) and [Red Blob hex algorithms](https://www.redblobgames.com/grids/hexagons/): documented rendering capabilities and coordinate mathematics.

**Avoid:** copying room-based sample servers into the global-world design; loading the whole map into each client; performing resource calculations in React; polling every row every second; giant Phaser Scenes; WebSocket route handlers that combine SQL and gameplay rules; broad unowned shared utility modules.

## 13. Data, Contracts, Code Generation, and Migrations

| Concern | Target location | Rule |
| --- | --- | --- |
| HTTP/WS schema | `packages/protocol/src/` | Version and test compatibility |
| Domain models | `apps/server/src/domain/` | No Kysely/persistence types |
| Game content | `packages/game-data/content/` | Stable IDs; validation in CI/startup |
| Content schema | `packages/game-data/src/schemas/` | Validate without executing arbitrary content |
| Database migrations | `db/migrations/` | Review, backup, recovery/rollback strategy |
| DB adapters | `apps/server/src/infrastructure/` | Only permitted dependence on SQL dialect |
| Generated files | Only a directory documented by an actual generator | Regenerate; never hand-edit |

**Candidate conceptual entities, not an approved schema:** account/player, session, world, chunk/hex, ownership, game entity, scheduled server job, processed command, outbox event. Define concrete columns and tables only when the corresponding use case is approved. Do **not** create tables for unapproved game systems.

Preserve contract compatibility where required. The supported old-client window needs a specific owner-approved policy; breaking changes require version negotiation. Every production-data migration needs a migration test and restore plan. Never store raw passwords; approve session/authentication architecture before implementing login.

## 14. Security and Safety Boundaries

### Hard rules

- The server validates permissions for every creation, cancellation, movement, read, ownership change, and payment/resource deduction. Never trust client-provided `player_id`/`owner_id`.
- Authenticate HTTP and WebSocket connections. On WebSocket upgrade, verify an allowed `Origin`, session validity, expiration, and connection limits; authorize each message at the action level.
- Enforce bounds on JSON payload size/depth, command frequency, and WebSocket output queues. Never execute client expressions or script-like content.
- Never expose private player state, fog-of-war entities, secret fields, or hidden entities via chunk snapshots/events.
- Never log secrets, session cookies, raw tokens, passwords, sensitive personal data, or unrestricted WS payloads.
- Store secrets only in approved environment variables/secret managers. Ignore `.env`, `*.db`, `*.db-wal`, and real backups in Git.
- Protect resource/inventory transactions against races, duplication, negative balances, and repeated client requests.
- Test rate limiting, mass sign-ups, credential stuffing, flooding, and replay attacks.

### Human approval required before

- Installing/replacing a major dependency or introducing major infrastructure.
- Implementing payments, changing authentication/roles/permissions, or rotating production secrets.
- Deleting data/files, running destructive migrations, changing production deployment/backup procedures, or replacing the database.
- Changing the world model, game rules, access policy, or authoritative network contract.

**Sensitive future areas:** `apps/server/src/api/auth/`, `apps/server/src/application/`, `apps/server/src/infrastructure/`, `db/migrations/`, and `docs/ops/`. Update these with actual paths after implementation.

## 15. Git, PR, and Definition of Done

- Branch names: `feat/*`, `fix/*`, `docs/*`, `perf/*`, `chore/*`.
- Commit messages: Conventional Commits (`feat:`, `fix:`, `docs:`, etc.). PR titles should describe outcomes without invented ticket IDs.
- Protect `main` after initialization. Use reviewable PRs, CI, and owner approval for architectural and game-design decisions.
- Record user-visible behavior and save-format changes in `CHANGELOG.md` once created. Do not fabricate an established release process.

A change is complete only when:

1. It preserves the single-world, authoritative-time, and data-driven boundaries.
2. Relevant unit/integration/security tests pass; DB, protocol, rendering, or scaling changes also receive appropriate recovery/load tests.
3. Contracts, schemas, migrations, content versions, and documentation are updated as needed.
4. No secrets, unauthorized dependencies, unchecked SQL writes, fabricated paths, or unsupported performance claims remain.
5. The PR summarizes outcome, primary sources, exact verification commands/results, risks, assumptions, and open questions.

## 16. Monorepo Guidance

- Keep global rules in the root `AGENTS.md`. Add `apps/web/AGENTS.md`, `apps/server/AGENTS.md`, or `packages/*/AGENTS.md` only when concrete module-specific guidance becomes necessary.
- Nested instructions must not weaken single-world, authoritative-server, security, or data-driven invariants.
- Changes to `packages/protocol` and `packages/game-data` require checks across every consumer. Client and server must not import each other's implementation details.

## 17. Known Pitfalls

1. **SQLite WAL is not multiwriter.** Adding API instances does not linearly scale writes. [SQLite WAL](https://www.sqlite.org/wal.html).
2. **SQLite WAL on network filesystems.** Shared-memory assumptions require compatible local hosting; do not move the file to an arbitrary shared disk. [SQLite networking caveats](https://www.sqlite.org/useovernet.html).
3. **Synchronous SQLite driver.** Heavy `better-sqlite3` operations can block Node.js threads; isolate hot-path work and measure event-loop lag.
4. **Game time is not FPS.** Closing a tab must not cancel authoritative operations.
5. **Idle WebSocket tests do not prove MMO capacity.** Test command writes, due jobs, fan-out, and reconnect storms.
6. **Viewport access is not authorization.** Subscribing to a chunk must not reveal private information or fog-of-war contents.
7. **Hex coordinate mismatches.** Never mix axial, cube, offset, and pixel coordinates without centralized, tested conversion.
8. **A Tilemap demo is not an infinite-world benchmark.** Validate memory and mobile performance.
9. **Content evolution.** Keep stable IDs; migrate old data intentionally rather than silently reinterpreting it under new definitions.
10. **Dependency versions and advisories.** Use a lockfile and security review before bootstrap and upgrades.

## 18. When the Agent Must Stop and Ask

Pause and ask Mark when:

- Multiple valid game rules, map orientations, generation methods, season systems, downtime policies, or authorization models remain possible.
- A proposal changes single-world identity, server timing, database provider, network contract, visibility semantics, or permissions.
- A new library/framework/dependency or infrastructure component is required after assessing existing solutions.
- The task needs a hosting account, production credentials, live deployment, payments, or migration/deletion of user data.
- Documentation materially conflicts with code, load results are inadequate, or unrelated tests fail for unclear reasons.
- Supporting 5,000 concurrent users requires a substantial tradeoff involving budget, consistency, latency, reliability, or architecture that the owner has not approved.

Do not stop to ask about ordinary local variable names or implementation details already covered by approved conventions and behavior.

## 19. Cross-Tool Alignment

`AGENTS.md` is the authoritative AI-agent instruction file. If `CLAUDE.md`, `.github/copilot-instructions.md`, `.cursorrules`, or `.gemini/settings.json` are added, link back to this document and mirror only essential local instructions. `README.md` should explain human setup and workflows, not duplicate the entire agent manual.

## 20. Maintenance and Adoption Checklist

- [ ] Create the repository; confirm product name and initial license.
- [ ] Create `README.md`, package manifests, `pnpm-workspace.yaml`, lockfile, and real scripts.
- [ ] Verify library versions, licensing, security advisories, peer dependencies, and record decisions in ADRs.
- [ ] Bootstrap `apps/web`, `apps/server`, `packages/protocol`, `packages/hex-core`, and `packages/game-data`.
- [ ] Implement and verify setup/build/lint/typecheck/test/CI commands on the actual repository.
- [ ] Implement a minimal vertical slice: login → one world → small hex viewport → authoritative command → atomic persistence → event/update → reconnect.
- [ ] Approve downtime semantics, hex orientation, world generation/expansion, and the initial gameplay mechanics.
- [ ] Document and test backup/restore, crash recovery, and persistent scheduled jobs.
- [ ] Run reproducible 1,000/5,000-CCU load tests with an approved activity profile and hardware budget.
- [ ] Replace **planned** paths, commands, and reference implementations with **actual verified** in-repository examples once code exists.
- [ ] Update this manual whenever architecture, protocols, stack versions, storage, or workflows change.

---

**Instruction to the first coding agent:** implement only an approved, testable vertical slice and its minimum foundation. Do not invent gameplay coefficients or claim the architecture supports thousands of players without measurements. Propose alternatives and obtain owner approval before introducing major technology.