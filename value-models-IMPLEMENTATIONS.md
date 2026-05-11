# Implementations — value-models

This file tracks known production implementations of the [`value-models`](https://github.com/The-Value-Project/value-models) schemas (`ValueModel` and `CustomerVariables`).

Registered implementations count toward the **Stage 2 governance trigger** — once 10 independent production implementations are registered across The Value Project, an independent Technical Steering Committee will be formed. See the [Governance document](https://github.com/The-Value-Project/.github/blob/main/GOVERNANCE.md) for details.

---

## What Counts as a Production Implementation

A qualifying implementation is a deployed system that:

1. Reads or writes JSON conformant to the `ValueModel` and/or `CustomerVariables` schemas published in this repository
2. Is in active use by at least one paying customer or end user
3. Is registered here by the implementing organization via pull request

Implementations do not need to be public or open source. Proprietary commercial products qualify.

---

## Registered Implementations

| # | Organization | System | Schemas Used | Version | Registered |
|---|---|---|---|---|---|
| 1 | ValueIQ Technologies Inc. | ValueIQ Value Sales Agent | `ValueModel`, `CustomerVariables` | 1.0 | May 2026 |

---

## Registering Your Implementation

Open a pull request adding a row to the table above. Your PR must include:

- **Organization** — your company or project name
- **System** — the name of the product, service, or tool that implements the schema
- **Schemas Used** — which schema(s) you implement (`ValueModel`, `CustomerVariables`, or both)
- **Version** — the schema version you are implementing against (e.g., `1.0`)
- **Registered** — month and year of registration
- **Contact** *(optional, omit if you prefer not to make it public)* — a name or email for TSC nomination purposes when Stage 2 is triggered

You do not need to disclose proprietary implementation details. The registration is a statement of conformant use, not a code submission.

---

## Stage 2 Progress

**Implementations registered (excluding ValueIQ):** 0 of 10 required

When this count reaches 10, ValueIQ will initiate the Stage 2 transition and contact all registered non-ValueIQ organizations about TSC seat eligibility.

---

*Governed under [CC BY 4.0](./LICENSE-CC-BY-4.0). Part of [The Value Project](https://github.com/The-Value-Project) by [ValueIQ](https://valueiq.ai).*
