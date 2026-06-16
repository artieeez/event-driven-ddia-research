# Book club — proposed discussion (next session)

> **Edition:** DDIA 2nd ed. (ISBN 9781098119058). Chapter numbers differ from the 1st ed.; always cite edition when comparing notes.
>
> **Repo context:** [state.md](../../state.md) · [INDEX.md](../../INDEX.md)

## Where we are

The [DDIA Research](../../readme.MD) lab already has a modeled e-commerce domain ([event-storming/](../../event-storming/)) and a scaffolded NestJS monorepo ([ecommerce/](../../ecommerce/)), but no event store, projections, or HTTP layer yet. This meeting is the right moment to align on **data-model and query-shape** decisions before we pick storage engines.

**Topics in focus for the lab:** CQRS, event sourcing, read models.

---

## This session — Chapter 3: Data Models and Query Languages

**Official title (2nd ed.):** Data Models and Query Languages  
**Book club focus:** read models, CQRS, event sourcing — and how query languages shape the way we think about the domain.

Chapter 3 is about the layer where application concepts meet general-purpose data models (relational, document, graph) and the languages we use to read and write them. Kleppmann's stack — real world → application objects → database model → bytes on disk — sets up why read/write separation is a modeling choice, not just an infrastructure trick.

### Suggested reading focus

| Section theme | Why it matters for us |
|---------------|---------------------|
| Layering data models | Our aggregates (application layer) vs. how we persist events and projections |
| Relational vs. document vs. graph | One store per context, or different models per read need? |
| CQRS / read–write separation | Core pattern for the lab; when is the split worth it? |
| Read models & denormalization | Each screen or API may need its own projection shape |
| Event sourcing (if covered in your reading) | Events as the write model; projections as derived read models |
| Query languages & access patterns | The query you need should drive the read model, not the other way around |

---

## Discussion prompts — from the chapter

### 1. Data models and how we think

- Kleppmann says data models affect how we **think about the problem**. Did Event Storming already fix our “application model,” or do we still have modeling work when we choose persistence shapes?
- Where do our six aggregates sit in the layer cake — and what sits *below* them (event envelopes, projection rows, integration payloads)?

### 2. CQRS — when and how much?

- What are the **write** responsibilities vs. **read** responsibilities in each bounded context (Shopping, Ordering, Fulfillment, Returns)?
- Do we need full CQRS everywhere, or only at context boundaries (e.g. Ordering write path, customer-facing read APIs)?
- What is the operational cost we are willing to pay (more stores, lag, rebuild logic) for read optimization?

### 3. Read models — one or many?

- List 3–5 concrete **read queries** the e-commerce lab needs (cart, order status, warehouse pick list, return eligibility, …).
- One denormalized read model per bounded context, or **one projection per query**?
- How stale can each read model be before the product breaks? (seconds, minutes, “after refresh”)

### 4. Event sourcing — separate decision?

- Does event sourcing follow from CQRS, or is it an independent choice?
- Which domain events from the workshop are **facts to keep forever** vs. **derived state we could recompute**?
- Happy path only for now — do we still design for correction/compensation events, or defer?

### 5. Query languages and access patterns

- For each read query above: is it a point lookup, a timeline/range scan, a join across contexts, or an aggregation?
- Does any query suggest a **graph** read model (e.g. order → shipments → returns), or is relational/document enough?

---

## Discussion prompts — tied to repository status

Snapshot as of 2026-06-16 ([state.md](../../state.md)):

| Area | Status | Discussion hook |
|------|--------|-----------------|
| Event Storming / bounded contexts | ✅ Complete | 4 contexts, 26 events — source material for commands, events, and read queries |
| Domain layer (`ecommerce/libs/*`) | ✅ Scaffolded | Aggregates and repository interfaces exist; persistence shape still open |
| Event store | ❌ Not started | Is the write model an event stream, or aggregates + events as a side effect? |
| Read-model projections | ❌ Not started | Which projections do we build first? |
| HTTP / application layer | ❌ Not started | Separate read and write APIs from day one? |

### Map the workshop to read/write paths

Use [event-storming/04-bounded-contexts.md](../../event-storming/04-bounded-contexts.md) as a whiteboard:

```
[Command] → Write model / aggregate → [Domain events]
                                              ↓
                                    [Projection handler(s)]
                                              ↓
                                    Read model(s) per query
```

- Which events are **integration** events (cross-context) vs. **internal** to one aggregate?
- Where do **policies** run — synchronous in the write path, async consumers, or both?
- **First vertical slice** — which user journey do we wire first?
  - Candidate: `PlaceOrder` → events → order status read model (Ordering)
  - Alternative: Shopping → Ordering (`CheckoutInitiated` → draft order)

### Decisions worth aiming for this session

- [ ] Agree on CQRS scope (whole monorepo vs. per-context vs. first slice only)
- [ ] Name 2–3 read models for v0 and their query shapes
- [ ] Decide whether v0 write model is event-sourced or “events later”
- [ ] Pick the first vertical slice (journey + bounded context)
- [ ] Note acceptable projection lag in plain language

Record outcomes in [state.md](../../state.md).

---

## Spoiler — Chapter 4 is next (Storage and Retrieval)

Chapter 3 ends by handing off to Chapter 4: once you know **what** you store and **how** you query it, the next question is **how the database stores bytes and finds them again** (B-trees, LSM-trees, column stores, indexes, …). We are not discussing that in depth today — but the choices we make in Chapter 3 should stay compatible with storage trade-offs we will hit soon.

---

## Take home — provocations for reading Chapter 4

Start Chapter 4 after this meeting with these questions in mind (no need to answer them tonight):

1. **Workload fit** — For each read model we named today, is the underlying access pattern OLTP (point lookups, small updates) or analytical (scans, aggregations)? Would they belong on the same engine?

2. **Append-heavy writes** — If we event-source, our write path looks like an append-only log. Chapter 4's log-structured storage section is the database-side mirror of that idea. What would replaying projections from scratch cost us?

3. **One database or several** — Does CQRS already imply multiple storage engines in our lab, or can Postgres (or similar) play every role until we outgrow it?

4. **Indexes follow queries** — Every read query we listed implies indexes (or their absence). When you read about secondary indexes in Ch. 4, map them back to one concrete query from our domain.

5. **First storage experiment** — The repo plans to compare backends for read models. After Ch. 4, which **two** engines would teach us the most with the least setup?

Bring one note per question — or just the ones that sparked something — to the next book club.

---

## Prep for participants

**Before the meeting**

1. Read (or re-read) Chapter 3 with the e-commerce domain in mind.
2. Skim [event-storming/04-bounded-contexts.md](../../event-storming/04-bounded-contexts.md) and pick one context to think about read vs. write.
3. Optional: for one aggregate in [event-storming/03-aggregates.md](../../event-storming/03-aggregates.md), list what you would expose as events vs. what you would put in a read model.

**During the meeting**

- Keep [state.md](../../state.md) open — update focus, open questions, and planned experiments with whatever we agree.

**After the meeting**

- Update `state.md` DDIA reading table (mark Ch. 3 discussed).
- Begin Chapter 4 with the take-home provocations above.
- Replace or archive this file when scheduling the following session.
