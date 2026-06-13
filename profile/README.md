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

1.  **🛡️ Security First:** Security is the foundation, not an addon. **Fail-closed ABAC** (a controller action with no `#[Voter]` is denied unless explicitly marked `#[PublicAccess]`), **fail-closed universal authentication** (JWT, OAuth2/OIDC, HMAC assertions, API keys, Basic), a **stateless HMAC CSRF** layer bound to the authenticated identity, **internal SSRF protection** (DNS-pinned, private-CIDR-blocked outbound calls), timing-safe secret comparison, trusted-host enforcement, baseline secure headers, native YAML parsing, and strict input validation — Waffle assumes the environment is hostile.

2.  **⚡ Bleeding Edge Performance:** Designed to run on **FrankenPHP** (Caddy) in Worker Mode. Long-lived workers, bounded memory, and a non-blocking outbound HTTP client deliver sub-millisecond response times.

3.  **🧘 No Magic:** No facades. No global state. No implicit logic. Waffle code is predictable, testable, and statically analyzable.

4.  **🧹 Zero-Debt:** Every component passes `vendor/bin/mago fmt`, `lint`, `analyze`, `guard`, and `composer tests` with **0 errors, 0 warnings**. No baseline files exist anywhere in the tree.

5.  **🐘 PHP 8.5 Strict:** Property Hooks for DTO validation, Asymmetric Visibility (`public private(set)`) on framework state, typed constants for ecosystem-wide identifiers, `final readonly` value objects, `#[\Override]` on every interface implementation.


🧩 The Ecosystem Structure
--------------------------

The `waffle-commons` organization is a modular monorepo of **18 fiercely independent framework components**. Every component depends only on `waffle-commons/contracts` (plus `waffle-commons/utils`, the shared foundation — which itself requires only `contracts`) and its declared PSR dependencies — `mago guard` enforces this perimeter on every build.

