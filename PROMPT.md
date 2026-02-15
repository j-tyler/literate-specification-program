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
