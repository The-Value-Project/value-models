# Contributing to The Value Project

> **License note:** This document is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). By contributing, you agree that your contributions to JSON schemas will be licensed under [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0), and contributions to specifications and documentation under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

---

Thank you for your interest in contributing. The Value Project publishes open schemas and specifications for B2B value-selling and sales-led pricing. Contributions that improve correctness, clarity, and usability are welcome.

---

## Ways to Contribute

### 1. Report a schema issue
Open an issue in the relevant repository if you find:
- An error, ambiguity, or missing field in a schema
- A field whose `description` is insufficient for a human or LLM to apply it
- An inconsistency in the join keys between schemas across the two repos

### 2. Propose a schema change
Open an issue labelled `schema-proposal` before submitting a pull request. Discuss the change, its impact on backward compatibility, and which other schemas it touches. Breaking changes (those that invalidate existing conforming instances) require strong justification and increment the MAJOR version.

### 3. Improve specifications or documentation
Pull requests are welcome for:
- Correcting errors in spec documents
- Improving clarity of algorithm steps or field descriptions
- Adding worked examples that illustrate edge cases
- Fixing typos or formatting

### 4. Report implementation experience
If you have built tooling on top of these schemas and encountered friction, open an issue. This is the highest-value feedback we receive.

---

## Before Opening a Pull Request

1. **Open an issue first** for any non-trivial change.
2. **Keep changes focused.** One concern per PR.
3. **Preserve backward compatibility** unless explicitly proposing a MAJOR version increment.
4. **Update cross-references.** If your change affects join keys or field names that are referenced across repos, update both repos and note the dependency.
5. **Update the spec** if your change affects documented computation behavior.
6. **Update `CHANGELOG.md`** under the `[Unreleased]` heading.
7. **Update `schema_version`** in the affected schema's `x-version` field and the `const` on `schema_version` if introducing a version increment.

---

## Issue Guidelines

Use a clear descriptive title. For schema issues, include:
- Schema name and current version (e.g., `PricingModel v1.0.0`)
- The field or section in question
- Expected vs. actual behavior
- A minimal JSON example where relevant

---

## Style Guidelines

**Schema descriptions** must be self-contained: a human or LLM reading only the `description` field must understand what the field means and how to populate it correctly.

**Specification language** should use RFC 2119 terms: `MUST`, `MUST NOT`, `SHOULD`, `SHOULD NOT`, `MAY`.

**Cross-references** between repos must use full GitHub URLs pointing to the `main` branch.

**Version numbers** follow semver (`MAJOR.MINOR.PATCH`). The `schema_version` field in JSON instances and the `x-version` field in schemas must be kept in sync.

---

## License

By contributing to JSON schema files (`schemas/`), you agree your contributions will be licensed under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0).

By contributing to specification and documentation files (`spec/`, `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `examples/`), you agree your contributions will be licensed under the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

---

## Contact

For questions not suited to a GitHub issue, reach out via [valueiq.ai](https://valueiq.ai).

*Part of [The Value Project](https://github.com/The-Value-Project) by [ValueIQ](https://valueiq.ai)*