### 🏛 Foundation

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`contracts`**](https://github.com/waffle-commons/contracts) | **The Law.** Interfaces, marker attributes, enums, exception interfaces, ecosystem-wide typed constants — the only shared dependency. Hosts the canonical `#[Route]` attribute (relocated here in Beta 2) and the HTTP-method `Constant`s. | PSR-3, 6, 7, 11, 14, 15, 16, 17, 18 (extension surfaces) |
| [**`utils`**](https://github.com/waffle-commons/utils) | Stateless tokenizer-based class introspection (`ClassParser`, `AttributeReader`, `ReflectionInspector`). | — |

### 🧱 Infrastructure

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`http`**](https://github.com/waffle-commons/http) | Immutable HTTP messages + factories. `GlobalsFactory`, `ResponseEmitter` with 8 KiB chunked streaming. | PSR-7, PSR-17 |
| [**`http-client`**](https://github.com/waffle-commons/http-client) | Non-blocking PSR-18 client (`curl_multi`) for outbound proxying. Bidirectional 8 KiB streaming, SSRF protocol allowlist (HTTP/HTTPS only), hardcoded 1 s / 10 s timeouts. | PSR-18 |
| [**`container`**](https://github.com/waffle-commons/container) | PSR-11 container with reflection autowiring, circular-dependency detection, locked core services, and worker-mode `reset()`. | PSR-11 |
| [**`config`**](https://github.com/waffle-commons/config) | Native YAML loader (`ext-yaml`, `yaml.decode_php = 0`) with environment overlays. Read-only env map — no process-environment mutation. | — |
| [**`log`**](https://github.com/waffle-commons/log) | PSR-3 `StreamLogger` emitting one JSON line per record onto `stdout`/`stderr`. Docker/Kubernetes-native. | PSR-3 |
| [**`cache`**](https://github.com/waffle-commons/cache) | PSR-6 + PSR-16 adapters: `ArrayCache`, `FileCache`, `RedisCache`. JSON serialization (no `unserialize` gadget surface), stampede protection, strict key validation. | PSR-6, PSR-16 |
| [**`event-dispatcher`**](https://github.com/waffle-commons/event-dispatcher) | PSR-14 dispatcher + `#[AsEventListener]` discovery, priority ordering, stoppable events. | PSR-14 |

### ⚡ Request pipeline

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`pipeline`**](https://github.com/waffle-commons/pipeline) | PSR-15 `MiddlewareStack` with locking semantics; `TrustedHostMiddleware`, `SecureHeadersMiddleware`, `CoreRoutingMiddleware` (with `OPTIONS` preflight auto-answer). | PSR-15 |
| [**`routing`**](https://github.com/waffle-commons/routing) | Attribute-based routing via `#[Route]` / `#[Argument]`. HTTP-method filtering & route overloading, `HEAD ⇒ GET` fallback, priority/catch-all, deterministic `Allow` header, worker-safe PCRE cache. | — |
| [**`security`**](https://github.com/waffle-commons/security) | Hierarchical ABAC (10 levels), `#[Rule]` / `#[Voter]` / `#[PublicAccess]` / `#[RequiresCsrfToken]` attributes, fail-closed authorization, stateless HMAC double-submit CSRF, `SecureContainer` decorator. | — |
| [**`error-handler`**](https://github.com/waffle-commons/error-handler) | PSR-15 outermost middleware. RFC 7807 ("Problem Details") JSON renderer; interface-based status resolution incl. `405 Method Not Allowed` + `Allow` header. | PSR-15, RFC 7807 |

### 🔐 Auth & 🗄 Data

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`auth`**](https://github.com/waffle-commons/auth) | **Universal Authentication Bridge (RFC-021).** All inbound authN — JWT (HS256/RS256 + JWKS), OAuth2/OIDC (PKCE S256), HMAC gateway assertions (`X-Wfl-Assert-User`), API keys, HTTP Basic — plus an outbound `AuthenticatedClient` PSR-18 decorator. Fail-closed, stateless, `hash_equals()` throughout. *AuthN only — authZ stays in `security`.* | PSR-15, PSR-18 |
| [**`data`**](https://github.com/waffle-commons/data) | **Universal Data & Persistence (RFC-022).** Warm `PDOConnectionPool`, a backend-agnostic query AST, parameterized SQL / Firestore / Mongo / key-value / Cassandra / GraphQL compilers, seven stateless CRUD repositories, a property-hook hydrator, and a stateless SQL migration runner. **No ORM, no identity map, no change tracking.** | — |

### 🧠 Kernel & runtime

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`waffle`**](https://github.com/waffle-commons/waffle) | The Kernel. `KernelInterface::handle()`, controller dispatch + argument resolution, native `#[Dto]` validation → RFC 7807 `422`, PSR-14 lifecycle events. | PSR-7, 11, 14, 15 |
| [**`runtime`**](https://github.com/waffle-commons/runtime) | `WaffleRuntime::loop()` for FrankenPHP worker mode; classic-SAPI single-shot fallback; periodic `gc_collect_cycles()`. | — |

### 🧰 Tooling & developer experience

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`console`**](https://github.com/waffle-commons/console) | Zero-magic CLI runtime. Built-in commands: `cache:clear`, `route:list`, `security:audit`. Typed `ExitCode` + `Verbosity` enums. | — |
| [**`workspace`**](https://github.com/waffle-commons/workspace) | Integration playground. Wires every component via path repositories for end-to-end testing under FrankenPHP. *(Template app — French-localized.)* | — |
| [**`skeleton`**](https://github.com/waffle-commons/skeleton) | Starter project: `composer create-project waffle-commons/skeleton my-app`. *(Template app — French-localized.)* | — |
| [**`component-template`**](https://github.com/waffle-commons/component-template) | Reusable component scaffold. Clone + `./configure-component.sh MyName` produces a fully wired new component. | — |
| [**`academy`**](https://github.com/waffle-commons/academy) | Hands-on learning app: 50 TDD labs across five levels (L1–L5) with an answer-key tree + solve/verify toggle, plus an enriched FrankenPHP sandbox. *(Template app — French-localized.)* | — |


🧑‍💻 Local Developer Tooling — `bin/wfl`
----------------------------------------

The umbrella ships a Docker-native host helper, **`wfl`**, so contributors never run PHP on the host:

| Command | What it does |
|:--------|:-------------|
| `wfl init` | Inits submodules, boots the `docker compose` stack, installs the Git hooks, and symlinks `wfl` into `~/.local/bin`. |
| `wfl up` / `wfl down` | Bring the container stack up / down (extra args passed through to `docker compose`). |
| `wfl debug` | Activate the 🐛 **debug** PHP profile (Xdebug on, JIT off) and restart the container. |
| `wfl bench` | Activate the 🚀 **bench** PHP profile (Xdebug off, JIT on) and restart the container. |
| `wfl mago [comp]` / `wfl test [comp]` | Run `composer mago` / `composer tests` for a component inside the container. |
| `wfl igor` / `wfl compare-audit [comp…]` | Run the igor-php worker-state audit (the **0 KO** gate), or the SEC-03 timing-safe-comparison gate over secret/token/HMAC sites. |
| `wfl check:all [--with-tests]` / `wfl monorepo:sync [--fix]` | Parallel `composer mago` across every component; report (or with `--fix`, align) `waffle-commons/*` version constraints. |
| `wfl academy:test` / `:solve` / `:reset` / `:verify` / `:serve` | Run the Academy labs TDD suite + progress card, load/clear reference solutions, prove them green, or serve the sandbox. |
| `wfl link` / `wfl unlink` | Wire a local provider component into a consumer's `composer.json` via a path repository. |
| `wfl csrf-init` / `wfl secret-gen` | Generate matching `WAFFLE_SID` + CSRF token for API testing, or a fresh 32-byte application secret. |

Run `wfl help` for the full command list.


🚦 Project Status
-----------------

> **Current Phase:** 🟦 **Beta 4 (Security & Stability — Release-Candidate readiness groundwork)**

Beta 4 hardens the framework toward RC readiness: **session-ID rotation + identity-bound CSRF** (SEC-01), **internal SSRF protection** with DNS-pinning and private-CIDR blocking (SEC-02), a **timing-safe comparison gate** for secret/token/HMAC sites (SEC-03, `wfl compare-audit`), architectural-stability and worker-mode-diagnostics passes, developer-experience tooling (`wfl check:all`, `wfl monorepo:sync`, Git hooks), and the new monorepo **Academy** (50 TDD labs). It builds on Beta 3, which shipped the `data` (RFC-022) and `auth` (RFC-021) components plus the `wfl igor` worker-safety gate.

*   ✅ **Alpha 4:** Pipeline & Hardening — native YAML config, PSR-15 pipeline, RFC 7807 error-handler.

*   ✅ **Alpha 5:** Observability & Integration — PSR-3 log, PSR-14 event-dispatcher, attribute-based security.

*   ✅ **Alpha 6:** Contracts freeze; DTO-validation groundwork.

*   ✅ **Beta 0:** "Zero-Debt" — Mago Purge Protocol complete, new `cache` + `console` components.

*   ✅ **Beta 1:** "EcoShield" — worker-native security hardening, new PSR-18 `http-client`, fail-closed ABAC, stateless CSRF, secure headers.

*   ✅ **Beta 2:** HTTP correctness wave (405 / `OPTIONS` / `HEAD` / `Allow`) + developer experience + cognitive tooling.

*   ✅ **Beta 3:** Data & Persistence (`data`, RFC-022) + Universal Auth Bridge (`auth`, RFC-021) + `wfl igor` worker-safety tooling.

*   🟦 **Beta 4** (current): Security & stability hardening (SSRF, identity-bound CSRF, timing-safe comparisons) + DX tooling + the monorepo Academy.

*   🎯 **v1.0.0:** Production Ready (Gold — April 2027), via Beta 5 (AOT · pooling · telemetry), Beta 6 (production surface — `queue`, OpenAPI, serializer, testing), Beta 7 (API freeze), and `1.0.0-RC1`.


🤝 Contributing
---------------

We maintain a **zero-tolerance policy** for code quality. Before contributing, please ensure your environment meets the following:

*   **PHP:** 8.5+ (strictly enforced; Property Hooks, Asymmetric Visibility, typed constants).

*   **Toolchain:** Mago (formatter, linter, analyzer, **guard**) + the **`wfl igor`** worker-safety audit (igor-php 0.7, **0 KO**) — every check must pass with zero baselines.

*   **Testing:** PHPUnit 12.5+ (≥ 95 % coverage required for all components).

*   **Architectural perimeter:** Your changes must respect the `[guard.perimeter.rules]` declared in the component's `mago.toml`. Cross-component coupling outside that perimeter is a build break.

Every framework component ships a Keep-a-Changelog `CHANGELOG.md` and a PR template listing the exact gates a contribution must clear. CI runs an incremental, per-component matrix (only changed submodules are audited) behind a single required `umbrella-ci gate`. Please read our [**Contribution Guidelines**](https://github.com/waffle-commons/waffle/blob/main/CONTRIBUTING.md) in the `waffle` repository.

***

> [![Discord](https://img.shields.io/discord/755288001592033391?color=7289da&label=discord&logo=discord&style=for-the-badge)](https://discord.gg/eKgywnfXr2)<br />
> *Join the core team and contributors on Discord to shape the future of cloud-native PHP.*

***

<small>Maintained by the Waffle Framework Core Team. © 2026.</small>
