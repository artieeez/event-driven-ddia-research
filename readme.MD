> **Navigation:** [INDEX.md](INDEX.md) · [state.md](state.md) · [AGENTS.md](AGENTS.md)

# DDIA Research

Personal lab for experimenting with ideas from [*Designing Data-Intensive Applications*](https://dataintensive.net/) (DDIA). Topics and projects may change over time; the current focus is **CQRS**, **event sourcing**, and **read models** on a deliberately mundane B2C e-commerce domain.

DDIA chapter 3 (2nd ed., read models and query languages) motivates this direction — book club discussion in [docs/book-club/next-session.md](docs/book-club/next-session.md), then applied in code.

For the full repository map, implementation status, and drill-down links, see [INDEX.md](INDEX.md) and [state.md](state.md).

## What's here now

- **`event-storming/`** — Event Storming workshop output (4 bounded contexts, aggregates, domain events)
- **`ecommerce/`** — Nx + NestJS implementation lab scaffolded from that model
- **`docs/`** — ADRs and deeper write-ups as they accumulate

More top-level folders (other domains, storage experiments) may appear later.

Any external service is expected to run in the author's personal cluster.

## Topics in focus

1. CQRS
2. Event Sourcing
3. Read models
