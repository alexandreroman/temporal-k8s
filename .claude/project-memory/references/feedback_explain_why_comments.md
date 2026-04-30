---
name: Comments explain the "why"
description: >-
  Add comments in code and configuration only to
  explain non-obvious choices, never the "what"
type: feedback
---

# Comments explain the "why"

Add comments in code and configuration files to
explain the *why* behind non-obvious choices —
not the *what* (which the code already shows).

**Why:** YAML manifests and Taskfile entries
contain many tunable knobs; without context,
future contributors (and Claude) cannot tell
whether a value is load-bearing or arbitrary.

**How to apply:** when introducing a non-default
value, an unusual annotation, a workaround, or a
constraint imposed by upstream, leave a short
comment above it explaining the rationale. Skip
comments for self-evident code.
