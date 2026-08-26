# Scrum Plan — ELT Pipeline, Self-Service Analytics & Predictive Modeling

**Project (Jira, "My Scrum Space"):** ELT & Self-Service Analytics
**Duration:** 2 sprints, 1 week each
**Team:** Santosh Hiremath (solo — self-organized, not a claimed team project)

This plan restructures a project that was actually built (see
`elt-selfservice-analytics/` — 6 source modules, 23 tests, real CI) into
a genuine sprint-based backlog: real epics, real user stories with
acceptance criteria and story points, a real sprint goal per sprint, and
an honest retro reflecting what actually happened during the build —
including the one real bug that was found and fixed. Nothing here is
invented drama; the story points and acceptance criteria are written
against work that was genuinely done and tested.

Copy this into Jira as: one Epic per section below, one Story per
bullet, sized with the story points shown. Two sprints, sprint goals as
stated.

---

## Epic 1: ELT Data Pipeline (Load + Transform)

**Epic goal:** Get raw, messy booking event data into a queryable
warehouse table, with the actual Load-then-Transform pattern (not
transform-before-load).

### Sprint 1

**Sprint 1 goal:** Raw data lands in the warehouse untouched, and a
first working Transform pass produces a clean, queryable fact table.

- **STORY-1: Generate realistic synthetic booking events** (3 pts)
  - Acceptance criteria: generator produces booking events across 6
    product SKUs, 4 channels, 5 regions; includes realistic messiness
    (missing product names on ~10% of rows, a configurable duplicate-
    delivery rate) — not a pristine, idealized dataset.
  - Status: Done.

- **STORY-2: Load stage — raw events into warehouse, untransformed** (2 pts)
  - Acceptance criteria: a `raw_events` table exists in DuckDB after
    Load; row count matches input exactly (including duplicates);
    nulls preserved untouched. Any transformation happening during Load
    fails this story's acceptance criteria.
  - Status: Done.

- **STORY-3: Transform stage — deduplicate and enrich in-warehouse** (5 pts)
  - Acceptance criteria: `fct_bookings` table has zero duplicate
    `event_id`s; every row has a non-null `product_name` (recovered via
    lookup join where missing); `gross_revenue_eur` correctly computed
    as `price * quantity` for every row.
  - Status: Done.

- **STORY-4: Regression test — dedup does not over-collapse genuinely
  distinct events** (2 pts)
  - Acceptance criteria: two different events (different `event_id`)
    that happen to share every other field value must NOT be collapsed
    into one row by the dedup logic — only true duplicate deliveries
    are removed.
  - Status: Done. (Found this needed an explicit test after initially
    assuming `SELECT DISTINCT *` was obviously safe — it is, because
    `event_id` is part of the row, but the assumption was worth
    verifying explicitly rather than trusting by inspection.)

**Sprint 1 total: 12 points, all delivered.**

---

## Epic 2: Self-Service Analytics Layer

**Epic goal:** Let a business user answer common pricing/ops questions
without writing SQL, with guardrails against invalid or unbounded
queries.

### Sprint 2 (part 1)

- **STORY-5: Revenue-by-region query, with category filter** (3 pts)
  - Acceptance criteria: returns booking count, total revenue, and
    average price per region; optional category filter validates
    against an allowed-list and raises a clear error on an invalid
    category rather than silently returning nothing.
  - Status: Done.

- **STORY-6: Channel-performance query, with cancellation rate** (3 pts)
  - Acceptance criteria: cancellation rate computed correctly as a
    percentage of total bookings per channel — cancelled bookings must
    be counted, not filtered out by default (a naive self-service query
    that always excludes cancelled=TRUE rows would make this question
    unanswerable).
  - Status: Done.

- **STORY-7: Top-products-by-revenue query, with bounded limit** (2 pts)
  - Acceptance criteria: `limit` parameter validated to a sane range
    (1–50); non-integer or out-of-range limit raises a clear error
    instead of an unbounded or malformed query.
  - Status: Done.

