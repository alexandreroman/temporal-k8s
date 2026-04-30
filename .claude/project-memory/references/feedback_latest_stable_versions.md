---
name: Use latest stable versions
description: >-
  Verify and pick the latest stable release before
  adopting any component (chart, image, CRD, tool)
type: feedback
---

# Use latest stable versions

Always use the latest stable version of any
component — Helm chart, container image, CRD,
CLI tool, or library. Verify against official
documentation or use the **context7** MCP server
before pinning a dependency.

**Why:** this is a test/dev project; staying
current minimizes the gap when contributors try
it on fresh setups, and surfaces compatibility
issues with the upstream ecosystem early.

**How to apply:** before adding or upgrading a
component, check the official source for the
current stable release. Do not reuse versions
visible in older docs, blog posts, or other
repos without verifying.
