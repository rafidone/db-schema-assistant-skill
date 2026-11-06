# DB Schema Assistant

> An open-source AI Agent Skill that turns product ideas into production-grade relational database architectures.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenCode Skill](https://img.shields.io/badge/OpenCode-Skill-blueviolet)](https://opencode.ai)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15%2B-336791?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0%2B-4479A1?logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Prisma](https://img.shields.io/badge/Prisma-ORM-2D3748?logo=prisma&logoColor=white)](https://www.prisma.io/)
[![TypeORM](https://img.shields.io/badge/TypeORM-ORM-FE0803)](https://typeorm.io/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Version](https://img.shields.io/badge/version-1.1.0-blue.svg)](SKILL.md)

**DB Schema Assistant** is a portable Agent Skill for OpenCode, Claude Code, ChatGPT, and other AI assistants that support skill instructions or system prompts. Describe the product you are building; the skill produces a normalized, multi-tenant-aware, auditable database design with executable DDL or ORM models, an ERD, an index rationale, and a reversible migration plan.

The skill is deliberately **self-contained**: a model reading only `SKILL.md` gets the rules, the ambiguity defaults, the execution protocol, three few-shot examples, the output contract, and the quality gate.

---

## Why This Skill Exists

Most AI-generated schemas stop at table names. They skip foreign-key behavior, soft-delete correctness, tenant isolation, partial indexes, and rollback plans. This skill makes those decisions explicit and reviewable.

It is designed for engineers who want a **starting point that survives code review**, not a diagram that collapses under production traffic.

### What you get

Every response contains four required sections:

| Section | Deliverable |
| --- | --- |
| **1. Mermaid ERD** | A complete `erDiagram` with entities, keys, and cardinalities |
| **2. Executable Schema** | PostgreSQL/MySQL DDL, or Prisma/TypeORM models |
| **3. Indexing & Performance** | A query-to-index map with composite, partial, GIN, and B-tree rationale |
| **4. Migration & Rollback** | Forward migration, lock analysis, verification queries, and an exact down migration |

---

## Features

- **3NF by default** — first, second, and third normal form checks, with an explicit denormalization ledger for justified exceptions.
- **Mandatory audit columns** — every table gets `id`, `created_at`, `updated_at`, and `deleted_at`.
- **Production-grade integrity** — named foreign keys with `ON DELETE`/`ON UPDATE`, `CHECK` constraints, unique business keys, and guarded state transitions.
- **Soft-delete correctness** — partial unique indexes for PostgreSQL, generated-column uniqueness for MySQL, and explicit ORM guidance.
- **Multi-tenant isolation** — `tenant_id` on tenant-owned tables, tenant-first indexes, and composite foreign keys that prevent cross-tenant references.
- **Query-driven indexing** — B-tree, hash, GIN, BRIN, composite, partial, and covering-index decisions tied to real access patterns.
- **Multi-database support** — PostgreSQL, MySQL, Prisma, and TypeORM, with dialect-specific edge cases called out.
- **Reversible migrations** — forward plan, lock analysis, verification queries, and a down migration that reverses every object.
- **Security-minded defaults** — hashed tokens, no plaintext secrets, PII minimization, append-only audit logs.
- **No placeholders** — complete schemas, not `...` or "add indexes as needed."
- **Universal AI compatibility** — normative language, a portable execution protocol, and deterministic defaults so different models produce comparable designs.
- **Few-shot calibration** — three worked examples: an ambiguous but inferable prompt, a critically underspecified prompt, and a complex multi-table soft-delete design.
- **Explicit ambiguity handling** — missing requirements, conflicting requirements, existing-schema integration, multi-target requests, and incremental changes each have a defined behavior.

---

## Installation

### OpenCode (recommended)

OpenCode discovers skills in:

- **Global:** `~/.config/opencode/skills/<skill-id>/SKILL.md`
- **Project:** `.opencode/skills/<skill-id>/SKILL.md`
- **Compatibility paths:** `~/.claude/skills`, `~/.agents/skills`, `.claude/skills`, `.agents/skills`

Clone the repository directly into a skill directory:

```bash
mkdir -p ~/.config/opencode/skills
git clone https://github.com/rafidone/db-schema-assistant-skill.git \
  ~/.config/opencode/skills/db-schema-assistant
```

Or install it for a single project:

```bash
mkdir -p .opencode/skills
git clone https://github.com/rafidone/db-schema-assistant-skill.git \
  .opencode/skills/db-schema-assistant
```

The skill ID is the directory name: **`db-schema-assistant`**.

> These URLs already point at [`rafidone/db-schema-assistant-skill`](https://github.com/rafidone/db-schema-assistant-skill).

Verify discovery by asking OpenCode to list or load the skill, or explicitly reference it:

```text
@db-schema-assistant design a PostgreSQL schema for a multi-tenant SaaS billing platform
```

OpenCode watches skill directories and reloads on save. If the skill does not appear, confirm that the file is exactly `SKILL.md` inside the skill directory and that the directory name matches the ID you are loading.

### Claude Code and other assistants

Copy the repository into a compatible skills directory, for example:

```bash
mkdir -p .claude/skills
cp -R db-schema-assistant-skill .claude/skills/db-schema-assistant
```

Or paste `SKILL.md` into your assistant's system prompt / custom instructions. The skill is written to be portable: it uses standard Markdown, explicit rules, and target-specific sections rather than relying on a single vendor's tooling.

It has no OpenCode-specific runtime dependency, so the same file works as:

- a Claude Code skill,
- a ChatGPT custom GPT instruction or project instruction,
- a system prompt in an agent framework,
- a review checklist for a human database engineer.

When using it outside a skill-aware client, include the entire file. The section map near the top tells the model where to start and which section governs each decision.

---

## Usage

### Basic idea-to-schema

```text
@db-schema-assistant Design a multi-tenant SaaS schema for a project
management platform with organizations, users, roles, projects, tasks,
invitations, API keys, and audit logs.
```

### Choose a target

```text
@db-schema-assistant Design the same schema for MySQL 8.0+.
```

```text
@db-schema-assistant Generate the Prisma schema for a multi-tenant
subscription billing system with plans, invoices, and payments.
```

```text
@db-schema-assistant Generate TypeORM entities and migrations for a
multi-tenant helpdesk with tickets, agents, teams, and SLAs.
```

### Review an existing schema

```text
@db-schema-assistant Review this schema for 3NF violations, missing
foreign keys, soft-delete bugs, and missing indexes. Then produce a
corrected migration plan.
```

### Design for access patterns

```text
@db-schema-assistant Design an events/audit schema for 50M rows per month.
The top queries are by tenant + time range, by tenant + entity, and by
actor. Include partitioning and index recommendations.
```

### Example output

See [`examples/sample-output.md`](examples/sample-output.md) for a complete multi-tenant SaaS schema covering:

- tenants, users, memberships, roles, permissions
- plans, subscriptions, invoices, invoice items, payments
- projects, API keys, invitations, and audit logs
- 16 tables, 46 explicit indexes, 75 table CHECK constraints, and a full rollback plan

### Few-shot examples

[`SKILL.md`](SKILL.md#13-few-shot-examples) contains three worked examples that show the expected reasoning, assumptions, ERD, and SQL shape:

1. **Ambiguous but inferable** — a marketplace prompt that omits entity and commission details but can be designed safely with documented defaults.
2. **Critically underspecified** — a "build my startup" prompt that should trigger at most three blocking questions or a deliberately narrow SaaS foundation, never an invented domain schema.
3. **Complex multi-table plus soft delete** — a multi-tenant project-management model with subtasks, join entities, composite tenant foreign keys, coordinated soft delete, and an append-only audit trail.

---

## Repository Structure

```text
db-schema-assistant-skill/
├── SKILL.md                  # Primary system instructions, defaults, and few-shot examples
├── README.md                 # This file
├── CONTRIBUTING.md           # Contribution guidelines
├── LICENSE                   # MIT license
└── examples/
    └── sample-output.md      # Full multi-tenant SaaS reference output
```

### Documentation map

| Document | What it answers |
| --- | --- |
| [`SKILL.md`](SKILL.md) | Full rules, ambiguity defaults, execution protocol, few-shot examples, output contract, quality gate |
| [`SKILL.md#13-few-shot-examples`](SKILL.md#13-few-shot-examples) | Worked examples for ambiguous, underspecified, and complex requests |
| [`examples/sample-output.md`](examples/sample-output.md) | Complete 16-table reference output with ERD, DDL, index rationale, and rollback |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | How to propose rules, examples, and new database targets |
| [`LICENSE`](LICENSE) | MIT license terms |

---

## Rules Enforced by the Skill

| Area | Default rule |
| --- | --- |
| Normalization | 3NF; denormalization requires a comment and sync mechanism |
| Primary keys | UUIDv4 for public/distributed; BIGINT identity only for internal high-volume tables |
| Audit columns | `id`, `created_at`, `updated_at`, `deleted_at` on every table |
| Deletes | Soft delete by default; hard delete only for retention/erasure |
| Foreign keys | Explicit `ON DELETE` and `ON UPDATE` on every relationship |
| Integrity | Named `CHECK` constraints for status, money, dates, and cross-field rules |
| Indexes | Every FK indexed; query-driven composite/partial indexes; tenant-first ordering |
| Multi-tenancy | `tenant_id` on tenant-owned tables; composite FKs prevent cross-tenant references |
| Money | Integer minor units or exact decimal; never floating point |
| Timestamps | UTC; `timestamptz` on PostgreSQL, `DATETIME(6)` on MySQL |
| Secrets | Hashed passwords/tokens; non-secret prefixes only |
| Migrations | Forward + down migration; lock analysis and verification queries |
| Ambiguity | Documented defaults; at most three blocking questions |
| Complex relationships | Join entities for attributes, self-reference cycle strategy, explicit polymorphic and ledger rules |

---

## Supported Targets

| Target | Status | Notes |
| --- | --- | --- |
| PostgreSQL 15+ | Full | `timestamptz`, `uuid`, `citext`, `jsonb`, partial indexes, RLS guidance |
| MySQL 8.0+ | Full | `DATETIME(6)`, `utf8mb4`, generated columns for partial uniqueness |
| Prisma | Full | Native types, relation actions, raw-SQL gaps called out |
| TypeORM | Full | Entities, relation actions, `synchronize: false`, migrations |

If no target is specified, the skill defaults to **PostgreSQL 15+** and states that assumption.

---

## Abridged Example Output

The skill does not just list tables. It explains the model. The following is a shortened excerpt; the full 16-table reference is in [`examples/sample-output.md`](examples/sample-output.md).

```text
## 1. Mermaid ERD
erDiagram
    TENANTS ||--o{ MEMBERSHIPS : "has"
    USERS ||--o{ MEMBERSHIPS : "joins"
    MEMBERSHIPS ||--o{ MEMBERSHIP_ROLES : "granted"
    ROLES ||--o{ MEMBERSHIP_ROLES : "assigned"
    ...

## 2. Executable Schema
CREATE TABLE memberships (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id uuid NOT NULL,
  user_id uuid NOT NULL,
  ...
  CONSTRAINT fk_memberships__tenant
    FOREIGN KEY (tenant_id) REFERENCES tenants (id)
    ON DELETE RESTRICT ON UPDATE CASCADE,
  ...
);

## 3. Indexing & Performance Rationale
| Index | Table | Definition | Serves | Why |
| --- | --- | --- | --- | --- |
| uq_memberships__tenant_user_active | memberships | UNIQUE (tenant_id, user_id) WHERE deleted_at IS NULL | Membership check | Prevents duplicate active membership; tenant-first |

## 4. Migration & Rollback Plan
...
DROP TABLE IF EXISTS memberships, ... CASCADE;
```

The complete reference output is in [`examples/sample-output.md`](examples/sample-output.md).

### Validation status

The repository is checked with:

- **PostgreSQL parsing** — every complete PostgreSQL example parses with `pglast`.
- **Executable DDL** — the 16-table reference schema and the few-shot schemas execute in PGlite; `citext` is substituted with `text` because PGlite does not ship the extension.
- **Behavioral checks** — coordinated soft delete, append-only audit protection, active-row uniqueness, and `updated_at` triggers are exercised.
- **Mermaid** — every ERD is parsed by the Mermaid parser.
- **Documentation** — all local Markdown links and heading anchors resolve.

These checks validate syntax and core behavior. They do not replace testing against your production database version, extensions, data volume, and access patterns.

---

## Customizing the Skill

The rules live in [`SKILL.md`](SKILL.md). Common customizations:

- **Change the default database** in the Operating Contract.
- **Use UUIDv7 or ULID** if your platform requires time-sortable identifiers.
- **Add organization-specific naming conventions** or required columns.
- **Make RLS mandatory** for every tenant-owned table.
- **Add a data-classification policy** for PII columns.
- **Require partitioning** above a row-count threshold.
- **Adjust the ambiguity defaults** in Section 0.3 if your organization has a standard tenancy or identity model.
- **Add domain-specific few-shot examples** to Section 13 when your team repeatedly designs the same kind of system.

Keep changes consistent across the rule sections and the quality gate. If you change a default, update the templates, the few-shot examples, and the sample output too.

---

## Contributing

Contributions are welcome. Good contributions include:

- New database targets (SQLite, SQL Server, Drizzle, Kysely).
- More real-world examples (marketplace, fintech ledger, healthcare scheduling).
- Improved dialect edge cases and migration recipes.
- Corrections to SQL syntax or ORM behavior.
- Better accessibility and documentation.

Please open an issue describing the change before a large pull request. For schema examples, include the full four-section output and verify the SQL against the target database version.

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for details.

---

## License

Released under the [MIT License](LICENSE).

```text
MIT License

Copyright (c) 2026 db-schema-assistant contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## Acknowledgments

Built for the OpenCode skill ecosystem and the broader community of AI-assisted software engineering. Inspired by the database design reviews that every production team eventually learns to do the hard way.

If this skill saves you a schema rewrite, consider starring the repository and sharing the example output with your team.
