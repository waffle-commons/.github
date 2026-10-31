🧇 The Waffle-Commons Ecosystem
=================================

> Strict. Secure. Fast.

🚀 Overview
-----------

**Waffle** is a next-generation PHP framework for building secure, high-performance APIs and microservices. It is built on a **Zero Trust** architecture and enforces **Strict Typing** and **Immutability** by design.

Unlike traditional frameworks, Waffle refuses "magic." We prioritize explicit dependency injection, rigorous interface contracts, and the bleeding-edge features of **PHP 8.5+** (Property Hooks, Asymmetric Visibility, Readonly Classes).

### 📚 [**Read the Official Documentation**](https://github.com/waffle-commons/documentation)

_Everything you need to build, secure, and deploy Waffle applications._

💎 Core Philosophy
------------------

1.  **🛡️ Security First:** Security is the foundation, not an addon. **Fail-closed ABAC** (a controller action with no `#[Voter]` is denied unless explicitly marked `#[PublicAccess]`), **fail-closed universal authentication** (JWT, OAuth2/OIDC, WebAuthn passkeys, HMAC assertions, API keys, Basic), a **stateless HMAC CSRF** layer bound to the authenticated identity, **rate limiting** at the edge (a token bucket per subject or client IP, shared in Redis), **internal SSRF protection** (DNS-pinned, private-CIDR-blocked outbound calls), timing-safe secret comparison, trusted-host enforcement, baseline secure headers, native YAML parsing, and strict input validation — Waffle assumes the environment is hostile.

2.  **⚡ Built for the Resident Worker:** Designed to run on **FrankenPHP** (Caddy) in Worker Mode. The kernel boots once and stays resident, memory stays bounded, and outbound calls go through a non-blocking client with mandatory timeouts, bounded retries and a circuit breaker. Performance figures are published with their method and environment, and re-measured on the current runtime before they are quoted again.

3.  **🧘 No Magic:** No facades. No global state. No implicit logic. Waffle code is predictable, testable, and statically analyzable.

4.  **🧹 Zero-Debt:** Every component passes `vendor/bin/mago fmt`, `lint`, `analyze`, `guard`, and `composer tests` with **0 errors, 0 warnings**, plus the worker-safety audit (`wfl igor`, **0 KO**). No baseline files exist anywhere in the tree.

5.  **🐘 PHP 8.5 Strict:** Property Hooks for DTO validation, Asymmetric Visibility (`public private(set)`) on framework state, typed constants for ecosystem-wide identifiers, `final readonly` value objects, `#[\Override]` on every interface implementation.


🧩 The Ecosystem Structure
--------------------------

The `waffle-commons` organization is a modular monorepo of **25 fiercely independent framework components**. Every component depends only on `waffle-commons/contracts` (plus `waffle-commons/utils`, the shared foundation — which itself requires only `contracts`) and its declared PSR dependencies — `mago guard` enforces this perimeter on every build.

