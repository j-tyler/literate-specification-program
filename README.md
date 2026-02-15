# Literate Specification

Based on Knuth's concept of Literate Programming, crossed with Specification-Driven Development for AI coding

## Introduction

### Literate Programming

In 1984, Donald Knuth introduced Literate Programming, the idea that programs should be written primarily for humans to read, not for machines to execute. In a literate program, the source file reads top to bottom as a narrative dominated by natural language prose, with code woven in where needed to make the ideas concrete. The author explains *why* and *what* in plain English, then shows *how* with code. The result is a document that tells a story: the problem, the reasoning, the design choices, and the implementation, all in one place.

Traditional programming asks "does the compiler understand this?" Literate Programming asks "does a person understand this?"

### Specification-Driven Development

Specification-Driven Development is the practice of defining *what* a system should do before writing any code. You write a clear, complete specification covering the inputs, outputs, behaviors, constraints, and edge cases, then build the implementation to match.

This matters especially when AI is writing the code. A language model can generate working software remarkably fast, but only if it knows what "working" means. Without a specification, AI tends to produce code that looks plausible but drifts from intent in subtle ways. With a specification, every generated function has a target to hit and a standard to be measured against. The specification becomes the contract between human intent and machine output.

### Combining the Two

A Literate Specification brings these two ideas together. You write a specification that reads like a narrative, explaining the system from first principles, and interleave executable code that anchors every claim in working, verifiable logic. The same document serves two purposes: read it to understand what the system should do, run it to verify that the specification is self-consistent. This clarity benefits human reviewers and AI agents alike.

---

*The section below defines Literate Specification as a standalone reference. Copy it into your project to establish the standard. The example that follows demonstrates the methodology in practice and can serve as a model that teaches both humans and AI how to write Literate Specifications for their own domains.*

## Literate Specification

**Literate Programming**, introduced by Donald Knuth, is the idea that a program should read top to bottom as a narrative dominated by natural language prose with code woven in. The document explains *why* and *what* in plain English, then shows *how* with code. The original audience was a human reader, not a compiler. Today, with AI generating code from specifications, the audience is also an AI that benefits from this same level of clarity.

**Specification-Driven Development** is the practice of defining *what* a system should do, including its inputs, outputs, behaviors, constraints, and edge cases, before writing any implementation. When AI is generating code, the specification is what separates software that does what you meant from software that merely looks like it does.

A **Literate Specification** combines these two ideas. It is a clear, readable specification describing a system, written as a Literate Program that proves the behaviors it defines. The same document serves two purposes: read it to learn what the system does, run it to verify that the system does it.

### Priorities

Both the prose and the code in a Literate Specification share the same priorities, ranked by importance:

| Priority | Rule | Why |
|----------|------|-----|
| 1 | **Correct** | What the specification says is law. If the specification is wrong, every implementation built from it will be wrong too. |
| 2 | **Complete** | Underspecification leads to incomplete implementations. Every behavior, constraint, and edge case that matters must be stated. |
| 3 | **Clear and simple** | A specification that is correct and complete but unclear or needlessly complex will lead to implementation bugs. Simplicity serves correctness, never the other way around. |

### Prose and Code

1. **Code makes prose precise.** When *read*, code anchors the specification in concrete specifics. Where plain English might be ambiguous, code eliminates the ambiguity. A reader sees not just what is meant, but exactly how it works.

2. **Code verifies prose.** When *executed*, code serves as empirical proof that the specification is implementable and self-consistent. Every piece of code directly demonstrates the concept described in the surrounding prose.

### Example

A snippet from a Literate Specification of Semantic Versioning:

