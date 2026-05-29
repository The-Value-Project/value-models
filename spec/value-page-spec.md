---
license: "CC-BY-4.0"
license_url: "https://creativecommons.org/licenses/by/4.0/"
license_full_text: "LICENSE-DOCS"
copyright: "Copyright 2026 The Value Project (an initiative by ValueIQ — https://valueiq.ai)"
version: "1.2.0"
repo: "https://github.com/The-Value-Project/value-models"
related_schema: "https://github.com/The-Value-Project/value-models/blob/main/schemas/value_model.json"
related_llm_reference: "https://github.com/The-Value-Project/value-models/blob/main/spec/value-model-llm-reference.md"
related_pricing_page_spec: "https://github.com/The-Value-Project/pricing-models/blob/main/spec/pricing-page-spec.md"
part_of: "The Value Project — https://github.com/The-Value-Project"
---

**The Value Project — value-models**

This document specifies the structure of a machine-readable value model page that publishes a vendor's `ValueModel` JSON in a form that both humans and LLMs can reliably read, verify, and reason about.

A conforming value model page is a Markdown document containing:
1. A structured front matter header
2. A structured human-readable model description
3. The complete `ValueModel` JSON in a fenced code block
4. A link to the LLM interpreter reference

The human-readable description serves two purposes: it allows a human reviewer to check the model for accuracy without reading raw JSON, and it gives an LLM a structured natural-language cross-reference to validate its interpretation of the JSON.

---

## Purpose

A `ValueModel` JSON instance precisely defines a product's value proposition in computable form. A value model page wraps it with framing — metadata, a structured narrative, and a pointer to interpretation instructions — that allows any LLM to correctly understand what the model represents and how to use it.

Value model pages are intended to be:
- Published alongside pricing pages and product documentation
- Used by LLM-assisted sales tools, value calculators, and deal intelligence pipelines
- Referenced in RAG pipelines, agent context windows, and tool call payloads
- Accessible to prospects and customers as a transparent statement of value

---

## Document Structure

A conforming value model page MUST contain the following sections in order:

### 1. Front Matter

YAML front matter declaring the document type and key metadata. Required fields:

```yaml
---
document_type: value-model
schema_version: "1.0"
schema_ref: https://github.com/the-value-project/value-models/blob/main/schemas/value_model.json
llm_ref: https://github.com/the-value-project/value-models/blob/main/spec/value-model-llm-reference.md
vendor: <vendor name>
product: <product name>
category: <the product category this model is designed for>
effective_date: <ISO date>
benchmark_claims_ref: <URL of the BenchmarkClaims JSON for this model>
---
```

Optional front matter fields:

```yaml
value_model_url: <canonical URL of this document>
segment: <target customer segment, if model is segment-specific>
contact: <contact email or URL for value discussions>
```

Note: `benchmark_claims_ref` is required when the model contains any `Improvement Claim` variables and a canonical URL has been set. If no URL has been set, omit the field entirely — do not write a placeholder string, as an LLM reading the page will attempt to fetch whatever value appears there.

### 2. Page Title

```markdown
# [Vendor Name] [Product Name] — Value Model
```

### 3. LLM Instruction Block

A clearly marked block directing an LLM to the interpreter reference before processing the JSON. MUST appear before both the human description and the JSON block.

```markdown
> **For LLMs:** This page contains a machine-readable ValueModel JSON instance conforming to
> The Value Project value-models standard (v1.0). Before interpreting the JSON below,
> read the interpreter reference at the `llm_ref` URL in the front matter. The human-readable
> description in this document is a structured narrative summary of the JSON — use it to
> validate your interpretation of the JSON, not as a substitute for it. The Improvement Claim
> Benchmarks section contains the vendor's asserted median, minimum, and maximum values for
> each Improvement Claim variable — use these as default values when estimating those variables
> for a specific customer unless customer-specific data is available.
```

### 4. Human-Readable Model Description

A structured narrative that describes the value model in plain language. This section MUST be derived directly from the JSON and MUST be kept in sync with it. A human reviewer should be able to check every statement here against the JSON.

The description MUST contain the following subsections:

#### 4a. Solution Overview

One paragraph describing:
- What the product does and who it is for
- The primary categories of value it delivers
- The customer types or segments this model is designed for
- Any important scope limitations or assumptions

#### 4b. Variables

A table listing every variable in the model, identified by display name:

| Display Name | Type | Category | Description |
|-------------|------|----------|-------------|
| [display_name] | [type] | [category] | [description — concise, ≤15 words] |

For variables with a dependency (i.e. they appear in `dependencies`), add a note in the description column: *(derived: [equation])*

Descriptions MUST be concise (≤15 words) while preserving the core meaning and any how-to-source guidance. The variable `name` (machine identifier) is not shown in this table — it appears in the ValueModel JSON block.

This table allows a buyer to understand what inputs drive the value calculation and where each input comes from.

