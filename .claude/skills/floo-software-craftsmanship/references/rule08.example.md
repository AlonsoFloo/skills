# Rule 8: Postel's Law (Robustness Principle) — Reference Examples

Deep dive for [SKILL.md — Rule 8](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

When receiving payloads over networks or inter-process communication, ensure
unexpected payload additions do not cause client failure, while outgoing
payloads rigidly adhere to schema specifications.
