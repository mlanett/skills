---
name: logging-standards
description: Log a single line at the end of a function saying what it did, using K=V format.
---

# Logging Standards

- Each function or method should have at most one log line, placed at the end, describing what it accomplished.
- Logs in exception blocks are acceptable only if the exception is consumed (not rethrown).
- Always include the function or method name in the log message.
- Use key=value format for all parameters. No colons, no English phrases, no other separators.
- Logs must output to a single line for searchability. No newlines, no pretty-printed JSON, no separator lines, and no colorization escape codes.
- The single end-of-function log should be at INFO level. All other logs (if temporarily needed during development) should be DEBUG level.