*Category key: **Customer Variable** — specific to this buyer's organisation; **Market Variable** — industry-wide benchmark; **Improvement Claim** — vendor's asserted performance improvement; **Solution Variable** — fixed property of the product.*

Buyers should pay particular attention to `Improvement Claim` variables — these are the vendor's performance assertions and should be verified against the Improvement Claim Benchmarks section.

#### 4c. Value Drivers

One entry per value driver, in array order. Publishers may choose any presentation format (table, list, card, structured prose) suited to their rendering context. Regardless of format, each driver entry MUST convey the following information to a buyer:

| Field | Source | What a buyer needs to know |
|-------|--------|---------------------------|
| Number and name | `number`, `name` | Which driver this is and what it addresses |
| Category | `category` / `subcategory` | The type of value this driver delivers |
| Requires | `tiers_or_modules`; "All configurations" if empty | Which product tier or module must be purchased to realise this driver |
| Impact | `continuity_profile.mode`; include time horizon and decay rate if present | Whether value is delivered once or recurs over time |
| Key metric | `key_category_metric` | What a buyer would measure post-purchase to verify this value |
| Applies to customers who | `customer_criteria` rewritten for buyer audience | The customer conditions under which this driver applies. Lead with a self-qualification question or condition a buyer can answer immediately. State the condition once in plain language. Omit seller process instructions ("a seller can confirm by asking...") and repetition. 2–3 sentences maximum. |

A buyer reading this section should be able to determine: the full set of value drivers the product offers, which module or tier each requires, whether each driver is one-time or recurring, what to track after purchase, and whether each driver applies to their specific situation.

#### 4d. Improvement Claim Benchmarks

A table listing the vendor's asserted benchmark values for every variable with category `Improvement Claim` in the model. This section is required when `benchmark_claims_ref` is present in the front matter. Values MUST match the companion `BenchmarkClaims` JSON instance at that URL.

| Display Name | Basis | Min | Median | Max | Unit | Notes |
|-------------|-------|-----|--------|-----|------|-------|
| [display_name] | [basis] | [min] | [median] | [max] | [unit] | [notes or —] |

Rows are identified by `display_name`. The variable `name` (machine identifier) is not shown in this table — it appears in the BenchmarkClaims JSON block.

The `basis` field indicates the evidentiary foundation for the claim:

| Basis | Meaning |
|-------|---------|
| `customer_data` | Measured across actual customers |
| `third_party_research` | Published external research |
| `internal_testing` | Controlled internal benchmarks |
| `analyst_estimate` | Analyst or advisory firm projection |
| `vendor_estimate` | Vendor's own informed estimate |

This table allows a buyer to evaluate the vendor's improvement claims. The median is the typical outcome to expect; min and max show the range across different customer conditions.

*Basis key: **customer_data** — measured across actual customers; **third_party_research** — published external research; **internal_testing** — controlled vendor benchmarks; **analyst_estimate** — analyst projection; **vendor_estimate** — vendor's informed estimate (treat with most scrutiny).*

#### 4e. Tiers and Modules

If `tiers_and_modules` is present, one entry per tier or module. Publishers may choose any presentation format. Each entry MUST convey:

| Field | What a buyer needs to know |
|-------|---------------------------|
| Name | The tier or module name |
| Type | Whether this is a subscription tier or an add-on module |
| Description | What this tier or module includes or enables |
| Includes | Which other tiers or modules are bundled within it, if any |

---

### 5. Improvement Claim Benchmarks JSON Block

The complete, valid `BenchmarkClaims` JSON instance for this model, fenced with the `json` language identifier. Required when `benchmark_claims_ref` is present in the front matter.

````markdown
```json
{
  ...
}
```
````

Requirements:
- MUST validate against the `BenchmarkClaims` schema at `https://github.com/The-Value-Project/value-models/blob/main/schemas/benchmark_claims.json`
- `model_id` MUST match the `model_id` in the ValueModel JSON block
- Every variable with `category: "Improvement Claim"` in the ValueModel MUST have an entry in `claims[]`
- `variable_name` values MUST resolve to variables in the ValueModel JSON
- SHOULD be pretty-printed (2-space indent)

### 6. ValueModel JSON Block

The complete, valid `ValueModel` JSON instance, fenced with the `json` language identifier:

````markdown
```json
{
  ...
}
```
````

Requirements:
- MUST validate against the `ValueModel` schema at the `schema_ref` URL
- MUST be the complete model, not a subset
- Variable names in `dependencies` and `value_drivers` MUST resolve to entries in `variables`
- `tiers_or_modules` entries MUST resolve to entries in `tiers_and_modules`
- Fields with `null` values or empty arrays SHOULD be omitted rather than serialized
- `variables[]` entries SHOULD omit the `description` field (descriptions appear in the Variables table)
- `tiers_and_modules[]` entries SHOULD omit the `description` field (descriptions appear in the Tiers and Modules table)
- Driver `description` fields SHOULD be concise (≤25 words) while preserving the core impact mechanism
- SHOULD be pretty-printed (2-space indent)

### 7. Footer

