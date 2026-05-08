---
license: "CC-BY-4.0"
license_url: "https://creativecommons.org/licenses/by/4.0/"
license_full_text: "LICENSE-DOCS"
copyright: "Copyright 2026 The Value Project (an initiative by ValueIQ — https://valueiq.ai)"
version: "1.0.0"
repo: "https://github.com/The-Value-Project/value-models"
related_schema: "https://github.com/The-Value-Project/value-models/blob/main/schemas/value_model.json"
related_page_spec: "https://github.com/The-Value-Project/value-models/blob/main/spec/value-page-spec.md"
related_pricing_llm_reference: "https://github.com/The-Value-Project/pricing-models/blob/main/spec/pricing-model-llm-reference.md"
part_of: "The Value Project — https://github.com/The-Value-Project"
---

# ValueModel LLM Interpreter Reference


**The Value Project — value-models standard, v1.0**

You are reading this document because you have encountered a `ValueModel` JSON instance on a value model page conforming to The Value Project value-models standard. This reference tells you exactly how to interpret that JSON and how to use it to quantify the value of a solution for a specific customer.

Read this document fully before processing the JSON.

---

## What a ValueModel Is

A `ValueModel` is a complete, computable representation of a product's value proposition. It defines every variable that drives value, every equation that calculates economic impact, and every tier or module that unlocks each driver. It is the product-level model — it does not contain customer-specific estimates. Those live in a `CustomerVariables` document.

The `ValueModel` answers: *"How does this solution create economic value, for whom, and under which conditions?"*

---

## Schema Structure

The JSON object has the following top-level arrays. `variables`, `dependencies`, and `value_drivers` are required. `tiers_and_modules` is present when the product has a tiered or modular structure.

| Field | Type | What it contains |
|-------|------|-----------------|
| `variables` | array | All variables used in driver equations |
| `dependencies` | array | Derived variable relationships |
| `value_drivers` | array | Economic impact calculations, one per driver |
| `tiers_and_modules` | array | Catalog of subscription tiers and add-on modules |

---

## Variables

Each variable is an input to one or more value driver equations. Variables have:

- `name` — a JavaScript-safe identifier used in equations (e.g. `num_sales_reps`)
- `display_name` — a human-readable label
- `description` — what the variable means and how to determine its value for a specific customer
- `type` — `Money`, `Numeric`, or `Percentage`
- `category` — classifies the variable's origin:

| Category | Meaning |
|----------|---------|
| `Solution Variable` | A property of the vendor's solution (e.g. accuracy rate, response time) — typically a constant or range |
| `Improvement Claim` | The improvement the solution delivers (e.g. % reduction in manual effort) — vendor's assertion |
| `Market Variable` | A property of the customer's market context (e.g. average deal size, churn rate) |
| `Customer Variable` | A property of the specific customer's operations (e.g. number of reps, revenue, headcount) |

When estimating values for a specific customer, focus on `Market Variable` and `Customer Variable` items — these require customer-specific research. `Solution Variable` and `Improvement Claim` values are provided by the vendor and should be taken from the model unless the customer has data suggesting different outcomes.

**Percentage variables:** Equations use fractional form internally (e.g. `0.03` for 3%). The `description` field expresses examples as percents — translate accordingly when substituting values.

---

## Dependencies

Dependencies define variables that are derived from other variables rather than estimated directly. Each entry has:
- `variable` — the name of the derived variable
- `equation` — a JavaScript expression computing it from other named variables

When computing driver values, resolve dependencies first. Substitute the computed value for the derived variable name in any driver equation that uses it.

---

## Value Drivers

Each value driver defines how to calculate the economic impact of the solution on a specific customer. Key fields:

**`equation`** — a JavaScript expression that, when evaluated with the customer's variable values substituted in, produces the economic impact in currency units. All variable names in the equation appear in `variables_used` and are defined in `variables`.

**`continuity_profile`** — whether the impact is `one_time` or `recurring`:
- `one_time` — a single event (e.g. migration cost savings, implementation efficiency)
- `recurring` — an ongoing annual benefit. If `annual_decay_rate` is set, multiply the prior year's benefit by `(1 - annual_decay_rate)` each year. `time_horizon_years` caps the projection.

**`key_category_metric` and `key_category_metric_equation`** — the observable metric that should be tracked post-sale to verify that predicted value was actually realized. The `key_category_metric_equation` is a JavaScript expression, using the same declared variables, that computes this metric. This is distinct from the driver `equation`: the driver equation computes *predicted economic impact*; the key category metric equation computes *what to measure in the real world*.

**`tiers_or_modules`** — which tiers or modules from `tiers_and_modules` this driver requires. An empty array means the driver applies to all configurations. A non-empty array means the driver's value is only realized if the customer has selected at least one of the listed tiers or modules.

