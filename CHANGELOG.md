# Changelog — value-models

> License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) · Part of [The Value Project](https://github.com/The-Value-Project/value-models)

All notable changes to the `value-models` schemas and specifications are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/). Versions follow `MAJOR.MINOR.PATCH` (semver). Breaking changes increment MAJOR.

---

## [Unreleased]

*No unreleased changes.*

---

## [1.0.0] — 2026-05-07

### Added — Repository structure

- `LICENSE` — dual-license overview (schemas: Apache-2.0; docs/specs: CC BY 4.0)
- `LICENSE-CODE` — Apache License 2.0 full text (applies to `schemas/`)
- `LICENSE-DOCS` — Creative Commons Attribution 4.0 full text (applies to `spec/`, `examples/`, docs)
- `CONTRIBUTING.md` — contribution guide with license terms for contributors
- `CHANGELOG.md` — this file

### Added — `ValueModel` schema v1.0.0 (`schemas/value_model.json`)

License: Apache-2.0

- `schema_version` field (`const: "1.0.0"`) — required on all conforming instances
- `model_id` — stable unique identifier, referenced by `CustomerVariables.model_id`
- `$id`, `x-version`, `x-license`, `x-related-schemas` — license header and cross-repo references embedded in schema
- `variables[]` — full variable catalog: `name` (JS-safe), `display_name`, `description`, `type` (Money | Numeric | Percentage), `category` (Solution Variable | Improvement Claim | Market Variable | Customer Variable), optional `default_value`
- `dependencies[]` — derived variable relationships with JavaScript `equation`
- `value_drivers[]` — economic impact calculations with `equation`, `description`, `variables_used[]`, `continuity_profile` (one_time | recurring with `annual_decay_rate` and `time_horizon_years`), `tiers_or_modules[]`, `key_category_metric`, `key_category_metric_equation`
- `tiers_and_modules[]` — catalog of tiers and modules; names correspond to `PricingModel.tiers[]` in [`pricing-models`](https://github.com/The-Value-Project/pricing-models) repo

Cross-repo join: `tiers_or_modules[]` on value drivers references tier/module names in the pricing-models repo's `PricingModel` schema.

### Added — `CustomerVariables` schema v1.0.0 (`schemas/customer_variables.json`)

License: Apache-2.0

- `schema_version` field (`const: "1.0.0"`)
- `customer_id` — stable unique identifier
- `model_id` — references `ValueModel.model_id`
- `$id`, `x-version`, `x-license`, `x-related-schemas` — license header and cross-repo references
- `variables[]` — per-variable estimates: `value`, `confidence` (0–1), `triangulation_notes`, `source_url`
- `value_drivers[]` — per-driver applicability: `selected`, `reason`, `risk_adjustments` (`execution_risk` 0–1, `attribution_percentage` 0–100)
- `missing_variables[]` — structured record of variables that couldn't be estimated

### Added — Specifications

License: CC BY 4.0

- `spec/value-page-spec.md` v1.0.0 — specification for machine-readable value model pages: YAML front matter format, human-readable description structure (variables table, per-driver subsections with equations and key metrics), ValueModel JSON block, `llm_ref` pointer requirement
- `spec/value-model-llm-reference.md` v1.0.0 — LLM interpreter reference: instructions for estimating variables, evaluating driver equations, applying continuity profiles, applying execution risk and attribution, surfacing key category metrics, producing a CustomerVariables instance, navigating to pricing-models for deal configuration

Both spec files include license front matter (YAML) and inline license notices.

---

*Changelog for [value-models](https://github.com/The-Value-Project/value-models) · Part of [The Value Project](https://github.com/The-Value-Project)*
