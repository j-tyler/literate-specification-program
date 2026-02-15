# Literate Specification Prompt

*Copy this file into your project to teach AI agents how to work with Literate Specifications.*

---

This project uses Literate Specifications. Read and understand the reasons we use Literate Specification and the structure of a Literate Specification before continuing.

The executable code in a Literate Specification serves as an eval for the specification itself. This prevents contradictions, impossible constraints, and logical gaps from hiding in the prose. When implementing from a Literate Specification, that same eval code reduces defects by giving you both clear prose and concrete examples to work from.

## Background

**Literate Programming**, introduced by Donald Knuth, is the idea that a program should read top to bottom as a narrative dominated by natural language prose with code woven in. The document explains *why* and *what* in plain English, then shows *how* with code. The original audience was a human reader, not a compiler. Today, with AI generating code from specifications, the audience is also an AI that benefits from this same level of clarity.

**Specification-Driven Development** is the practice of defining *what* a system should do, including its inputs, outputs, behaviors, constraints, and edge cases, before writing any implementation. When AI is generating code, the specification is what separates software that does what you meant from software that merely looks like it does.

## What a Literate Specification Is

A **Literate Specification** combines these two ideas. When read, the document is a specification. The prose defines the system's behavior, and the code grounds each definition in a concrete, unambiguous example. When executed, the document is a proof. The assertions verify that the code does what the prose claims, which means the prose is not just plausible but demonstrably self-consistent. The same document serves both purposes: read it for understanding, run it for confidence that the understanding is sound.

## The Code Is a Reference Model

The code in a Literate Specification is a reference model, not an implementation blueprint. It exists to make the assertions runnable and the prose unambiguous, not to dictate how an implementation should be structured.

When *read*, the code anchors the prose in concrete specifics, eliminating ambiguity that plain English alone would leave open. When *executed*, it verifies that the specification is implementable and self-consistent. An implementation may look similar to the reference or entirely different. What matters is that the code satisfies the assertions.

**The assertions are canonical. The code that supports them is incidental.**

## Priorities

When writing a Literate Specification, prioritize correctness, then completeness, then clarity — making the correct and complete specification as clear as possible.

1. **Correct.** Every statement in the specification must be correct, because an error in the specification becomes an error in every implementation derived from it. No other priority may override this one.
2. **Complete.** A specification that is correct but incomplete will produce a system with undefined behavior, forcing implementers to guess on their own, deviating from what was desired or causing bugs. Every behavior, constraint, and edge case that matters must be explicitly stated, even when doing so makes the document longer or less elegant.
3. **Clear and simple.** Given that the specification is already correct and complete, make it as clear and simple as possible so that implementers do not introduce bugs through misunderstanding. Never simplify by removing necessary detail or softening a hard constraint.

## Examples

There is no single format for a Literate Specification. The shape of the document depends on what is being specified. What matters are the principles: correct, complete, clear, with code that makes prose precise and verifiable.

The two examples below specify different kinds of systems and take different forms, but both follow the same principles.

### Task Queue Priority

A specification that defines ordering rules, building up small functions with assertions that verify each rule:

```python
"""
A task queue processes tasks by severity. There are four
severity levels, from most to least urgent: critical,
high, normal, low.
"""

SEVERITY = {"low": 0, "normal": 1, "high": 2, "critical": 3}

assert SEVERITY["critical"] > SEVERITY["high"]
assert SEVERITY["high"] > SEVERITY["normal"]
assert SEVERITY["normal"] > SEVERITY["low"]

"""
To prevent starvation, a task's effective severity increases
by one level for each full day it waits, up to a maximum of
critical. A normal-severity task that has waited 48 hours is
treated as critical.
"""

def effective_severity(severity: str, age_hours: int) -> int:
    boost = age_hours // 24
    return min(SEVERITY[severity] + boost, SEVERITY["critical"])

assert effective_severity("normal", 0) == SEVERITY["normal"]
assert effective_severity("normal", 24) == SEVERITY["high"]
assert effective_severity("normal", 48) == SEVERITY["critical"]
assert effective_severity("critical", 999) == SEVERITY["critical"]

"""
When two tasks have the same effective severity, the older
task (the one submitted earlier) is processed first.

When aging raises a task to the same effective severity as
another task, age breaks the tie. A low-severity task that
has waited three days reaches critical effective severity AND
has been waiting longer than a fresh critical task.

It is processed first.
"""

def queue_position(severity: str, age_hours: int) -> tuple:
    return (effective_severity(severity, age_hours), age_hours)

assert queue_position("high", 10) > queue_position("high", 5)
assert queue_position("low", 72) > queue_position("critical", 0)
```

### Order Checkout

A specification where the sequence of operations matters. Here the prose lives inside a single function, declaring rules about ordering rather than defining separate components:

```python
"""
An order total is computed in three steps: subtotal,
discount, then tax. This sequence is not arbitrary —
each step depends on the result of the one before it.
"""

def checkout(items, discount_pct, tax_rate):
    """
    The subtotal is the sum of all item prices at full
    cost. The discount percentage applies to this amount
    as a whole, not to individual line items. Per-item
    rounding accumulates errors: 20% off three items at
    $1.33 gives $3.18 per-item but $3.19 whole.
    """
    subtotal = sum(item["price"] * item["qty"] for item in items)
    discount = round(subtotal * discount_pct, 2)

    """
    Tax is owed on the amount the customer actually pays,
    not the list price. The discount MUST be subtracted
    before tax is computed. Reversing this order overcharges
    tax on money the customer did not spend.
    """
    taxable = subtotal - discount
    tax = round(taxable * tax_rate, 2)

    return subtotal, discount, tax, taxable + tax

items = [{"price": 1.33, "qty": 3}]
subtotal, discount, tax, total = checkout(items, 0.20, 0.08)

assert subtotal == 3.99
assert discount == 0.80
assert tax == 0.26
assert total == 3.45
```

## When Implementing from a Literate Specification

- Read the specification top to bottom before writing any code. It is a narrative and builds on itself.
- Use the assertions as your acceptance criteria. Your implementation must produce the same answers.
- Derive your design from the prose and constraints. The reference model exists to support the assertions, not to serve as an architectural template. Write idiomatic code for your language and architecture.
- If the prose and an assertion disagree, the specification is invalid. Flag the inconsistency so it can be resolved before any implementation proceeds.

## When Editing a Literate Specification

- The document reads top to bottom as a narrative. Preserve that flow.
- Every claim in prose should be anchored by code or assertions.
- Do not add code that exists only for implementation convenience. Code exists to make prose precise and verifiable.
- Do not separate assertions from the prose they verify. Co-location is the point.
- The reference model can use any language or style. What matters is that the assertions are correct and unambiguous.
