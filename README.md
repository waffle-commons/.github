# `.github` — Waffle-Commons organization profile

This repository hosts the **public organization profile** for [github.com/waffle-commons](https://github.com/waffle-commons) and the shared branding assets (logo, marketing imagery) referenced across the ecosystem.

> 👉 **The canonical landing page is [`profile/README.md`](profile/README.md).** That's the file GitHub renders on the organization profile.

## Contents

| Path | Purpose |
| :--- | :--- |
| [`profile/README.md`](profile/README.md) | The public org profile (rendered on the [`waffle-commons`](https://github.com/waffle-commons) org page): every component with its description and PSR compliance, the **Strict · Secure · Fast** philosophy, project status, and roadmap. |
| [`assets/`](assets/) | Logo and marketing/branding imagery referenced by component READMEs and the documentation site. |

## Current release

🟦 **`0.1.0-beta4`** — *Security & Stability — Release-Candidate readiness groundwork.* See the [profile README](profile/README.md#-project-status) for the full roadmap.

## Continuous integration & quality bar

CI is incremental and parallel by design — it lives in the umbrella repository's `.github/workflows/umbrella-ci.yml`:

- **Change detection → dynamic matrix.** [`dorny/paths-filter`](https://github.com/dorny/paths-filter) inspects the umbrella diff and emits **one matrix leg per modified submodule** — unchanged components are never re-audited.
- **Per-component gates.** Each leg runs `composer install`, `vendor/bin/mago lint`, `vendor/bin/mago analyze`, then PHPUnit 12.5 emitting a Clover report.
- **≥ 95 % line coverage.** Every component writes `clover.xml`, uploaded to Codecov; the coverage status is the ≥ 95 % gate referenced by branch protection.
- **Single aggregating check.** A final `umbrella-ci gate` job collapses the whole matrix into one required status, so branch protection pins exactly one check.

### Zero-baseline Mago policy

No `mago-*-baseline.toml` file exists anywhere in the tree. Every component must satisfy `vendor/bin/mago fmt`, `lint`, `analyze`, and **`guard`** (the dependency-perimeter check that keeps each component depending only on `waffle-commons/contracts` plus `waffle-commons/utils` and its declared PSR packages) with **0 errors, 0 warnings**. The only sanctioned exceptions are documented, reviewable `[analyzer.ignore]` entries inside each `mago.toml`. `fmt` + `lint` + `analyze` + `guard` run in the **pre-commit hook**; together with the **`wfl igor`** worker-safety audit (igor-php 0.7, **0 KO**) they form the local definition of done (`composer mago && composer tests`, `wfl igor`).

## Related

- 📖 [**Documentation**](https://github.com/waffle-commons/documentation) — the Diátaxis-organized framework docs (tutorials, how-to, reference, explanation).
- 🏗 [**Skeleton**](https://github.com/waffle-commons/skeleton) — starter project (`composer create-project waffle-commons/skeleton my-app`).
- 🧩 [**Component template**](https://github.com/waffle-commons/component-template) — scaffold for new components.
- 🔒 [**Member-facing org profile**](https://github.com/waffle-commons/.github-private) — internal architecture deep-dive for core contributors.
