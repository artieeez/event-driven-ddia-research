> **Navigation:** [INDEX.md](INDEX.md) · [state.md](state.md) · [AGENTS.md](AGENTS.md)

This repo was created to experiment with ideas conveyed in the book Designing Data Intensive Applications (DDIA).

We are currently discussing the chapter 3 (2nd edition) in a book club.

I'll build an e-commerce system. Pretty boring, but serves as object to apply the ideas from DDIA.

## What's here now

- **`event-storming/`** — Event Storming workshop output (4 bounded contexts, aggregates, domain events)
- **`ecommerce/`** — Nx + NestJS implementation lab scaffolded from that model
- **`docs/`** — ADRs and deeper write-ups as they accumulate
- **`obsidian-vault/`** — Personal Obsidian notes (book club sessions, DDIA study logs)

More top-level folders (other domains, storage experiments) may appear later.

Any external service is expected to be provided in my personal cluster.

## Topics in focus

1. CQRS
2. Event Sourcing
3. Read models
