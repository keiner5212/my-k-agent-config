---
name: quick-python-scripts
description: >-
  Load for focused Python CLI scripts, file processing, small automations,
  data transforms, log parsing, and repeatable one-off operational tasks.
---

# Quick Python Scripts

## When to use

- Create or update a small Python script for one focused operational task.
- Transform files, parse logs, call an API, or automate repeatable CLI work.
- Replace manual shell steps with a safe, documented command.

## Hard rules

- Search existing scripts and utilities first; extend or reuse before creating a new script.
- Keep one script focused on one task. Do not add frameworks, classes, or configuration layers without need.
- Use `argparse` for arguments, `main() -> int` for execution, and `sys.exit(main())`.
- Validate inputs before mutation; fail with clear stderr messages and nonzero exit codes.
- Send machine-readable result data to stdout and progress or diagnostics to stderr.
- Use `pathlib` and standard library first; add dependencies only when they remove clear complexity.
- Provide `--dry-run` for destructive or bulk mutations and make repeated runs safe where possible.

## Workflow

1. Inspect existing scripts, command conventions, input shape, and expected output.
2. Define command usage, required arguments, optional output, exit codes, and failure cases.
3. Implement smallest direct path using standard library modules.
4. Validate paths, formats, and permissions before processing.
5. Run against a representative input, including one invalid-input case.
6. Print concise result counts and write output atomically when replacing files.

## Checklist

- `--help` describes command purpose, arguments, and defaults.
- Positional inputs and output destination are validated.
- Exceptions produce actionable stderr messages and correct exit status.
- File writes avoid partial output; temporary file and rename used when needed.
- Destructive actions require explicit flag or dry-run preview.
- Script has no unused dependencies, globals, or unrelated options.

## Anti-patterns

- Creating a new script before checking existing automation.
- Using `sys.argv` parsing for more than a trivial fixed command.
- Printing logs and data together on stdout.
- Catching `Exception` and continuing with corrupted state.
- Adding a package for basic JSON, CSV, path, or subprocess work.
- Building a reusable framework for a single script.

## Refs

- `argparse`: CLI arguments and generated help.
- `pathlib`: path validation and file traversal.
- `json`, `csv`, `re`, `subprocess`, `shutil`: common standard library tools.