### 🏛 Foundation

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`contracts`**](https://github.com/waffle-commons/contracts) | **The Law.** Interfaces, marker attributes, enums, exception interfaces, ecosystem-wide typed constants — the only shared dependency. Hosts the canonical `#[Route]` attribute and, since Beta 7, the queue, health, rate-limit, resilience, serializer and OpenAPI contracts. | PSR-3, 6, 7, 11, 14, 15, 16, 17, 18, 20 (extension surfaces) |
| [**`utils`**](https://github.com/waffle-commons/utils) | Stateless tokenizer-based class introspection (`ClassParser`, `AttributeReader`, `ReflectionInspector`), the build-time `DtoShapeReader` shared by the serializer and the OpenAPI generator, and the PSR-20 `SystemClock`. | PSR-20 |

### 🧱 Infrastructure

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`http`**](https://github.com/waffle-commons/http) | Immutable HTTP messages + factories. `GlobalsFactory`, `ResponseEmitter` with 8 KiB chunked streaming, `TrustedProxyClientIpResolver` for the client IP behind trusted proxies. | PSR-7, PSR-17 |
| [**`http-client`**](https://github.com/waffle-commons/http-client) | Non-blocking PSR-18 client (`curl_multi`) for outbound proxying. Bidirectional 8 KiB streaming, SSRF protocol allowlist (HTTP/HTTPS only), mandatory timeout policies, bounded retries for idempotent methods, and a circuit breaker whose state lives in Redis. | PSR-18 |
| [**`container`**](https://github.com/waffle-commons/container) | PSR-11 container with reflection autowiring, AOT compilation (`ContainerCompiler`), circular-dependency detection, locked core services, and worker-mode `reset()`. | PSR-11 |
| [**`config`**](https://github.com/waffle-commons/config) | Native YAML loader (`ext-yaml`, `yaml.decode_php = 0`) with environment overlays. Read-only env map — no process-environment mutation. | — |
| [**`log`**](https://github.com/waffle-commons/log) | PSR-3 `StreamLogger` emitting one JSON line per record onto `stdout`/`stderr`. Docker/Kubernetes-native. | PSR-3 |
| [**`cache`**](https://github.com/waffle-commons/cache) | PSR-6 + PSR-16 adapters: `ArrayCache`, `FileCache`, `RedisCache`. JSON serialization (no `unserialize` gadget surface), stampede protection, strict key validation, a `/readyz` probe. | PSR-6, PSR-16 |
| [**`event-dispatcher`**](https://github.com/waffle-commons/event-dispatcher) | PSR-14 dispatcher + `#[AsEventListener]` discovery, priority ordering, stoppable events. | PSR-14 |
| [**`telemetry`**](https://github.com/waffle-commons/telemetry) | **SDK-Free Metrics & Prometheus Exposition (RFC-005).** Worker-safe `MetricsRegistry` with `ApcuMetricStore` (zero-heap shared metrics), `/waffle-metrics` Prometheus endpoint via `MetricsMiddleware`, the `TracingMiddleware` server span, memory/GC/pool collectors, and dev-only profiler headers. | PSR-15 |
| [**`telemetry-otel`**](https://github.com/waffle-commons/telemetry-otel) | **OpenTelemetry Bridge (RFC-005).** Implements `telemetry` interfaces with OpenTelemetry SDK bindings, exported spans & metrics to OTLP collectors. | — |

### ⚡ Request pipeline

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`pipeline`**](https://github.com/waffle-commons/pipeline) | PSR-15 `MiddlewareStack` with locking semantics; `TrustedHostMiddleware`, `SecureHeadersMiddleware`, `CoreRoutingMiddleware` (with `OPTIONS` preflight auto-answer), and `ProbeMiddleware` (`/healthz` liveness, fail-closed `/readyz` readiness). | PSR-15 |
| [**`routing`**](https://github.com/waffle-commons/routing) | Attribute-based routing via `#[Route]` / `#[Argument]`. AOT trie compilation (`WAFFLE_AOT=1`), HTTP-method filtering & route overloading, `HEAD ⇒ GET` fallback, priority/catch-all, deterministic `Allow` header, worker-safe PCRE cache. | — |
| [**`security`**](https://github.com/waffle-commons/security) | Hierarchical ABAC (10 levels), `#[Voter]` / `#[PublicAccess]` / `#[RequiresCsrfToken]` attributes, fail-closed authorization, stateless HMAC double-submit CSRF, fail-closed CORS, `SecureContainer` decorator, and the token-bucket `RateLimitMiddleware` (`RateLimit-*` headers, `429` + `Retry-After`). | PSR-15 |
| [**`error-handler`**](https://github.com/waffle-commons/error-handler) | PSR-15 outermost middleware. RFC 7807 ("Problem Details") JSON renderer; interface-based status resolution incl. `405 Method Not Allowed` + `Allow` header. | PSR-15, RFC 7807 |
| [**`async`**](https://github.com/waffle-commons/async) | **Fiber Finish-Request Task Deferral (RFC-015).** Runs deferred tasks post-response in isolated PHP `Fiber` boundaries with bounded task budgets (`DEFAULT_BUDGET = 64`). Worker-safe `ResettableInterface`. | — |

### 📨 Production surface (new in Beta 7)

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`queue`**](https://github.com/waffle-commons/queue) | Background jobs on Redis Streams: bounded retries with exponential back-off, a dead-letter store, crash recovery through the pending list. The `queue:work` worker (in `console`) resets the container after each message and drains on SIGTERM. Mail is a contract: a mail is a queue message. | PSR-20 |
| [**`openapi`**](https://github.com/waffle-commons/openapi) | OpenAPI 3.1 generated at build time from `#[Route]` and typed DTOs (`openapi:generate`), refined by `#[OA\Operation]`, `#[OA\Response]` and `#[OA\Property]`; a debug-only Swagger UI at `/_docs`. | PSR-15 |
| [**`serializer`**](https://github.com/waffle-commons/serializer) | `#[Dto]` ↔ JSON at the HTTP boundary: per-DTO normalizers compiled for `WAFFLE_AOT=1` (reflection-free), an interpreted fallback, and `Accept` negotiation. | PSR-15 |
| [**`testing`**](https://github.com/waffle-commons/testing) | Dev-only kernel testing bridge: `WaffleTestCase` sends PSR-7 requests through the real pipeline in-process, with doubles for the clock, the queue, the mailer, the HTTP client and the rate-limiter store. | PSR-18, PSR-20 |

### 🔐 Auth & 🗄 Data

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`auth`**](https://github.com/waffle-commons/auth) | **Universal Authentication Bridge (RFC-021).** All inbound authN — JWT (HS256/RS256 + JWKS), OAuth2/OIDC (PKCE S256), WebAuthn/passkeys (`WebAuthnLibAdapter`), HMAC gateway assertions (`X-Wfl-Assert-User`), API keys, HTTP Basic — plus an outbound `AuthenticatedClient` PSR-18 decorator. Fail-closed, stateless, `hash_equals()` throughout. *AuthN only — authZ stays in `security`.* | PSR-15, PSR-18 |
| [**`data`**](https://github.com/waffle-commons/data) | **Universal Data & Persistence (RFC-022).** Warm `PDOConnectionPool`, a backend-agnostic query AST, parameterized SQL / Firestore / Mongo / key-value / Cassandra / GraphQL compilers, seven stateless CRUD repositories, a property-hook hydrator, reversible SQL migrations (`db:migrate`, `db:rollback`, `db:status`), and the Redis stores behind the rate limiter and the circuit breaker. **No ORM, no identity map, no change tracking.** | — |

### 🧠 Kernel & runtime

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`waffle`**](https://github.com/waffle-commons/waffle) | The Kernel. `KernelInterface::handle()`, controller dispatch + argument resolution, native `#[Dto]` validation → RFC 7807 `422` (through the serializer when one is registered), PSR-14 lifecycle events. | PSR-7, 11, 14, 15 |
| [**`runtime`**](https://github.com/waffle-commons/runtime) | `WaffleRuntime::loop()` for FrankenPHP worker mode; classic-SAPI single-shot fallback; periodic `gc_collect_cycles()`. | — |

### 🧰 Tooling & developer experience

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`console`**](https://github.com/waffle-commons/console) | Zero-magic CLI runtime. Built-in commands include `route:list`, `security:audit`, `db:migrate` / `db:rollback` / `db:status`, the `make:*` scaffolders, the AOT `container:compile` / `route:compile` pair and the `queue:work` worker. Typed `ExitCode` + `Verbosity` enums. | — |
| [**`workspace`**](https://github.com/waffle-commons/workspace) | Integration playground. Wires every component via path repositories for end-to-end testing under FrankenPHP. *(Template app — French-localized.)* | — |
| [**`skeleton`**](https://github.com/waffle-commons/skeleton) | Starter project: `composer create-project waffle-commons/skeleton my-app`. *(Template app — French-localized.)* | — |
| [**`component-template`**](https://github.com/waffle-commons/component-template) | Reusable component scaffold. Clone + `./configure-component.sh MyName` produces a fully wired new component. | — |
| [**`academy`**](https://github.com/waffle-commons/academy) | Hands-on learning app: 50 TDD labs across five levels (L1–L5) with an answer-key tree + solve/verify toggle, plus an enriched FrankenPHP sandbox. *(Template app — French-localized.)* | — |
| [**`ecoshield-gateway`**](https://github.com/waffle-commons/ecoshield-gateway) | FinOps API gateway — the Strangler-Fig dogfood in front of a legacy PHP monolith, built on public Waffle APIs only. Its alpha on Beta 7 adds the rate limiter and the resilient proxy; its soak on `1.0.0-RC1` decides v1 adoption. | — |


🧑‍💻 Local Developer Tooling — `bin/wfl`
----------------------------------------

The umbrella ships a Docker-native host helper, **`wfl`**, so contributors never run PHP on the host:

| Command | What it does |
|:--------|:-------------|
| `wfl init` / `wfl doctor` | Take a fresh clone to a working checkout — submodules, the `docker compose` stack, `composer install` per component, Git hooks, `wfl` on your PATH — or report what is missing, with the fix. |
| `wfl up` / `wfl down` | Bring the container stack up / down (extra args passed through to `docker compose`). |
| `wfl debug` | Activate the 🐛 **debug** PHP profile (Xdebug on, JIT off) and restart the container. |
| `wfl bench` | Activate the 🚀 **bench** PHP profile (Xdebug off, JIT on) and restart the container. |
| `wfl mago [comp]` / `wfl test [comp]` | Run `composer mago` / `composer tests` for a component inside the container. |
| `wfl dod [comp]` | The full definition of done: Mago at zero output, a clean PHPUnit `OK`, coverage ≥ 95 % and above the component's floor, `wfl igor` 0 KO. |
| `wfl igor` / `wfl compare-audit [comp…]` | Run the igor-php worker-state audit (the **0 KO** gate), or the SEC-03 timing-safe-comparison gate over secret/token/HMAC sites. |
| `wfl check:all [--with-tests]` / `wfl monorepo:sync [--fix]` | Parallel `composer mago` across every component; report (or with `--fix`, align) `waffle-commons/*` version constraints. |
| `wfl drain-check` / `wfl rolling-restart` | Prove the graceful drain: `/readyz` flips before SIGTERM, and a rolling restart under k6 load loses no request. |
| `wfl academy:test` / `:solve` / `:reset` / `:verify` / `:serve` | Run the Academy labs TDD suite + progress card, load/clear reference solutions, prove them green, or serve the sandbox. |
| `wfl link` / `wfl unlink` | Wire a local provider component into a consumer's `composer.json` via a path repository. |
| `wfl csrf-init` / `wfl secret-gen` | Generate matching `WAFFLE_SID` + CSRF token for API testing, or a fresh 32-byte application secret. |

Run `wfl help` for the full command list.


🚦 Project Status
-----------------

> **Current Phase:** 🟦 **Beta 7 (Production surface — background jobs, OpenAPI, DTO serialization, a kernel testing bridge, rate limiting, outbound resilience, Kubernetes operability)**

Beta 7 closes the gap between a fast framework and a production API ecosystem: four new components (`queue`, `openapi`, `serializer`, `testing`), a token-bucket rate limiter shared in Redis, outbound timeouts, retries and a circuit breaker, `/healthz` and `/readyz` probes with per-check timeouts, a graceful drain (in the acceptance run, a rolling restart of two replicas under k6 at 100 requests per second lost none of 7 501 requests — a correctness check, not a capacity figure), and reversible migrations. Every roadmap item was accepted on evidence: tests, command output and archived runs. It builds on Beta 6, which stabilized and audited the ecosystem: 17/17 security findings fixed natively with zero suppressions, a complete Diátaxis documentation overhaul, the EcoShield Gateway POC, and the first K6 benchmark harness — hardened in Beta 7 so that a run never overwrites a series and memory is reported as a slope with its 95 % bound.

*   ✅ **Alpha 4:** Pipeline & Hardening — native YAML config, PSR-15 pipeline, RFC 7807 error-handler.

*   ✅ **Alpha 5:** Observability & Integration — PSR-3 log, PSR-14 event-dispatcher, attribute-based security.

*   ✅ **Alpha 6:** Contracts freeze; DTO-validation groundwork.

*   ✅ **Beta 0:** "Zero-Debt" — Mago Purge Protocol complete, new `cache` + `console` components.

*   ✅ **Beta 1:** "EcoShield" — worker-native security hardening, new PSR-18 `http-client`, fail-closed ABAC, stateless CSRF, secure headers.

*   ✅ **Beta 2:** HTTP correctness wave (405 / `OPTIONS` / `HEAD` / `Allow`) + developer experience + cognitive tooling.

*   ✅ **Beta 3:** Data & Persistence (`data`, RFC-022) + Universal Auth Bridge (`auth`, RFC-021) + `wfl igor` worker-safety tooling.

*   ✅ **Beta 4:** Security & stability hardening (SSRF, identity-bound CSRF, timing-safe comparisons) + DX tooling + the monorepo Academy.

*   ✅ **Beta 5:** Enterprise Runtime — AOT container/router compiler, DB connection pooling, Fiber `async` finish-request deferral, contract-first `telemetry` & `telemetry-otel` bridge, WebAuthn/passkeys.

*   ✅ **Beta 6:** Stabilization, Audit & Performance — multi-engine security audit remediation (17/17 fixed natively), Diátaxis docs overhaul, EcoShield Gateway POC, K6 benchmark harness.

*   🟦 **Beta 7** (current, November 2026): Production surface — `queue`, `openapi`, `serializer`, `testing`, rate limiting and outbound resilience (NET), Kubernetes operability (OPS).

*   🎯 **v1.0.0:** Production Ready (Gold — late January 2027), via Beta 8 (API freeze & consolidation, December 2026) and `1.0.0-RC1` (December 2026), certified by a soak of at least four weeks on the EcoShield Gateway.


🤝 Contributing
---------------

We maintain a **zero-tolerance policy** for code quality. Before contributing, please ensure your environment meets the following:

*   **PHP:** 8.5+ (strictly enforced; Property Hooks, Asymmetric Visibility, typed constants).

*   **Toolchain:** Mago (formatter, linter, analyzer, **guard**) + the **`wfl igor`** worker-safety audit (igor-php 0.9.7, pinned, **0 KO**) — every check must pass with zero baselines.

*   **Testing:** PHPUnit 12.5+ (≥ 95 % coverage required for all components, and a clean `OK` verdict — no skipped, incomplete or risky tests).

*   **Architectural perimeter:** Your changes must respect the `[guard.perimeter.rules]` declared in the component's `mago.toml`. Cross-component coupling outside that perimeter is a build break.

Every framework component ships a Keep-a-Changelog `CHANGELOG.md` and a PR template listing the exact gates a contribution must clear. Every component runs the same CI workflow — Mago, PHPUnit against a Redis service, the igor audit, secret scanning and a CycloneDX SBOM — and the umbrella collapses them behind a single required `umbrella-ci gate`. Please read our [**Contribution Guidelines**](https://github.com/waffle-commons/waffle/blob/main/CONTRIBUTING.md) in the `waffle` repository.

***

> [![Discord](https://img.shields.io/discord/755288001592033391?color=7289da&label=discord&logo=discord&style=for-the-badge)](https://discord.gg/eKgywnfXr2)<br />
> *Join the core team and contributors on Discord to shape the future of cloud-native PHP.*

***

<small>Maintained by the Waffle Framework Core Team. © 2026.</small>