```markdown
---
*This value model page conforms to [The Value Project](https://github.com/the-value-project)
value-models standard (v1.0). Generated: <ISO date>.*
```

---

## Full Template

```markdown
---
document_type: value-model
schema_version: "1.0"
schema_ref: https://github.com/the-value-project/value-models/blob/main/schemas/value_model.json
llm_ref: https://github.com/the-value-project/value-models/blob/main/spec/value-model-llm-reference.md
vendor: Acme Corp
product: Acme AI Platform
category: AI-assisted sales execution
effective_date: 2025-01-01
benchmark_claims_ref: https://acmecorp.com/benchmark-claims.json
value_model_url: https://acmecorp.com/value-model.md
---

# Acme Corp Acme AI Platform — Value Model

> **For LLMs:** This page contains a machine-readable ValueModel JSON instance conforming to
> The Value Project value-models standard (v1.0). Before interpreting the JSON below,
> read the interpreter reference at the `llm_ref` URL in the front matter. The human-readable
> description in this document is a structured narrative summary of the JSON — use it to
> validate your interpretation of the JSON, not as a substitute for it. The Improvement Claim
> Benchmarks section contains the vendor's asserted median, minimum, and maximum values for
> each Improvement Claim variable — use these as default values when estimating those variables
> for a specific customer unless customer-specific data is available.

## Solution Overview

[One paragraph: what the product does, who it is for, what categories of value it delivers,
any scope limitations]

## Variables

| Display Name | Type | Category | Description |
|-------------|------|----------|-------------|
| ...         | ...  | ...      | ...         |

## Value Drivers

[One entry per driver conveying: number, name, category/subcategory, requires, impact, key metric, and who it applies to. Format at publisher's discretion.]

## Improvement Claim Benchmarks

| Display Name | Basis | Min | Median | Max | Unit | Notes |
|-------------|-------|-----|--------|-----|------|-------|
| ...         | ...   | ... | ...    | ... | ...  | ...   |

## Tiers and Modules

[One entry per tier or module conveying: name, type (tier or module), description, and what it includes. Format at publisher's discretion.]

## Value Model

'''json
{
  ...ValueModel JSON...
}
'''

---
*This value model page conforms to [The Value Project](https://github.com/the-value-project)
value-models standard (v1.0). Generated: 2025-01-01.*
```

*(Replace `'''` with triple backticks in actual documents.)*

---

## Conformance

A value model page conforms to this specification if:

- [ ] Front matter is present and contains all required fields
- [ ] `document_type` is exactly `value-model`
- [ ] `llm_ref` points to a valid LLM interpreter reference
- [ ] The LLM instruction block appears before the human description and JSON
- [ ] All required human description subsections are present
- [ ] Every variable in the JSON appears in the Variables table, identified by display name
- [ ] Variable descriptions in the table are ≤15 words
- [ ] Every value driver in the JSON has an entry in Value Drivers conveying all six required fields: name, category, requires, impact, key metric, and applies to customers who (rewritten for buyer audience)
- [ ] The JSON block contains a complete, valid `ValueModel` instance
- [ ] All variable name cross-references within the JSON resolve correctly
- [ ] All `tiers_or_modules` references resolve to `tiers_and_modules` entries
- [ ] Null fields and empty arrays are omitted from the JSON block
- [ ] If `benchmark_claims_ref` is present: Improvement Claim Benchmarks table is present
- [ ] If `benchmark_claims_ref` is present: every `Improvement Claim` variable has a row in the benchmarks table, identified by display name
- [ ] If `benchmark_claims_ref` is present: BenchmarkClaims JSON block is present and `model_id` matches
- [ ] The footer is present

---

## Notes for Publishers

**BenchmarkClaims:** When the model contains `Improvement Claim` variables, publish a companion `BenchmarkClaims` JSON instance and set `benchmark_claims_ref` in the front matter. The benchmarks table and JSON block must be kept in sync with that file. An LLM generating a value model page should produce the benchmarks table and BenchmarkClaims JSON simultaneously with the ValueModel JSON.

**Keeping the description in sync:** The human-readable description is derived from the JSON and must be updated whenever the JSON changes. An LLM generating a value model page from a `ValueModel` instance should produce both simultaneously.

**Reviewer guidance:** The Variables table is the primary accuracy checkpoint — pay particular attention to `Improvement Claim` variables, which embed the vendor's assertions about solution performance. The Value Drivers table allows reviewers to verify the complete driver set, tier applicability, and key metrics at a glance; full equation and description detail is in the ValueModel JSON block.

**Pairing with a pricing page:** A value model page is most useful when published alongside a conforming pricing page. Together they allow an LLM to quantify value and compute the price of the appropriate configuration.

**Segment-specific models:** If you maintain separate value models for different customer segments, publish one value model page per segment and use the `segment` front matter field to disambiguate.

---
*License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) · Copyright 2026 The Value Project (an initiative by [ValueIQ](https://valueiq.ai)) · Part of [The Value Project](https://github.com/The-Value-Project/value-models)*
