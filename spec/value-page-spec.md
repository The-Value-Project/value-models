---
license: "CC-BY-4.0"
license_url: "https://creativecommons.org/licenses/by/4.0/"
license_full_text: "LICENSE-DOCS"
copyright: "Copyright 2026 The Value Project (an initiative by ValueIQ — https://valueiq.ai)"
version: "1.0.0"
repo: "https://github.com/The-Value-Project/value-models"
related_schema: "https://github.com/The-Value-Project/value-models/blob/main/schemas/value_model.json"
related_llm_reference: "https://github.com/The-Value-Project/value-models/blob/main/spec/value-model-llm-reference.md"
related_pricing_page_spec: "https://github.com/The-Value-Project/pricing-models/blob/main/spec/pricing-page-spec.md"
part_of: "The Value Project — https://github.com/The-Value-Project"

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
---
```

Optional front matter fields:

```yaml
value_model_url: <canonical URL of this document>
segment: <target customer segment, if model is segment-specific>
contact: <contact email or URL for value discussions>
```

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
> validate your interpretation of the JSON, not as a substitute for it.
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

A table listing every variable in the model:

| Variable | Display Name | Type | Category | Description |
|----------|-------------|------|----------|-------------|
| [name] | [display_name] | [type] | [category] | [description] |

For variables with a dependency (i.e. they appear in `dependencies`), add a note in the description column: *(derived: [equation])*

This table is the primary human-checkable artifact for the variable set. Reviewers should verify that every variable listed here makes sense for the stated solution and customer type, and that `Improvement Claim` values are defensible.

#### 4c. Value Drivers

One subsection per value driver, structured as follows:

```
### [number]. [name]

**Category:** [category] / [subcategory]
**Applies to:** [tiers_or_modules joined by ", ", or "All configurations" if empty]
**Impact type:** [one_time or recurring; if recurring: over N years, decay rate X%]
**Key metric:** [key_category_metric]

[description — verbatim from the JSON]

**Equation:** `[equation]`
**Key metric equation:** `[key_category_metric_equation]`
**Variables used:** [variables_used joined by ", "]
```

This structure allows a human reviewer to verify: that the equation uses only the declared variables, that the key metric is a plausible real-world observable, that the impact type is correctly characterized, and that the description matches the equation.

#### 4d. Tiers and Modules

If `tiers_and_modules` is present, a table:

| Name | Type | Description | Includes |
|------|------|-------------|---------|
| [name] | Tier / Module | [description] | [includes joined by ", ", or —] |

---

### 5. ValueModel JSON Block

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
- SHOULD be pretty-printed (2-space indent)

### 6. Footer

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
value_model_url: https://acmecorp.com/value-model.md
---

# Acme Corp Acme AI Platform — Value Model

> **For LLMs:** This page contains a machine-readable ValueModel JSON instance conforming to
> The Value Project value-models standard (v1.0). Before interpreting the JSON below,
> read the interpreter reference at the `llm_ref` URL in the front matter. The human-readable
> description in this document is a structured narrative summary of the JSON — use it to
> validate your interpretation of the JSON, not as a substitute for it.

## Solution Overview

[One paragraph: what the product does, who it is for, what categories of value it delivers,
any scope limitations]

## Variables

| Variable | Display Name | Type | Category | Description |
|----------|-------------|------|----------|-------------|
| ...      | ...         | ...  | ...      | ...         |

## Value Drivers

### 1. [Driver Name]

**Category:** [category] / [subcategory]
**Applies to:** [tiers_or_modules or "All configurations"]
**Impact type:** [one_time / recurring over N years, decay rate X%]
**Key metric:** [key_category_metric]

[description verbatim]

**Equation:** `[equation]`
**Key metric equation:** `[key_category_metric_equation]`
**Variables used:** [variables_used]

### 2. [Driver Name]
...

## Tiers and Modules

| Name | Type | Description | Includes |
|------|------|-------------|---------|
| ...  | ...  | ...         | ...     |

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
- [ ] Every variable in the JSON appears in the variables table
- [ ] Every value driver in the JSON has a corresponding subsection with all required fields
- [ ] Driver descriptions are stated verbatim from the JSON
- [ ] The JSON block contains a complete, valid `ValueModel` instance
- [ ] All variable name cross-references within the JSON resolve correctly
- [ ] All `tiers_or_modules` references resolve to `tiers_and_modules` entries
- [ ] The footer is present

---

## Notes for Publishers

**Keeping the description in sync:** The human-readable description is derived from the JSON and must be updated whenever the JSON changes. An LLM generating a value model page from a `ValueModel` instance should produce both simultaneously.

**Reviewer guidance:** The variables table is the primary accuracy checkpoint — pay particular attention to `Improvement Claim` variables, which embed the vendor's assertions about solution performance. The per-driver subsections allow reviewers to sanity-check equations and key metrics independently.

**Pairing with a pricing page:** A value model page is most useful when published alongside a conforming pricing page. Together they allow an LLM to quantify value and compute the price of the appropriate configuration.

**Segment-specific models:** If you maintain separate value models for different customer segments, publish one value model page per segment and use the `segment` front matter field to disambiguate.

---
*License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) · Copyright 2026 The Value Project (an initiative by [ValueIQ](https://valueiq.ai)) · Part of [The Value Project](https://github.com/The-Value-Project/value-models)*
