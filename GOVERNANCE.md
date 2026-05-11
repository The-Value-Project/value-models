# Governance — The Value Project

**Version:** 1.0  
**Effective:** May 2026  
**Owner:** ValueIQ Technologies Inc.  
**Repository:** [github.com/The-Value-Project](https://github.com/The-Value-Project)

---

## Purpose

This document defines how The Value Project is governed: who makes decisions, how changes are proposed and accepted, how the standard evolves, and how governance will transfer as the ecosystem matures.

The Value Project publishes open JSON schemas and specifications for machine-readable B2B value models and pricing models. These are infrastructure-layer artifacts — they are designed to be adopted across CRM, CPQ, billing, subscription management, financial ERP, and AI agent systems. Infrastructure requires stable, transparent, trustworthy governance. This document is that governance.

---

## Governance Principles

**Open from day one.** All technical decisions, schema changes, and governance updates are made in public. No back-channel decisions. No private patches. If it changes the standard, it is visible in the repository.

**Transparency over process.** The goal is not bureaucracy. It is trust. Every decision that affects the schema or specification must be traceable to a public issue, a documented rationale, and a named decision-maker.

**Openness accelerates adoption. Adoption creates the moat.** The Value Project is published under open licenses because openness is the fastest path to ecosystem-wide adoption, and broad adoption is the condition under which this standard becomes genuinely useful infrastructure. ValueIQ leads the standard because we built it — not because the standard is locked to us.

**Planned transfer.** This project is designed to grow beyond any single company. The governance roadmap below makes that transfer explicit, with concrete triggers at each stage.

---

## Governance Roadmap

The Value Project follows a three-stage governance model, with transitions triggered by ecosystem milestones rather than calendar dates.

### Stage 1 — ValueIQ Leads (Now)

**Trigger:** Project inception through first 10 production implementations.

**Who decides:** ValueIQ Technologies Inc. All schema changes, specification updates, and governance decisions are made by the ValueIQ technical team.

**What "open" means at this stage:**
- All decisions are made in public GitHub issues and pull requests
- This governance policy is published and versioned in the repository
- Any contributor may open an issue, propose a change, or comment on any decision
- ValueIQ has merge rights and final say on all changes

**Decision-making process:**
1. Any change to a schema or specification begins as a GitHub issue in the relevant repository
2. The issue is labelled `proposal` and remains open for a minimum of 14 days for community comment
3. ValueIQ reviews comments, documents the rationale for the decision (accept, modify, or decline), and closes the issue with a written decision record
4. Accepted changes proceed to a pull request and are merged by a ValueIQ maintainer
5. All merged changes are recorded in `CHANGELOG.md` in the affected repository

**Maintainers (Stage 1):**
- ValueIQ Technologies Inc. technical team
- Maintainers are listed in `MAINTAINERS.md` in each repository

---

### Stage 2 — Independent Technical Steering Committee (10 Production Implementations)

**Trigger:** 10 independent production implementations of either the `value-models` or `pricing-models` schemas by organizations other than ValueIQ.

A "production implementation" is defined as: a deployed system that reads or writes schema-conformant JSON, is in active use by at least one paying customer or end user, and has been registered in `IMPLEMENTATIONS.md` in the relevant repository.

**What changes at this stage:**
- An independent Technical Steering Committee (TSC) is formed
- TSC holds collective merge authority on all schema and specification changes
- ValueIQ retains a permanent founding seat on the TSC
- Founding partner organizations (those with registered implementations at the time the TSC is formed) hold seats proportional to their implementation count, up to a maximum defined in the TSC charter
- New TSC seats are added as further qualifying implementations are registered

**TSC structure:**
- Minimum 3 members, maximum 9 members
- Decisions by simple majority for MINOR and PATCH changes
- Decisions requiring 2/3 supermajority: MAJOR version bumps, governance changes, new normative requirements, removal of any existing schema field
- TSC chair elected by TSC members annually
- Meetings held monthly, minutes published publicly in the `.github` repository
- No single organization may hold more than 1/3 of TSC seats (the founding partner rule applies at formation; the cap applies to all subsequent additions)

**ValueIQ's role at Stage 2:**
- Holds one permanent founding seat
- Continues to maintain implementation tooling and reference implementations
- Does not hold veto rights — all decisions go through TSC process
- Continues to employ the project's primary technical contributors

**Registering an implementation:**
Open a pull request adding your organization to `IMPLEMENTATIONS.md` in the relevant repository. Include: organization name, system description, schema(s) implemented, version(s) used, and a contact for TSC nomination purposes.

---

### Stage 3 — Linux Foundation (Long-term)

**Trigger:** TSC vote, requiring 2/3 supermajority, that the project has reached sufficient ecosystem scale and organizational diversity to benefit from transfer to a neutral foundation.

**What this means:**
- Intellectual property, trademarks (if any), and governance of the specification transfer to the Linux Foundation or an equivalent neutral open standards body
- ValueIQ retains a founding seat on whatever governance structure the foundation establishes
- The Apache 2.0 license on all published schemas and the CC BY 4.0 license on all published specification documents are irrevocable — transfer does not affect any existing adopter's rights
- The project continues under the same open governance principles; the foundation provides legal infrastructure, IP stewardship, and community support

**Why the Linux Foundation:**
The Linux Foundation hosts the industry's most successful open standards infrastructure — including OpenAPI (via the OpenAPI Initiative), JSON Schema, and CNCF projects that underpin cloud-native computing. It has the legal infrastructure, the community relationships, and the credibility to steward a standard that will need to interoperate with systems across the entire enterprise software ecosystem. The Value Project is building the commercial logic layer for the agent economy. That layer belongs on neutral infrastructure.

---

## Versioning Policy

All schemas and specifications follow semantic versioning: `MAJOR.MINOR`.

| Change type | Version impact | Approval required |
|---|---|---|
| New optional field | MINOR | TSC simple majority (Stage 2+) / ValueIQ (Stage 1) |
| New enum value | MINOR | TSC simple majority (Stage 2+) / ValueIQ (Stage 1) |
| Clarified field description | MINOR | TSC simple majority (Stage 2+) / ValueIQ (Stage 1) |
| New required field | MAJOR | TSC 2/3 supermajority (Stage 2+) / ValueIQ (Stage 1) |
| Renamed or removed field | MAJOR | TSC 2/3 supermajority (Stage 2+) / ValueIQ (Stage 1) |
| Changed field type | MAJOR | TSC 2/3 supermajority (Stage 2+) / ValueIQ (Stage 1) |
| New normative requirement | MAJOR | TSC 2/3 supermajority (Stage 2+) / ValueIQ (Stage 1) |

**Deprecation policy:** Any field or normative requirement marked `deprecated` must be supported for a minimum of two MINOR versions before removal. Deprecation notices are published in `CHANGELOG.md` and in the schema `description` field of the deprecated element.

**Compatibility guarantee:** MINOR version changes are backward-compatible. A system that reads `ValueModel` 1.0 must be able to read `ValueModel` 1.1 without modification. MAJOR version changes may require migration; migration guides are published in `spec/` alongside the new version.

---

## Change Control Process

All changes to schemas, specifications, or governance documents follow this process:

### Step 1 — Open an Issue
File an issue in the relevant repository (`value-models` or `pricing-models`). Label it `proposal`. The issue must include:
- A clear description of the proposed change
- The use case or problem it addresses
- The specific schema field(s) or specification section(s) affected
- Whether the change is MAJOR or MINOR (with reasoning)
- Any known impacts on existing implementations

### Step 2 — Community Review Period
The proposal remains open for a minimum of **14 days** (MINOR) or **28 days** (MAJOR) for community comment. During this period, any contributor may comment, ask questions, or propose modifications.

### Step 3 — Decision
At the close of the review period, the decision-maker (ValueIQ at Stage 1; TSC at Stage 2+) documents the outcome — accepted, accepted with modifications, or declined — with written rationale directly in the issue. The issue is then closed.

### Step 4 — Pull Request
Accepted changes are implemented in a pull request that references the originating issue. The PR must include:
- Updated schema file(s)
- Updated spec document(s) if affected
- Updated examples if affected
- `CHANGELOG.md` entry

### Step 5 — Merge
At Stage 1: a ValueIQ maintainer merges after internal review.  
At Stage 2+: a TSC member merges after TSC approval per the voting rules above.

---

## Contributor Rights and Responsibilities

**Anyone may:**
- Open issues proposing schema changes or specification improvements
- Comment on any open issue or pull request
- Submit pull requests with new examples, spec clarifications, or bug fixes
- Register a production implementation in `IMPLEMENTATIONS.md`

**Contributors retain:**
- Copyright in their contributions
- The right to use their contributions in any other context

**Contributors grant:**
- For schema and example contributions: a perpetual, irrevocable license to The Value Project (and its successors) to include the contribution under Apache 2.0
- For specification and governance contributions: a perpetual, irrevocable license to include the contribution under CC BY 4.0

By submitting a pull request, contributors confirm they have the right to make the contribution and grant the above licenses. See `CONTRIBUTING.md` for full contribution guidelines.

---

## Conflict Resolution

If a contributor believes a decision was made in violation of this governance policy:

1. Open an issue in the `.github` repository labelled `governance-dispute`, documenting the decision in question and the specific policy provision they believe was violated
2. ValueIQ (Stage 1) or the TSC chair (Stage 2+) will respond within 14 days with a written review
3. If the dispute involves a TSC decision, the TSC will hold a re-vote with the dispute recorded in the meeting minutes
4. If the dispute involves alleged ValueIQ overreach at Stage 2+, any TSC member may call an emergency session to review

---

## Licenses

The Value Project uses two licenses, applied by artifact type.

### JSON Schemas and Examples — Apache License 2.0

All JSON Schema files (`schemas/*.json`) and example files (`examples/*.json`) are licensed under the [Apache License, Version 2.0](./LICENSE-APACHE).

Apache 2.0 is chosen for schemas because it is maximally compatible with commercial software development, including proprietary systems. Implementors should not need to think twice about embedding these schemas in their products.

- You may use, copy, modify, and distribute these files in any product, commercial or open source, without restriction
- You must retain the copyright notice and license text
- You must state that you changed the files if you modify them
- The license is irrevocable — no future governance change can revoke rights already granted

### Specification and Governance Documents — CC BY 4.0

All specification documents (`spec/*.md`), governance documents (including this file), README files, and all other prose in this project are licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](./LICENSE-CC-BY-4.0).

CC BY 4.0 is chosen for specification and governance prose because it ensures attribution while remaining freely usable, shareable, and adaptable by anyone — including for commercial purposes.

- You may share and adapt this material for any purpose, including commercially
- You must give appropriate credit to The Value Project and ValueIQ, provide a link to the license, and indicate if changes were made
- No additional restrictions may be applied

### Summary

| Artifact type | License |
|---|---|
| JSON Schema files (`schemas/*.json`) | Apache 2.0 |
| Example files (`examples/*.json`) | Apache 2.0 |
| Specification documents (`spec/*.md`) | CC BY 4.0 |
| Governance and community documents | CC BY 4.0 |
| README files | CC BY 4.0 |

---

## Governance Document History

| Version | Date | Summary of Changes |
|---|---|---|
| 1.0 | May 2026 | Initial governance policy published |

---

*The Value Project is an initiative by [ValueIQ Technologies Inc.](https://valueiq.ai)*  
*Governance questions: open an issue in [The-Value-Project/.github](https://github.com/The-Value-Project/.github)*
