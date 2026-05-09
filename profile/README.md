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

1.  **🛡️ Security First:** Security is not an addon; it's the foundation. From Attribute-Based Access Control (ABAC) to native YAML parsing and strict input validation, Waffle assumes the environment is hostile.

2.  **⚡ Bleeding Edge Performance:** Designed to run on **FrankenPHP** (Caddy) in Worker Mode. We leverage the latest PHP optimizations to deliver sub-millisecond response times.

3.  **🧘 No Magic:** No facades. No global state. No implicit logic. Waffle code is predictable, testable, and statically analyzable.


🧩 The Ecosystem Structure
--------------------------

The waffle-commons organization is a modular monorepo split into decoupled components. You can use them standalone or together via the Skeleton.

| Component                                                      | Description | PSR Compliance |
|:---------------------------------------------------------------| :--- | :--- |
| [**`contracts`**](https://github.com/waffle-commons/contracts) | **The Law.** Interfaces that define the behavior of the entire system. | All applicable |
| [**`waffle`**](https://github.com/waffle-commons/waffle)       | **The Kernel.** The agnostic core that orchestrates the request lifecycle. | - |
| [**`http`**](https://github.com/waffle-commons/http)           | Immutable HTTP Message Factories and Response Emitters. | PSR-7, PSR-17 |
| [**`pipeline`**](https://github.com/waffle-commons/pipeline)   | Middleware stack and request handler implementation. | PSR-15 |
| [**`routing`**](https://github.com/waffle-commons/routing)     | High-speed, attribute-based routing system (`#[Route]`). | - |
| [**`security`**](https://github.com/waffle-commons/security)   | Hierarchical ABAC security rules and container hardening. | - |
| [**`config`**](https://github.com/waffle-commons/config)       | Secure configuration loader using native YAML PECL extension. | - |
| [**`runtime`**](https://github.com/waffle-commons/runtime)     | Bootstrapping logic optimized for FrankenPHP and long-running processes. | - |
| [**`skeleton`**](https://github.com/waffle-commons/skeleton)   | The starting point for new projects. Pre-configured and secure by default. | - |

🚦 Project Status
-----------------

> **Current Phase:** 🚧 **Alpha 5 (Observability & Defense)**

We are currently stabilizing the observability layer (Logs & Events) and finalizing the Security Middleware bridge.

*   ✅ **Alpha 4:** Pipeline & Hardening (Released Jan 2026)

*   ✅ **Alpha 5:** Observability & Integration (Released Apr 2026)

*   🚧 **Alpha 6:** Interaction & Integrity (In Progress)

*   📅 **Beta 1:** Interaction & Integrity (Coming Fall 2026)

*   🎯 **v1.0.0:** Production Ready (Coming Winter 2026)


🤝 Contributing
---------------

We maintain a **zero-tolerance policy** for code quality.Before contributing, please ensure your environment meets the following requirements:

*   **PHP:** 8.5+ (Strictly enforced)

*   **Toolchain:** Mago (Rust-based linter/analyzer)

*   **Testing:** PHPUnit 12+ (+95% Coverage required for Core components)


Please read our [**Contribution Guidelines**](https://github.com/waffle-commons/waffle/blob/main/CONTRIBUTING.md) in the `waffle` repository.

<p align="center">
<small>Maintained by the Waffle Framework Core Team. © 2026.</small>
</p>
