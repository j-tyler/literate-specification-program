# Literate Specification

AI can write specifications, but prose alone leaves room for contradictions, impossible constraints, and logical gaps that are not caught until implementation. A Literate Specification closes this gap by embedding executable code directly in the document, serving as an eval for the specification itself. The code demonstrates claims the prose makes, so a specification written this way has a fundamentally lower chance of internal inconsistency because it has been empirically proven to hold together before any implementation begins.

The same advantage carries forward to implementation. An AI implementing from a traditional prose specification must interpret English, and English is ambiguous. An AI implementing from a Literate Specification has both the English explanation and concrete, runnable examples showing exactly what the prose means. The risk of misinterpretation drops because the AI is not guessing at intent; it can see the intent demonstrated in code. Implementations derived from a Literate Specification have a lower chance of defect because the specification itself is both clearer and proven consistent.

## Background

### Literate Programming

In 1984, Donald Knuth introduced Literate Programming, the idea that programs should be written primarily for humans to read, not for machines to execute. In a literate program, the source file reads top to bottom as a narrative dominated by natural language prose, with code woven in where needed to make the ideas concrete. The author explains *why* and *what* in plain English, then shows *how* with code. The result is a document that tells a story: the problem, the reasoning, the design choices, and the implementation, all in one place.

Traditional programming asks "does the compiler understand this?" Literate Programming asks "does a person understand this?"

### Specification-Driven Development

Specification-Driven Development is the practice of defining *what* a system should do before writing any code. You write a clear, complete specification covering the inputs, outputs, behaviors, constraints, and edge cases, then build the implementation to match.

This matters especially when AI is writing the code. A language model can generate working software remarkably fast, but only if it knows what "working" means. Without a specification, AI tends to produce code that looks plausible but drifts from intent in subtle ways. With a specification, every generated function has a target to hit and a standard to be measured against. The specification becomes the contract between human intent and machine output.

### Combining the Two

A Literate Specification brings these two ideas together. You write a specification that reads like a narrative, explaining the system from first principles, and interleave executable code that anchors every claim in working, verifiable logic. The same document serves two purposes: read it to understand what the system should do, run it to verify that the specification is self-consistent. This clarity benefits human reviewers and AI agents alike.

### Reference Model, Not Implementation

The code in a Literate Specification is a reference model, not an implementation blueprint. It exists to make the assertions runnable and the prose unambiguous, not to dictate how an implementation should be structured. An implementation may end up looking similar to the reference or entirely different. What matters is that the code satisfies the assertions.

Consider HTTP. The specification is prose. There are a million implementations: Apache, Nginx, curl, every browser, every HTTP library in every language. None share code. All must agree on behavior. When the prose is ambiguous, they disagree, and that disagreement is the source of decades of interoperability bugs: header parsing edge cases, chunked transfer encoding quirks, how to handle malformed requests. Every one of those bugs traces back to two implementers reading the same English differently with no executable reference to settle it.

A Literate Specification solves this. The reference model exists to make the assertions runnable. The assertions exist to make the prose unambiguous. What is canonical is the assertions, not the code that supports them. An implementer in Rust does not care how the reference model is written. They care that their implementation produces the same answers.

## Using Literate Specifications

[PROMPT.md](PROMPT.md) contains the complete, portable definition of a Literate Specification: what it is, the priorities, and guidelines for writing and implementing them. Copy that file into your project to establish the standard.
