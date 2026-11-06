# Contributing

Thanks for improving **db-schema-assistant**.

## Before You Open a PR

- For large changes or new database targets, open an issue first so the scope can be agreed.
- Keep the skill's four-section output contract intact.
- Preserve universality: do not add vendor-specific instructions that only one assistant can follow. The skill must remain usable as plain Markdown.
- If you add a rule or default, update the quality gate, the few-shot examples, and the README claim that matches it.
- If you change a default rule, update every affected section, template, and example.
- Test generated SQL against the target database version, not just a parser.

## What Makes a Good Contribution

- A new database target (SQLite, SQL Server, Drizzle, Kysely).
- A realistic example (marketplace, fintech ledger, healthcare scheduling, logistics).
- A new few-shot example that demonstrates a distinct failure mode or ambiguity pattern.
- A corrected dialect edge case or migration recipe.
- Clearer rules, better checklists, or improved security guidance.
- Documentation fixes and accessibility improvements.

## Example Contributions

When adding a schema example, include:

1. Assumptions and scope.
2. A complete Mermaid `erDiagram`.
3. Executable DDL or ORM source.
4. Query-to-index rationale.
5. Forward migration and exact down migration.
6. Verification queries.

Do not include placeholders, truncated tables, or "add indexes as needed" language.

## Style

- Use `snake_case` for database identifiers.
- Use named constraints and indexes.
- Prefer explicit foreign-key actions.
- Explain every denormalization and every non-obvious index.
- Keep timestamps UTC and money in integer minor units.

## License

By contributing, you agree that your contributions are licensed under the repository's MIT License.
