🧇 The Waffle-Commons Ecosystem
=================================

> Strict. Secure. Fast.

🚀 Overview
-----------

**Waffle** is a next-generation PHP framework designed for building secure, high-performance APIs and microservices. It is built on a **Zero Trust** architecture and enforces **Strict Typing** and **Immutability** by design.

Unlike traditional frameworks, Waffle refuses "magic." We prioritize explicit dependency injection, rigorous interface contracts, and the bleeding-edge features of **PHP 8.5+** (Property Hooks, Asymmetric Visibility, Readonly Classes).

### 📚 [**Read the Official Documentation**](https://github.com/waffle-commons/documentation)

_Everything you need to build, secure, and deploy Waffle applications._

💎 Core Philosophy
------------------

1.  **🛡️ Security First:** Security is not an addon; it's the foundation. From Attribute-Based Access Control (ABAC) and a stateless CSRF layer, to native YAML parsing and strict input validation, Waffle assumes the environment is hostile.

2.  **⚡ Bleeding Edge Performance:** Designed to run on **FrankenPHP** (Caddy) in Worker Mode. We leverage the latest PHP optimizations to deliver sub-millisecond response times.

3.  **🧘 No Magic:** No facades. No global state. No implicit logic. Waffle code is predictable, testable, and statically analyzable.

4.  **🧹 Zero-Debt:** Every component passes `vendor/bin/mago fmt`, `vendor/bin/mago lint`, `vendor/bin/mago analyze`, `vendor/bin/mago guard`, and `composer tests` with **0 errors, 0 warnings, 0 deprecations, 0 helps, 0 notes**. No baseline files exist anywhere in the tree.

5.  **🐘 PHP 8.5 Strict:** Property Hooks for DTO validation, Asymmetric Visibility (`public private(set)`) on framework state, typed constants for ecosystem-wide identifiers, `final readonly` for value objects, `#[\Override]` on every interface implementation.


🧩 The Ecosystem Structure
--------------------------

The `waffle-commons` organization is a modular monorepo split into **16 fiercely independent components**. Every component depends only on `waffle-commons/contracts` plus its declared PSR dependencies — `mago guard` enforces this perimeter on every build.

