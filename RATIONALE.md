# Rationale — why 8 rules

> Companion to [中文版 README](./README.md) / [English README](./README.en.md).
> This file is **not** injected anywhere — open it when you want examples, or when discussing changes.

---

## Why split rules from rationale

Every line of the rules file goes into the model's context. Rule adherence drops sharply
beyond roughly 200 lines, and explanatory content (examples, reasons, comparison tables)
dilutes the actual constraints.

So: **constraints live in the rules; reasons and examples live here.**

---

## Why 12 → 8

| Was | Now | Why |
|---|---|---|
| 1 Think Before Coding + 8 Read Before You Write | → **1 Read → Think → Write** | Two faces of the same constraint |
| 2 Simplicity First | → **2** (+ the ladder) | The original gave the goal, not the search order |
| 3 Surgical Changes | → **3** | Deletion rule tightened — see below |
| 4 Goal-Driven Execution | → **6** | |
| 5 Don't use models for non-language work | **moved out** → scenario note (below) | Only applies when writing code that calls an LLM — not a general coding rule |
| 6 Hard token budget | **moved out** → process rule | It governs how the *agent* works, not what the *code* looks like |
| 7 Surface Conflicts, Don't Average | → **5** | |
| 8 Read Before You Write | **merged into 1** | |
| 9 Test for Correctness | → **7** | |
| 10 Long-Running Operations Need Checkpoints | → **4** | Conflict with "iterate autonomously" resolved: **confirm between groups, iterate within** |
| 11 Convention Over Innovation | **merged into 2** | |
| 12 Failure Must Be Visible | → **8** (+ the trade-off marker) | |

Net: **12 → 8**, with an explicit conflict-priority section added at the top.

---

## Failure modes each rule closes

**1 Read → Think → Write**
- *Silent wrong assumptions*: the agent guessed your intent; you find out three commits later.
- *Duplicate function*: a new function lands next to an identical existing one; which runs depends on import order.

**2 Simplicity First**
- *Over-engineering*: a 12-line fix becomes a 300-line abstraction layer.
- *Pattern pollution*: the codebase mixes two error-handling styles, new code uses both, errors get swallowed twice.

**3 Surgical Changes**
- *Orthogonal damage*: while fixing an unrelated bug, the agent reformats the whole file and renames variables.

**4 Checkpoints**
- *Cascading breakage*: step 4 of a 6-step refactor fails; steps 5 and 6 are already stacked on the broken state.

**5 Surface Conflicts**
- Two diverging patterns double the bug surface. The fix is to pick one and explain why — not to accommodate both.

**6 Goal-Driven**
- Weak criteria ("just make it work") cause repeated clarification, and the feature still doesn't work.

**7 Test Behavior, Not Shape**
- *Shape testing*: a function returns a constant, the test checks "a value was returned." All green. Auth is broken in production.

**8 Visibility**
- *Lying success*: a migration "completes successfully" while silently skipping 14% of records. Nobody notices until reports break 11 days later.
- *Silently rotting trade-offs*: a "we'll improve this later" marker that never says **when**. It never gets redone.

---

## Scenario note: don't use models for enumerable work

*(Removed from the rules in the 12 → 8 pass: it only applies when writing code that calls an LLM.)*

Retry strategies, routing decisions, rate-limit thresholds, arithmetic, sorting, counting —
**write them in code, not in a prompt loop.**

- A prompt deciding *"should we retry this 503?"* will read the whole request body, making retry behaviour random.
- Problems that application code should solve get handed to an LLM loop → unstable logic, unpredictable cost.

---

## About deletion

Deleting code carries two distinct risks, and version control only covers one:

| | Version control covers it | Version control does **not** |
|---|---|---|
| Losing the code | ✅ Fully recoverable | |
| **Not noticing it was deleted** | | ❌ A deletion buried in a diff is only discovered in production |
| **Being unable to roll back in time** | | ❌ Without a clean rollback point taken *before* the deletion, rolling back itself takes time |

So the rule is a **precondition**, not a permission: **a verified rollback point must exist before
anything is deleted.** The order cannot be reversed.

---

## Writing acceptance criteria

```
❌ make sure the API works
✅ POST /api/orders with a valid body returns HTTP 200 and the response contains `request_id`;
   the notification email actually arrives with subject [ORDER] …

❌ improve performance
✅ listOrders() returns 1000 orders in <50ms (timed in the test run)

❌ add input validation
✅ invalid input has tests; the tests must fail when the validation is removed
```

**Rule: acceptance must be runnable, readable, or observable as a status code.
If you can't write it that way, you don't have acceptance criteria.**

---

## Revision history

- **2026-05** — 4 rules (Karpathy) expanded to 12 (with Mnimiy's extension set).
- **2026-09-16** — restructured to 8: process rules moved out, overlapping rules merged,
  an explicit conflict-priority section added, and deletion made conditional on a verified rollback point.
