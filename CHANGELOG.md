# Changelog — value-models

> License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) · Part of [The Value Project](https://github.com/The-Value-Project/value-models)

All notable changes to the `value-models` schemas and specifications are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/). Versions follow `MAJOR.MINOR.PATCH` (semver). Breaking changes increment MAJOR.

---

## [Unreleased]

*No unreleased changes.*

---

## [1.2.0] — 2026-05-29

### Changed — `spec/value-page-spec.md` v1.1.0 → v1.2.0

License: CC BY 4.0

- **Value Drivers section (4c)** — replaced format-mandating table with a format-agnostic content requirements table. Publishers may now use any presentation format (table, list, card, structured prose). Six required fields per driver entry are specified by source field and buyer information need rather than by layout.
- **`customer_criteria` rendering** — "Applies to customers who" field must now be rewritten for a buyer audience rather than reproduced verbatim from the JSON. Required: lead with a self-qualification question or condition, state the condition once in plain language, omit seller process instructions ("a seller can confirm by asking..."), maximum 2–3 sentences. Previously: verbatim reproduction of the `customer_criteria` field.
- **Tiers and Modules section (4e)** — replaced format-mandating table with a format-agnostic content requirements table. Same rationale as Value Drivers.
- **Section heading** — "BenchmarkClaims JSON Block" renamed to "Improvement Claim Benchmarks JSON Block" for consistency with the preceding table section heading.
- **Template** — Value Drivers and Tiers and Modules template entries updated to format-agnostic placeholders.
- **Conformance checklist** — Value Drivers check updated to require all six content fields and buyer-audience rewrite of `customer_criteria`.

### Added — `spec/value-page-spec.md` v1.2.0

- **Category key** — inline legend added below the Variables table defining the four variable category values (Customer Variable, Market Variable, Improvement Claim, Solution Variable) in plain buyer language.
- **Basis key** — inline legend added below the Improvement Claim Benchmarks table defining the five basis values and explaining how a buyer should weight each when evaluating claims.
- **`benchmark_claims_ref` omit-if-unset rule** — front matter note clarified: if no canonical URL has been set, omit the field entirely. Do not write a placeholder string — an LLM reading the page will attempt to fetch whatever value appears there.

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

### Added — `BenchmarkClaims` schema v1.0.0 (`schemas/benchmark_claims.json`)

License: Apache-2.0

- `schema_version` field (`const: "1.0.0"`)
- `model_id` — references `ValueModel.model_id`
- `effective_date` — ISO date from which benchmarks are current
- `$id`, `x-version`, `x-license`, `x-related-schemas` — license header and cross-repo references
- `claims[]` — one entry per `Improvement Claim` variable in the referenced `ValueModel`:
  - `variable_name` — references `ValueModel.variables[].name` (category must be `Improvement Claim`)
  - `display_name` — human-readable label matching the ValueModel variable
  - `unit` — `%`, ISO 4217 currency code, or descriptive label
  - `median` — central or most typical observed value
  - `min` — lower bound of the observed or researched range
  - `max` — upper bound of the observed or researched range
  - `basis` — evidentiary classification: `customer_data` | `third_party_research` | `internal_testing` | `analyst_estimate` | `vendor_estimate`
  - `notes` — optional caveats, scope limitations, or methodology notes

### Added — Specifications

License: CC BY 4.0

- `spec/value-page-spec.md` v1.0.0 — specification for machine-readable value model pages: YAML front matter format, human-readable description structure (variables table, Improvement Claim benchmarks table with median/min/max/basis, per-driver subsections with equations and key metrics), BenchmarkClaims JSON block, ValueModel JSON block, `llm_ref` and `benchmark_claims_ref` pointer requirements
- `spec/value-model-llm-reference.md` v1.0.0 — LLM interpreter reference: instructions for estimating variables (including how to use BenchmarkClaims median/min/max and weight by basis), evaluating driver equations, applying continuity profiles, applying execution risk and attribution, surfacing key category metrics, producing a CustomerVariables instance, navigating to pricing-models for deal configuration

Both spec files include license front matter (YAML) and inline license notices.

---

*Changelog for [value-models](https://github.com/The-Value-Project/value-models) · Part of [The Value Project](https://github.com/The-Value-Project)*