### 🏛 Foundation

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`contracts`**](https://github.com/waffle-commons/contracts) | **The Law.** Interfaces, marker attributes, enums, exception interfaces, ecosystem-wide typed constants. The only shared dependency. | PSR-3, 6, 7, 11, 14, 15, 16, 17 (extension surfaces) |
| [**`utils`**](https://github.com/waffle-commons/utils) | Stateless helpers (`ReflectionTrait`) for tokenizer-based class introspection. | — |

### 🧱 Infrastructure

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`http`**](https://github.com/waffle-commons/http) | Immutable HTTP messages + factories. `GlobalsFactory`, `ResponseEmitter` with 8 KiB chunked streaming. | PSR-7, PSR-17 |
| [**`container`**](https://github.com/waffle-commons/container) | PSR-11 container with reflection autowiring, circular-dependency detection, and worker-mode `reset()`. | PSR-11 |
| [**`config`**](https://github.com/waffle-commons/config) | Native YAML loader (`ext-yaml`, `yaml.decode_php = 0`) with environment overlays and `%env(VAR)%` placeholders. | — |
| [**`log`**](https://github.com/waffle-commons/log) | PSR-3 `StreamLogger` emitting one JSON line per record onto `stdout`/`stderr`. Docker/Kubernetes-native. | PSR-3 |
| [**`cache`**](https://github.com/waffle-commons/cache) | PSR-6 + PSR-16 adapters: `ArrayCache`, `FileCache`, `RedisCache`. Stampede protection, strict key validation. | PSR-6, PSR-16 |
| [**`event-dispatcher`**](https://github.com/waffle-commons/event-dispatcher) | PSR-14 dispatcher + `#[AsEventListener]` attribute discovery, priority ordering, stoppable events. | PSR-14 |

### ⚡ Request pipeline

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`pipeline`**](https://github.com/waffle-commons/pipeline) | PSR-15 `MiddlewareStack` with locking semantics; `TrustedHostMiddleware`, `SecureHeadersMiddleware`, `CoreRoutingMiddleware`. | PSR-15 |
| [**`routing`**](https://github.com/waffle-commons/routing) | Attribute-based routing via `#[Route]` / `#[Argument]`. Tokenizer-based discovery, container-injected arguments. | — |
| [**`security`**](https://github.com/waffle-commons/security) | Hierarchical ABAC (10 levels), `#[Rule]` / `#[Voter]` / `#[RequiresCsrfToken]` attributes, stateless double-submit CSRF, `SecureContainer` decorator. | — |
| [**`error-handler`**](https://github.com/waffle-commons/error-handler) | PSR-15 outermost middleware. RFC 7807 ("Problem Details") JSON renderer, interface-based status-code resolution. | PSR-15, RFC 7807 |

### 🧠 Kernel & runtime

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`waffle`**](https://github.com/waffle-commons/waffle) | The Kernel. `KernelInterface::handle()`, controller dispatch + argument resolution + response conversion, PSR-14 lifecycle events (`RequestReceivedEvent`, `ResponseGeneratedEvent`, `TerminateEvent`). | PSR-7, 11, 14, 15 |
| [**`runtime`**](https://github.com/waffle-commons/runtime) | `WaffleRuntime::loop()` for FrankenPHP worker mode; classic-SAPI single-shot fallback; periodic `gc_collect_cycles()`. | — |

### 🧰 Tooling & developer experience

| Component | Description | PSR |
|:----------|:------------|:----|
| [**`console`**](https://github.com/waffle-commons/console) | Zero-magic CLI runtime. Built-in commands: `cache:clear`, `route:list`, `security:audit`. Typed `ExitCode` + `Verbosity` enums. | — |
| [**`workspace`**](https://github.com/waffle-commons/workspace) | Integration playground. Wires every component via path repositories for end-to-end testing under FrankenPHP. | — |
| [**`skeleton`**](https://github.com/waffle-commons/skeleton) | Starter project. `composer create-project waffle-commons/skeleton my-app` and you have a Beta 0-grade app shell. | — |
| [**`component-template`**](https://github.com/waffle-commons/component-template) | Reusable Beta 0 component scaffold. Cloning + `./configure-component.sh MyName` produces a fully wired new component. | — |


🚦 Project Status
-----------------

> **Current Phase:** 🟦 **Beta 0 (Stabilization & Synchronization)**

The Alpha 6 roadmap has been deprecated. Beta 0 freezes the `waffle-commons/contracts` surface, unifies the ecosystem on the PHP 8.5.5 baseline, enforces architectural perimeters via `mago guard`, and synchronizes every component's tooling. No new features land in Beta 0 — it is the audit and hardening cut.

*   ✅ **Alpha 4:** Pipeline & Hardening (Released Jan 2026)

*   ✅ **Alpha 5:** Observability & Integration (Released Apr 2026)

*   ❌ **Alpha 6:** _Deprecated — superseded by Beta 0._

*   🟦 **Beta 0:** Stabilization, Documentation & Synchronization (In Progress, May 2026)

*   📅 **Beta 1:** EcoShield Gateway prerequisites — `http-client` (PSR-18), catch-all routing, cache adapters at scale — Fall 2026

*   🎯 **v1.0.0:** Production Ready (Winter 2026)


🤝 Contributing
---------------

We maintain a **zero-tolerance policy** for code quality. Before contributing, please ensure your environment meets the following requirements:

*   **PHP:** 8.5.5+ (strictly enforced; Property Hooks, Asymmetric Visibility, typed constants)

*   **Toolchain:** Mago (formatter, linter, analyzer, **guard**) — every check must pass with zero baselines

*   **Testing:** PHPUnit 11+ (>= 95% coverage required for all components)

*   **Architectural perimeter:** Your changes must respect the `[guard.perimeter.rules]` declared in the component's `mago.toml`. Cross-component coupling outside that perimeter is a build break.


Every component carries a Beta 0 [PR template](https://github.com/waffle-commons/component-template/blob/main/.github/PULL_REQUEST_TEMPLATE.md) listing the exact gates a contribution must clear. Please read our [**Contribution Guidelines**](https://github.com/waffle-commons/waffle/blob/main/CONTRIBUTING.md) in the `waffle` repository.

<p align="center">
<small>Maintained by the Waffle Framework Core Team. © 2026.</small>
</p>
