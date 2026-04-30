---
name: Line length limits
description: >-
  80 columns max for text/Markdown,
  120 columns max for code and YAML
type: feedback
---

# Line length limits

- Text and Markdown: **80 columns max**
- Code and YAML manifests: **120 columns max**

**Why:** keeps files readable in narrow terminals
and side-by-side diffs, and avoids horizontal
scrolling in code reviews.

**How to apply:** wrap long Markdown paragraphs
at ~80 columns. For YAML, prefer block scalars
(`>-`, `|`) over inline strings when a value
would exceed 120 columns.