---

## Tiers and Modules

Each entry in `tiers_and_modules` has:
- `name` — the display name, referenced by `value_drivers[].tiers_or_modules`
- `module` — `true` if it is an add-on module; `false` if it is a subscription tier
- `includes` — names of other tiers or modules included within this one

Use `includes` to determine which drivers apply to a given tier: if a tier includes a module that is listed in a driver's `tiers_or_modules`, that driver applies.

---

## Computing Value for a Customer

To quantify the value of this solution for a specific customer, you need a `CustomerVariables` document that provides estimated values for each variable. With that document, proceed as follows:

**Step 1 — Resolve dependencies.** For each entry in `dependencies`, evaluate the `equation` using the customer's variable values. Store the result under the `variable` name.

**Step 2 — Identify applicable drivers.** For each driver in `value_drivers`, check:
- Is `selected: true` in the customer's `value_drivers` list? (from `CustomerVariables`)
- Does the customer's configuration include the required `tiers_or_modules`?
If both conditions are met, the driver applies.

**Step 3 — Evaluate each driver equation.** Substitute all variable values (including resolved dependencies) into the `equation`. The result is the raw predicted economic impact.

**Step 4 — Apply risk adjustments.** From the `CustomerVariables` document, for each selected driver:
- Multiply by `(1 - execution_risk)` to get realized value
- Multiply by `(attribution_percentage / 100)` to get attributed value
- Result: `attributed_value = raw_impact × (1 - execution_risk) × (attribution_percentage / 100)`

**Step 5 — Apply continuity profile.** For `one_time` drivers, the attributed value is a single figure. For `recurring` drivers, project over `time_horizon_years` applying `annual_decay_rate` each year:
- Year 1: `attributed_value`
- Year N: `attributed_value × (1 - annual_decay_rate)^(N-1)`
- Sum across years for total multi-year value.

**Step 6 — Sum across drivers.** Total quantified value = sum of all applicable drivers' attributed values (or multi-year values if projecting).

**Step 7 — Compute ROI.** Compare total value to the proposed price from the paired `PricingModel`. ROI = `(total_value - price) / price`. Payback period = `price / annual_value`.

---

## Key Category Metrics

For each applicable driver, the `key_category_metric_equation` defines what to measure post-sale. To surface these metrics:

1. Substitute the customer's variable values into the `key_category_metric_equation`
2. The result is the target value for `key_category_metric` — the number the customer should aim to hit
3. Track this metric at regular intervals to verify that predicted value is being realized

These metrics form the basis of a value realization review and should be included in customer success plans.

---

## Answering Common Questions

**"What value does this solution deliver to [Customer Type]?"**
Review the `value_drivers` array. For each driver, read `description` and `driver_impact` (if present) for narrative context. Note the `category` and `subcategory`. Summarize the drivers that are most likely to apply based on the customer type's characteristics.

**"Which drivers apply to [Tier/Module X]?"**
Find the tier or module name in `tiers_and_modules`. Find all drivers where `tiers_or_modules` contains that name, or where `tiers_or_modules` is empty (applies to all).

**"What variables do I need to estimate to quantify value for this customer?"**
List all `variables` with `category` of `Customer Variable` or `Market Variable`. These require customer-specific research. `Solution Variable` and `Improvement Claim` variables are vendor-supplied defaults.

**"How much of this value is recurring vs. one-time?"**
Group drivers by `continuity_profile.mode`. Sum attributed values separately for `one_time` and `recurring` groups.

**"What metric should we track to prove value post-sale for Driver X?"**
Read `key_category_metric` for the metric name. Evaluate `key_category_metric_equation` with the customer's variable values for the target number.

**"Is this driver applicable to a customer with [characteristic]?"**
Read `customer_criteria` (if present as an extension field) for explicit criteria. If absent, use the variable descriptions and `description` field to assess whether the customer's situation matches the driver's assumptions.

---

## What This Model Does Not Contain

- **Customer-specific variable estimates** — those live in a `CustomerVariables` document
- **Risk adjustments** — execution risk and attribution are set per-customer in `CustomerVariables`
- **Pricing** — use the paired `PricingModel` for price computation
- **Competitive claims** — competitive positioning may appear as extension fields but is not part of the standard schema

---

*ValueModel LLM Interpreter Reference — The Value Project, value-models standard v1.0*
*Published by [ValueIQ](https://valueiq.ai)*

---
*License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) · Copyright 2026 The Value Project (an initiative by [ValueIQ](https://valueiq.ai)) · Part of [The Value Project](https://github.com/The-Value-Project/value-models)*
