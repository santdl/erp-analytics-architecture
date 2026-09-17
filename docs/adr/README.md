# Decision records

Short records of the decisions that were genuinely contested. Each one states the option that was
rejected, because a decision record without the rejected alternative is just documentation.

| # | Decision | Status |
|---|---|---|
| [0001](0001-layering-and-registry.md) | Layered architecture with a module registry | Accepted |
| [0002](0002-enforce-boundaries-with-a-hook.md) | Enforce boundaries with a write-time static check | Accepted |
| [0003](0003-delete-not-truncate.md) | `DELETE`, not `TRUNCATE`, when rebuilding | Accepted |
| [0004](0004-full-refresh.md) | Full refresh over incremental ingestion | Accepted, with a known expiry |
| [0005](0005-in-process-scheduler.md) | In-process scheduler over an orchestrator | Accepted, with a known expiry |
| [0006](0006-no-unique-key-on-facts.md) | No unique key on fact tables | Accepted |
| [0007](0007-idempotent-ddl.md) | Idempotent DDL instead of a migration tool | Accepted, with a known expiry |
| [0008](0008-admin-is-not-a-role.md) | Platform administration is not a module role | Accepted |

"Accepted, with a known expiry" means the decision is right for the current scale and the
condition that invalidates it is written down in the record itself. A decision whose expiry
condition is not stated is a decision nobody will revisit.