**Sprint 2 (part 1) total: 8 points.**

---

## Epic 3: Predictive Modeling — Cancellation Risk

**Epic goal:** A genuinely predictive (not random, not suspiciously
perfect) cancellation-risk model trained on the transformed booking
data, with a business-usable threshold.

### Sprint 2 (part 2)

- **STORY-8: Train a cancellation-risk classifier on fct_bookings** (5 pts)
  - Acceptance criteria: logistic regression pipeline trains on
    transformed data, evaluated on a genuine held-out test split with
    ROC-AUC.
  - Status: Done — **but see BUG-1 below.**

- **BUG-1: First model's AUC (0.524) was barely above random** (3 pts)
  - Root cause: synthetic `cancelled` outcome was generated fully
    independent of every feature (`rng.random() < 0.08`), so there was
    no real signal for the model to learn.
  - Fix: rewrote the data generator so cancellation probability
    genuinely depends on lead time, price, and channel, with realistic
    per-event noise layered on top.
  - Acceptance criteria for the fix: AUC lands in a genuine, non-
    suspicious 0.55–0.65 range, verified stable across at least 3
    different random seeds (not just one lucky split).
  - Status: Done. This is the single most important story in the whole
    backlog to be honest about in an interview — it's a real example of
    catching a broken predictive-modeling setup rather than shipping a
    meaningless model.

- **STORY-9: Business-meaningful threshold selection (not 0.5 default)** (2 pts)
  - Acceptance criteria: threshold chosen to hit ~50% recall rather than
    an arbitrary 0.5 probability cutoff; precision at that threshold
    must exceed the base cancellation rate (i.e. the model must beat
    "just guess the base rate").
  - Status: Done.

**Sprint 2 (part 2) total: 10 points.**

**Sprint 2 total: 18 points, all delivered.**

---

## Epic 4: CI & Verification

**Epic goal:** The whole pipeline is verified automatically, not just
"it ran once on my machine."

- **STORY-10: GitHub Actions CI running full pipeline + test suite on
  every push** (2 pts) — Done.
- **STORY-11: 23-test suite covering Load, Transform, self-service
  queries, and the predictive model** (folded into Epics 1–3 above as
  acceptance-criteria tests, not counted separately to avoid double-
  counting the same work as two different stories).

---

## Sprint Retro (Sprint 2)

**What went well:**
- The Load/Transform split held up cleanly — no story from Epic 2 or 3
  needed to reach back into Load and change its behavior, which is a
  good sign the architecture boundary was drawn correctly in Sprint 1.
- Writing acceptance criteria before building each story (rather than
  after) made BUG-1 easier to catch — the acceptance criteria for
  STORY-8 explicitly asked "is the AUC in a genuine, non-suspicious
  range," which is what actually caught the problem, not luck.

**What didn't go well / what I'd change:**
- BUG-1 should arguably have been caught in Sprint 1, not Sprint 2 — the
  data generator (STORY-1) was written before there was a concrete
  acceptance criterion checking whether the "cancelled" field it
  produced was actually learnable. In a real team sprint, a "definition
  of ready" checklist on STORY-1 asking "does this field have a real,
  checkable relationship to at least one other field" would have caught
  this a sprint earlier.
- Story sizing (points) was assigned after the fact here, which is a
  known limitation of a retroactive plan — in a live sprint, points
  would be estimated by the team before work starts, and re-estimated at
  the retro against actual effort. That real estimate-vs-actual
  calibration loop is exactly the piece this retroactive plan cannot
  reproduce, and is worth being upfront about if asked in an interview.

**Velocity:** Sprint 1: 12 points. Sprint 2: 18 points (including 3
points of unplanned bug-fix work — BUG-1 — pulled in mid-sprint after
STORY-8's acceptance criteria failed).
