# value-models

**JSON schemas for B2B value modeling and customer value quantification.**

Part of [The Value Project](https://github.com/the-value-project) — open schemas for the full B2B commercial lifecycle.

---

## Overview

This repository contains two interoperable JSON schemas that together represent how a B2B vendor understands and communicates the economic value of their solution:

- **`ValueModel`** — the canonical product-level model of what a solution is worth and to whom: variables, equations, value drivers, and tiers/modules
- **`CustomerVariables`** — a customer-specific instantiation: variable estimates with evidence sources, value driver applicability, and risk adjustments (execution risk + attribution percentage)

These schemas are the foundation of value-based selling. They are designed to be produced by LLMs (using structured output / tool use), consumed by sales enablement tools, and linked to pricing models to recommend appropriate deal configurations.

---

## Schemas

### `ValueModel` — `schemas/value_model.json`

The Canonical Value Model (CVM) defines the full value proposition of a product in a computable, structured form.

**Top-level structure:**

```
ValueModel
├── variables[]           All variables used in value driver equations
│   ├── name              JavaScript-safe identifier
│   ├── display_name      Human-readable label
│   ├── description       How to determine this variable's value
│   ├── type              Money | Numeric | Percentage
│   └── category          Solution Variable | Improvement Claim | Market Variable | Customer Variable
│
├── dependencies[]        Derived variable relationships
│   ├── variable          Name of the dependent variable
│   └── equation          JavaScript expression computing it from other variables
│
├── value_drivers[]       Economic impact calculations for each driver
│   ├── name / number / category / subcategory
│   ├── equation          JavaScript expression → economic impact in currency
│   ├── description       What this driver does
│   ├── driver_impact     Narrative of the impact pathway (no numbers)
│   ├── variables_used[]  Which variables appear in the equation
│   ├── continuity_profile  one_time or recurring, with decay and time horizon
│   ├── customer_criteria   Which customers this driver applies to
│   ├── competitive_positioning
│   ├── key_category_metric + key_category_metric_equation
│   └── tiers_or_modules[]  Which product tiers/modules enable this driver
│
└── tiers_and_modules[]   Catalog of subscription tiers and add-on modules
    ├── name / module (boolean) / description
    └── includes[]        Other tiers/modules included in this one
```

**Variable categories:**

| Category | Description |
|----------|-------------|
| `Solution Variable` | A property of the vendor's solution (e.g., accuracy rate, response time) |
| `Improvement Claim` | The improvement the solution delivers (e.g., % reduction in manual effort) |
| `Market Variable` | A property of the customer's market context (e.g., average deal size) |
| `Customer Variable` | A property of the specific customer (e.g., number of sales reps) |

**Value driver continuity profiles:**

Each driver declares whether it provides a `one_time` or `recurring` benefit, and if recurring, its `annual_decay_rate`, `time_horizon_years`, and optionally a `gross_profit_change_rate`. This enables multi-year value modeling.

**Relation to pricing:** The `tiers_or_modules` field on each value driver links directly to the `tiers_and_modules` catalog, allowing a value model to answer: *"Which tier does a customer need to unlock this driver?"*

---

### `CustomerVariables` — `schemas/customer_variables.json`

A customer-specific instantiation of a value model. This schema captures the minimum required to quantify economic value for a specific prospect: estimates for each model variable, applicability and risk adjustments for each driver, and an indexed evidence registry that makes every estimate traceable.

**Top-level structure:**

```
CustomerVariables
├── customer              Customer name
│
├── variables[]           Variable value estimates — the numeric inputs to the value model equations
│   ├── name              References ValueModel.variables[].name
│   ├── value             Estimated numeric value for this customer
│   ├── confidence        0–1 confidence score for this estimate
│   ├── sources[]         Integer IDs referencing entries in sources_summary
│   └── triangulation_notes  How this estimate was derived or cross-checked
│
├── value_drivers[]       Per-driver applicability — which model drivers matter for this customer
│   ├── name              References ValueModel.value_drivers[].name
│   ├── selected          true if this driver applies; false if excluded
│   ├── reason            Why this driver was selected or excluded
│   └── risk_adjustments
│       ├── execution_risk         0–1 probability that this value won't be realized
│       ├── attribution_percentage  0–100% of value attributable to this solution
│       ├── justification          (optional) narrative explaining these parameters
│       └── sources[]              (optional) evidence IDs from sources_summary
│
├── sources_summary[]     Indexed evidence registry — cited by variables and risk_adjustments
│   ├── id                Integer key referenced by sources[] arrays
│   ├── title / url
│   ├── variables_supported[]  Variable names this source supports
│   └── notes             What the source says and how it was used
│
└── missing_variables[]   (optional) Variables that couldn't be estimated, with reasons
```

**What this schema computes:** Given a `ValueModel` and a `CustomerVariables` instance, a value tool can substitute variable estimates into each selected driver's equation, apply `execution_risk` and `attribution_percentage` to arrive at risk-adjusted attributed value per driver, and sum across selected drivers to produce a total quantified value. Comparing that total to the proposed price establishes ROI and payback period.

The `sources_summary` registry — with integer IDs cited by both `variables[].sources` and `value_drivers[].risk_adjustments.sources` — makes every figure traceable to evidence, which is important for both internal deal reviews and customer-facing value conversations.

**Extensibility:** The standard schema uses `additionalProperties: true` at the top level and within each item. Implementations are free to add fields — such as market fit assessments, pain point analysis, technical context, or synthesized value propositions — without breaking conformance with the standard. The core variable/driver/sources structure must be preserved.

**Relationship to `ValueModel`:** `variables[].name` references `ValueModel.variables[].name`; `value_drivers[].name` references `ValueModel.value_drivers[].name`. The `ValueModel` provides the structure and equations; `CustomerVariables` provides the customer-specific inputs.

---

## How They Work Together

```
ValueModel
  defines: variables, equations, drivers, tiers
      │
      │  CustomerVariables provides estimates for each variable
      │  and selects + risk-adjusts applicable drivers
      ▼
CustomerVariables
  computes (via engine): risk-adjusted value per driver
                         total quantified value
                         ROI vs. proposed price
                         recommended tier (via tiers_or_modules mapping)
```

A pricing engine can then use the recommended tier to select an appropriate `tier_id` in a `DealConfiguration` (see the [`pricing-models`](https://github.com/the-value-project/pricing-models) repo).

---

## LLM Usage

Both schemas are designed as structured output targets for LLMs:

**Building a `ValueModel`:** Provide the LLM with product documentation and ask it to produce a `ValueModel` instance as a structured output. Key guidance: equations must use only declared variable names; improvement claims should express percentages as percents (not decimals) in descriptions; `tiers_or_modules` should reference entries in `tiers_and_modules`.

**Building `CustomerVariables`:** Provide the LLM with a `ValueModel` instance plus customer research (website, reports, press releases, call notes) and ask it to produce a `CustomerVariables` instance. Key guidance: every variable estimate must cite at least one `sources_summary` entry; `execution_risk` and `attribution_percentage` must be explicit for every selected driver; `missing_variables` should document anything that couldn't be estimated.

---

## File Structure

```
value-models/
├── README.md
├── schemas/
│   ├── value_model.json          ValueModel schema (JSON Schema Draft-07)
│   └── customer_variables.json   CustomerVariables schema (JSON Schema Draft-07)
├── examples/
│   ├── value_model_example.json
│   └── customer_variables_example.json
└── CHANGELOG.md
```

---

## Versioning & Status

| Schema | Version | Status |
|--------|---------|--------|
| `ValueModel` | 1.0 | Implementation-Ready |
| `CustomerVariables` | 1.0 | Implementation-Ready |

Both schemas use JSON Schema Draft-07. `ValueModel` uses `additionalProperties: false` throughout. `CustomerVariables` uses `additionalProperties: true` to support implementation extensions while preserving the required core structure.

---

## Related

- [`pricing-models`](https://github.com/the-value-project/pricing-models) — PricingModel, DealConfiguration, InvoiceStatement schemas
- [The Value Project](https://github.com/the-value-project) — org overview and cross-schema relationships
- [ValueIQ](https://valueiq.ai) — the team behind this work

---

*Part of [The Value Project](https://github.com/the-value-project) by [ValueIQ](https://valueiq.ai).*
