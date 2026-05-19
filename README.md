# value-models

**JSON schemas for B2B value modeling and customer value quantification.**

Part of [The Value Project](https://github.com/The-Value-Project) — open schemas for the full B2B commercial lifecycle.

## Overview

This repository contains two interoperable JSON schemas that together represent how a B2B vendor understands and communicates the economic value of their solution:

| Schema | File | Version | License | Purpose |
|---|---|---|---|---|
| `ValueModel` | `schemas/value_model.json` | 1.0.0 | Apache-2.0 | Product-level value proposition: variables, equations, value drivers, tiers/modules |
| `CustomerVariables` | `schemas/customer_variables.json` | 1.0.0 | Apache-2.0 | Customer-specific instantiation: variable estimates, driver applicability, risk adjustments |
| `BenchmarkClaims` | `schemas/benchmark_claims.json` | 1.0.0 | Apache-2.0 | Vendor-asserted improvement claim benchmarks: median, min, max per claim with evidentiary basis |

Specifications and documentation in `spec/` are licensed under **CC BY 4.0**.

> **Related repo:** [`pricing-models`](https://github.com/The-Value-Project/pricing-models) — PricingModel, DealConfiguration, and InvoiceStatement schemas that these value schemas feed into.

---

## Licensing

| Content | License | SPDX | Full text |
|---|---|---|---|
| JSON schemas (`schemas/`) | Apache License 2.0 | `Apache-2.0` | [LICENSE-CODE](LICENSE-CODE) |
| Specifications and docs (`spec/`, `README.md`, `examples/`) | Creative Commons Attribution 4.0 | `CC-BY-4.0` | [LICENSE-DOCS](LICENSE-DOCS) |

See [LICENSE](LICENSE) for the dual-license overview and attribution guidance.

---

## Schemas

### `ValueModel` — `schemas/value_model.json`

The Canonical Value Model (CVM) defines the full value proposition of a product in a computable, structured form.

**Top-level structure:**

```
ValueModel
├── schema_version            "1.0.0"
├── model_id                  Stable unique identifier (referenced by CustomerVariables.model_id)
├── product_name / product_description
│
├── variables[]               All inputs used in value driver equations
│   ├── name                  JavaScript-safe identifier
│   ├── display_name          Human-readable label
│   ├── description           How to estimate this variable for a customer
│   ├── type                  Money | Numeric | Percentage
│   └── category              Solution Variable | Improvement Claim |
│                             Market Variable | Customer Variable
│
├── dependencies[]            Derived variable relationships
│   ├── variable              Name of the dependent variable
│   └── equation              JavaScript expression computing it from other variables
│
├── value_drivers[]           Economic impact calculations
│   ├── name                  Unique driver name (referenced by CustomerVariables.value_drivers[].name)
│   ├── equation              JavaScript expression → economic impact in currency
│   ├── description           Plain-language explanation of value created
│   ├── customer_criteria     Who this driver applies to — buyer self-assessment
│   ├── variables_used[]      Variable names used in the equation
│   ├── continuity_profile    one_time or recurring (decay rate + time horizon)
│   ├── tiers_or_modules[]    Tiers/modules that unlock this driver
│   ├── key_category_metric   Observable post-sale tracking metric
│   └── key_category_metric_equation  JavaScript expression for the metric
│
└── tiers_and_modules[]       Catalog of subscription tiers and add-on modules
    ├── name                  Must match corresponding name in PricingModel (pricing-models repo)
    ├── module                true = add-on module; false = subscription tier
    ├── description
    └── includes[]            Other tiers/modules included in this one
```

**Variable categories:**

| Category | Description |
|---|---|
| `Solution Variable` | A property of the vendor's solution (e.g., accuracy rate, response time) |
| `Improvement Claim` | The improvement the solution delivers (e.g., % reduction in manual effort) |
| `Market Variable` | A property of the customer's market context (e.g., average deal size) |
| `Customer Variable` | A property of the specific customer (e.g., number of sales reps) |

**Continuity profiles:** `one_time` or `recurring`. Recurring drivers carry `annual_decay_rate` and `time_horizon_years` for multi-year ROI calculations.

**Key category metrics:** `key_category_metric` and `key_category_metric_equation` define the observable outcome tracked post-sale to verify value was realized — separating predicted impact from measurable outcome.

**Cross-reference to pricing:** `tiers_or_modules[]` on each driver links by name to `tiers_and_modules[]`, which corresponds to tier/module names in the PricingModel schema. This is the join between value models and pricing models.

---

### `CustomerVariables` — `schemas/customer_variables.json`

A customer-specific instantiation of a ValueModel.

**Top-level structure:**

```
CustomerVariables
├── schema_version            "1.0.0"
├── customer_id               Stable unique identifier
├── customer                  Customer name
├── model_id                  References ValueModel.model_id
│
├── variables[]               Variable estimates for this customer
│   ├── name                  References ValueModel.variables[].name
│   ├── value                 Estimated numeric value
│   ├── confidence            0–1 confidence score
│   └── triangulation_notes   How this estimate was derived
│
├── value_drivers[]           Per-driver applicability
│   ├── name                  References ValueModel.value_drivers[].name
│   ├── selected              true = included; false = excluded
│   ├── reason                Why selected or excluded
│   └── risk_adjustments
│       ├── execution_risk         0–1 probability value won't be realized
│       └── attribution_percentage 0–100% attributable to this solution
│
└── missing_variables[]       Variables that couldn't be estimated, with reasons
```

**What this computes:** Substituting variable estimates into selected driver equations, applying `execution_risk` and `attribution_percentage`, and summing across selected drivers produces total risk-adjusted attributed value. Comparing to proposed price establishes ROI and payback period.

---

### `BenchmarkClaims` — `schemas/benchmark_claims.json`

A companion to the `ValueModel` that publishes the vendor's asserted improvement claim benchmarks. Every variable with `category: "Improvement Claim"` in a `ValueModel` should have a corresponding entry here.

**Top-level structure:**

```
BenchmarkClaims
├── schema_version            "1.0.0"
├── model_id                  References ValueModel.model_id
├── effective_date            ISO date from which these benchmarks are current
└── claims[]                  One entry per Improvement Claim variable
    ├── variable_name         References ValueModel.variables[].name
    ├── display_name          Human-readable label
    ├── unit                  '%', ISO currency code, or descriptive label
    ├── median                Central or most typical observed value
    ├── min                   Lower bound of the observed range
    ├── max                   Upper bound of the observed range
    ├── basis                 customer_data | third_party_research | internal_testing |
    │                         analyst_estimate | vendor_estimate
    └── notes                 Caveats, scope limitations, or methodology notes (optional)
```

**Why separate from `ValueModel`:** Benchmark data changes on a different cadence from model structure — new customer data or revised research can update benchmarks without touching the `ValueModel`. The separation also allows multiple benchmark datasets (e.g. by segment or region) to reference the same `ValueModel`.

**Relationship to `ValueModel`:** `claims[].variable_name` references `ValueModel.variables[].name`. Only variables with `category: "Improvement Claim"` should have entries.

---

## How the Schemas Relate

```
ValueModel (this repo)
  defines: variables, equations, drivers, tiers/modules, key metrics
      │                                     │
      │                                     │ Improvement Claim variables
      │                                     ▼
      │                             BenchmarkClaims (this repo)
      │                               median / min / max per claim
      │                               evidentiary basis classification
      │
      │  CustomerVariables provides customer-specific inputs,
      │  using BenchmarkClaims medians as defaults for Improvement Claims
      ▼
CustomerVariables (this repo)
  computes: risk-adjusted value per driver
            total quantified value + ROI
            recommended tier (via tiers_or_modules[])
            post-sale tracking metrics
                    │
                    │  Recommended tier_id feeds DealConfiguration
                    ▼
PricingModel + DealConfiguration (pricing-models repo)
  computes: InvoiceStatement
```

The `tiers_or_modules[]` field on each value driver is the **cross-repo join key**: names here must match `PricingModel.products[].tiers[].name` in the [pricing-models](https://github.com/The-Value-Project/pricing-models) repo.

---

## Specifications

### `spec/value-page-spec.md` — [CC BY 4.0](LICENSE-DOCS)

Specifies the format of a conforming **value model page**: a machine-readable Markdown document wrapping a ValueModel JSON instance with front matter metadata, human-readable descriptions, and a pointer to the LLM interpreter reference. Designed to be read by humans and fetched by LLMs.

### `spec/value-model-llm-reference.md` — [CC BY 4.0](LICENSE-DOCS)

LLM interpreter instructions for the ValueModel JSON. Instructs frontier models how to:
- Estimate customer variables and build a CustomerVariables instance
- Evaluate value driver equations and apply continuity profiles
- Apply execution risk and attribution adjustments
- Surface key category metrics for post-sale tracking
- Navigate to the pricing-models repo for deal configuration

This document is linked from every conforming value model page via the `llm_ref` front matter field.

---

## LLM Usage

Both schemas are designed as structured output targets for LLMs.

**Building a `ValueModel`:** Provide the LLM with product documentation and the schema as a structured output target. Key guidance:
- `schema_version` must be `"1.0.0"`
- All variable `name` values must be JavaScript-safe identifiers
- Equations reference only declared `variables[].name` values
- `tiers_or_modules[]` must reference names in `tiers_and_modules[]`
- `key_category_metric_equation` must use only declared variable names
- Percentage-type variables are expressed as whole numbers (30 = 30%)

**Using `BenchmarkClaims`:** When building a `CustomerVariables` instance, provide the LLM with the `BenchmarkClaims` document alongside the `ValueModel`. The LLM should use `median` as the default value for each `Improvement Claim` variable, adjusting toward `min` or `max` based on customer-specific factors, and note the `basis` classification when reporting estimates.

**Building `CustomerVariables`:** Provide the LLM with a ValueModel instance and customer research. Key guidance:
- `model_id` must match the ValueModel instance
- `execution_risk` and `attribution_percentage` must be set for every selected driver
- `missing_variables[]` must document anything that couldn't be estimated

---

## File Structure

```
value-models/
├── LICENSE                           Dual-license overview + attribution
├── LICENSE-CODE                      Apache 2.0 (full text) — applies to schemas/
├── LICENSE-DOCS                      CC BY 4.0 (full text) — applies to spec/, docs
├── README.md                         This file (CC BY 4.0)
├── CONTRIBUTING.md                   Contribution guide (CC BY 4.0)
├── CHANGELOG.md                      Version history (CC BY 4.0)
├── schemas/
│   ├── value_model.json              ValueModel schema v1.0.0 (Apache-2.0)
│   ├── customer_variables.json       CustomerVariables schema v1.0.0 (Apache-2.0)
│   └── benchmark_claims.json         BenchmarkClaims schema v1.0.0 (Apache-2.0)
├── spec/
│   ├── value-page-spec.md            Value model page specification v1.0.0 (CC BY 4.0)
│   └── value-model-llm-reference.md  LLM interpreter reference v1.0.0 (CC BY 4.0)
└── examples/
    ├── value_model_example.json         Worked example ValueModel instance
    ├── customer_variables_example.json  Worked example CustomerVariables instance
    └── benchmark_claims_example.json    Worked example BenchmarkClaims instance
```

---

## Versioning

| Schema | Version | Status |
|---|---|---|
| `ValueModel` | 1.0.0 | Implementation-Ready |
| `CustomerVariables` | 1.0.0 | Implementation-Ready |
| `BenchmarkClaims` | 1.0.0 | Implementation-Ready |

Versions follow `MAJOR.MINOR.PATCH` (semver). Breaking changes (invalidating existing conforming instances) increment MAJOR. Additive backward-compatible changes increment MINOR. The `schema_version` field in each JSON instance must match the schema version.

---

## Contributing

We welcome feedback on schemas, specifications, and implementation experience. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening issues or pull requests.

---

## Related

- [`pricing-models`](https://github.com/The-Value-Project/pricing-models) — PricingModel, DealConfiguration, InvoiceStatement schemas + computation spec
- [The Value Project](https://github.com/The-Value-Project) — org overview and cross-schema relationships
- [ValueIQ](https://valueiq.ai) — the team behind this work

---

*Part of [The Value Project](https://github.com/The-Value-Project) by [ValueIQ](https://valueiq.ai)*
*Schemas: [Apache-2.0](LICENSE-CODE) · Docs: [CC BY 4.0](LICENSE-DOCS)*
