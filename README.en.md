# Coding Standards

**Source:** Based on Andrej Karpathy's observations → [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) (4 rules) → Mnimiy ([@mnilax](https://x.com/Mnilax/status/2053116311132155938)) extended to 12 rules (claude-code-pro-pack) → 2026-09-16 restructured to 8 rules
**Purpose:** Guiding principles for AI coding agents — load them persistently, or inject them with coding tasks

> [中文版](./README.md)

---

## Priority (how to resolve conflicts)

1. **Security, data integrity, trust-boundary validation — non-negotiable.** No "simplification" may touch these three.
2. **All other conflicts: don't average them.** Pick one explicitly and say why.
3. **When something is unclear: stop and ask.** Don't guess.

## A Before you start

### 1. Read → Think → Write

- **Read**: before changing a function, read its callers; before adding a utility, search for an existing one; before creating a file, look at its siblings.
- **Think**: state assumptions explicitly; raise multiple interpretations; say when a simpler approach exists; stop and ask when unclear.
- **Never substitute trial-and-error for design.**

### 2. Simplicity First: Find the Minimum by the Ladder

**Stop at the first rung that holds** (ladder borrowed from the MIT-licensed [ponytail](https://github.com/DietrichGebert/ponytail)):

1. Does this need to exist at all? No → skip it (YAGNI)
2. Already in this codebase? → reuse it, don't rewrite
3. Does the standard library do it? → use it
4. Does a native platform feature cover it? → use it
5. Does an installed dependency do it? → use it
6. Can it be one line? → one line
7. Only then: the minimum code that works

**The ladder runs after you understand the problem, not instead of it.** Two rules ride along:

- **Build nothing that wasn't asked for**: no interface with one implementation, no factory for one product, no config for a value that never changes; no scaffolding "for later."
- **Convention over innovation**: in a project with established patterns, use them — **even if your approach is "better."** Two patterns are always worse than one.

Ask yourself: **would a senior engineer find this overcomplicated?**

## B While you work

### 3. Surgical Changes

- Change only what must change. **Don't "improve" neighboring code, comments, or formatting.**
- Clean up imports / variables / functions that **your own change** made unused.
- **Test: every changed line must map to a requirement.**

**Deleting existing code: default is don't. If you must, do all three — none is optional:**

1. **Confirm rollback first** — the target file is under version control and the working tree is clean; take an explicit rollback point (commit / tag / backup), and **verify you can actually get back**.
2. **Justify it separately** — file + line + why, written into the delivery report.
3. **State the rollback path** — the report says which commit to revert to and how.

**No rollback point → no deletion.** Set up version control and backups first, then come back.
When a deletion causes a fatal issue, "being able to fall back to a stable version" is the only safety net — **git solves "gone forever," not "nobody noticed."**

### 4. Finish What You Start (Checkpoints)

- Multi-step tasks: group them logically — **stop and confirm between groups**; **iterate autonomously within a group**.
- On error, roll back only to the last checkpoint, not to the start.

### 5. Surface Conflicts, Don't Average

When two parts of the codebase disagree, **pick one explicitly and explain why.**
Two error-handling patterns, two state-storage approaches, two code styles — choose the existing one, **don't create a third.**

## C Before you deliver

### 6. Goal-Driven: Acceptance Criteria First

Convert the task into verifiable goals before starting: **"add validation" → write a failing test first, then make it pass; "fix a bug" → write a reproduction test first; "refactor" → tests must pass before and after.**

Write a plan first for multi-step tasks: **`step → what verifies it`**.

### 7. Test Behavior, Not Shape

- Assertions must bind to **behavior**, not "something was returned."
- A test that can never fail provides zero protection in production.
- Coverage is not the goal; tests exist so you **can change code with confidence**.

### 8. Visibility: Failures and Trade-offs Both Leave a Trace

**Failures**: surface every skipped record, rolled-back transaction, and constraint violation; try/catch must not swallow an exception and report success; partial failures, skipped rows, truncated output, exhausted retries — **report all of it**. **Never report success while bypassing a problem.**

**Deliberate trade-offs**: when you simplify something with a known ceiling on purpose (a global lock, an O(n²) scan, a naive heuristic), leave one marker (convention from ponytail):

```
# ponytail: <what the ceiling is>, <when to redo it>
```

A marker with no trigger rots silently — **"later" must say what "later" means.**

## Pre-delivery self-check (answer each)

1. Did I state my assumptions explicitly?
2. Is there any change beyond the stated scope? If so, revert it or justify it.
3. Is there any test that "passes" without truly verifying behavior? Recheck the assertions.
4. Any partial failures / skipped records / truncated output? Surface them in the summary.
