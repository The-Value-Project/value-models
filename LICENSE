# The Value Project

**Open schemas and specifications for B2B value-selling and sales-led pricing.**

The Value Project publishes a set of interoperable JSON schemas and accompanying specifications that represent the full commercial lifecycle of a B2B deal — from understanding what a customer values, to configuring a price, to issuing an auditable invoice.

These schemas are designed to be:

- **Machine-readable** — usable as structured output targets for LLMs (including OpenAI strict mode and Anthropic tool use)
- **Human-legible** — every constraint expressed in plain text, not code
- **Deterministic** — identical inputs produce identical outputs
- **Interoperable** — linked by stable IDs, composable across systems
- **Extensible** — core schemas define the standard; implementations may add fields without breaking conformance
- **Vendor-neutral** — applicable to any B2B SaaS or AI-backed product

---

## Repositories

### [`value-models`](https://github.com/The-Value-Project/value-models)

Schemas for understanding and quantifying customer value.

| Schema | Version | Description |
|---|---|---|
| `ValueModel` | 1.0.0 | Canonical Value Model — variables, value drivers, equations, and tier/module catalog |
| `CustomerVariables` | 1.0.0 | Customer-specific variable estimates, driver applicability, and risk adjustments |

These schemas answer: *"What is this solution worth to this specific customer, and why?"*

**Schemas:** Apache-2.0 · **Specifications:** CC BY 4.0

→ [Browse value-models](https://github.com/The-Value-Project/value-models)

---

### [`pricing-models`](https://github.com/The-Value-Project/pricing-models)

Schemas and a full specification for sales-led pricing, deal configuration, and invoice generation.

| Schema | Version | Description |
|---|---|---|
| `PricingModel` | 1.0.0 | Vendor's complete pricing — modules, tiers, plans, discounts, credit types, business rules |
| `DealConfiguration` | 1.0.0 | Customer-specific deal referencing a PricingModel instance |
| `InvoiceStatement` | 1.0.0 | Deterministic, auditable computed output — line items, adjustments, entitlement summary |

These schemas answer: *"Given what this customer wants to buy, what do they owe, and why?"*

**Schemas:** Apache-2.0 · **Specifications:** CC BY 4.0

→ [Browse pricing-models](https://github.com/The-Value-Project/pricing-models)

---

## How the Schemas Relate

```
ValueModel          defines variables, drivers, equations, tier/module catalog
      │
CustomerVariables   customer-specific estimates + risk adjustments → ROI + tier recommendation
      │                                                                        │
      │                                                                        ▼
PricingModel        vendor's full pricing catalog
      │
DealConfiguration   customer's deal — selects tier, quantities, discounts
      │
InvoiceStatement    deterministic computed output — fully auditable
```

The cross-repo join key: `ValueModel.tiers_and_modules[].name` corresponds to `PricingModel.products[].tiers[].name`. A customer value analysis recommends a tier; that tier feeds directly into a `DealConfiguration`.

---

## Licensing

| Content | License | SPDX |
|---|---|---|
| JSON schemas (`schemas/`) | Apache License 2.0 | `Apache-2.0` |
| Specifications, docs, examples (`spec/`, `README.md`, `examples/`) | Creative Commons Attribution 4.0 | `CC-BY-4.0` |

Each repo contains `LICENSE-CODE` (Apache 2.0 full text) and `LICENSE-DOCS` (CC BY 4.0 full text).

---

## Design Principles

1. **Structure over expressions.** Price and value emerge from declared objects and primitives — no free-form expressions.
2. **Plain-text business rules.** Every constraint is stated in natural language, interpretable by humans and LLMs alike.
3. **Deterministic evaluation.** Identical inputs produce identical results.
4. **Auditability.** Every invoice line and every value claim traces to a source field.
5. **Generic entitlements.** Any unit of value (seats, credits, API calls, tokens) is modeled via `credit_types` — never as hardcoded fields.
6. **Separation of concerns.** The model defines what's available. The configuration captures what was chosen. The output records what was computed.
7. **Self-documenting.** Every schema field carries a description sufficient for an LLM or engineer to apply it without external documentation.

---

## Contributing

Issues and pull requests are welcome in the relevant repository. Please read the `CONTRIBUTING.md` in each repo before opening a PR — in particular, schema contributions are licensed Apache-2.0 and specification contributions are licensed CC BY 4.0.

Open issues: [value-models](https://github.com/The-Value-Project/value-models/issues) · [pricing-models](https://github.com/The-Value-Project/pricing-models/issues)

---

*The Value Project is an initiative by [ValueIQ](https://valueiq.ai).*