```python
"""
A semantic version is composed of three numeric identifiers:
MAJOR, MINOR, and PATCH, written as MAJOR.MINOR.PATCH.

- MAJOR changes when you make incompatible API changes.
- MINOR changes when you add functionality in a backward-compatible manner.
- PATCH changes when you make backward-compatible bug fixes.

Each identifier MUST be a non-negative integer expressed
as ASCII digits, and MUST NOT contain leading zeros.
"""

def parse_base_version(version_string: str) -> tuple[int, int, int]:
    major, minor, patch = version_string.strip().split(".")
    for part in (major, minor, patch):
        if not part.isascii() or not part.isdigit():
            raise ValueError(f"Invalid version identifier: {part!r}")
        if len(part) > 1 and part[0] == "0":
            raise ValueError(f"Leading zeros not allowed: {part!r}")
    return int(major), int(minor), int(patch)

assert parse_base_version("1.4.2") == (1, 4, 2)

"""
Precedence is determined by comparing major, minor, and patch
numerically, in that order. The first difference found
determines precedence.
"""

assert parse_base_version("0.1.0") < parse_base_version("1.0.0")
assert parse_base_version("1.0.0") < parse_base_version("1.1.0")
assert parse_base_version("1.1.0") < parse_base_version("1.1.1")

"""
A version MAY include a pre-release tag, appended after a hyphen.
The tag is a series of dot-separated identifiers. Each identifier
is either numeric (digits only) or alphanumeric.

Pre-release versions indicate that the version is unstable and
may not satisfy the intended compatibility requirements.
"""

base, tag = "1.0.0-alpha.1".split("-", 1)
identifiers = tag.split(".")
assert base == "1.0.0"
assert identifiers == ["alpha", "1"]

"""
Precedence between two versions is determined by the following
rules, evaluated from broadest to most specific. The first rule
that finds a difference determines the result.

Rule 1: Compare MAJOR, MINOR, and PATCH numerically. If two
versions differ in their base, none of the pre-release rules
matter.

Rule 2: A pre-release version ALWAYS has lower precedence than
the same version without a pre-release tag. For example,
1.0.0-alpha precedes 1.0.0.

Rule 3: When both versions are pre-release with the same base,
compare their dot-separated identifiers left to right:
- Numeric identifiers are compared as integers.
- Alphanumeric identifiers are compared lexically (ASCII order).
- A numeric identifier always has lower precedence than an
  alphanumeric identifier.
- If all compared identifiers are equal, the version with
  fewer identifiers has lower precedence.
"""

def is_numeric(identifier: str) -> bool:
    return identifier.isascii() and identifier.isdigit()

def precedes(a: str, b: str) -> bool:
    """True if version a has lower precedence than version b."""
    a_base, *a_rest = a.split("-", 1)
    b_base, *b_rest = b.split("-", 1)
    a_pre = a_rest[0] if a_rest else None
    b_pre = b_rest[0] if b_rest else None

    # Rule 1
    if parse_base_version(a_base) != parse_base_version(b_base):
        return parse_base_version(a_base) < parse_base_version(b_base)

    # Rule 2
    if a_pre is not None and b_pre is None:
        return True
    if a_pre is None:
        return False

    # Rule 3
    a_ids = a_pre.split(".")
    b_ids = b_pre.split(".")

    for a_id, b_id in zip(a_ids, b_ids):
        if a_id == b_id:
            continue
        if is_numeric(a_id) and is_numeric(b_id):
            return int(a_id) < int(b_id)
        if is_numeric(a_id):
            return True
        if is_numeric(b_id):
            return False
        return a_id < b_id

    return len(a_ids) < len(b_ids)

assert precedes("1.0.0-alpha", "1.0.0-alpha.1")
assert precedes("1.0.0-alpha.1", "1.0.0-alpha.beta")
assert precedes("1.0.0-alpha.beta", "1.0.0-beta")
assert precedes("1.0.0-beta", "1.0.0-beta.2")
assert precedes("1.0.0-beta.2", "1.0.0-beta.11")
assert precedes("1.0.0-beta.11", "1.0.0-rc.1")
assert precedes("1.0.0-rc.1", "1.0.0")

"""
A version MAY include build metadata, appended with a plus sign.
...
"""

# <continue Semantic Versioning specification>
```
