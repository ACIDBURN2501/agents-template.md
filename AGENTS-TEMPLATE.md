# AGENTS.md

This document is intended to be consumed by AI coding agents. It is often
automatically injected into system prompts by tools like OpenCode. Keep it
self-contained. When starting a new repo, keep the **Generic** section
lean and delete any **Sections** that do not apply to your project.

---

## 1) Project-specific instructions

**Project:** <name>

**Primary goal:** <one sentence>

### 1.1 Essential commands

| Task               | Command     |
| ------------------ | ----------- |
| Setup              | `<command>` |
| Build              | `<command>` |
| Run                | `<command>` |
| Test (all)         | `<command>` |
| Test (single/fast) | `<command>` |
| Format             | `<command>` |
| Lint               | `<command>` |

### 1.2 Repo-specific gotchas (only the non-obvious)

- <e.g., simulation vs hardware constraints>
- <e.g., thread-safety constraints>
- <e.g., platform assumptions>
- Never commit secrets (`.env`, credentials.json, etc.)
- Use environment variables for sensitive data
- Avoid logging sensitive information
- Follow principle of least privilege for access
- Sanitize inputs to prevent injection attacks
- Validate all external data before processing

### 1.3 Generated / protected paths (do not edit)

- `<path/glob>` — reason
- `<path/glob>` — reason

### 1.4 Key locations (3–8 bullets; avoid full repo trees)

- `<path>` — purpose
- `<path>` — purpose

---

## 2) CI / source of truth

- Prefer `pre-commit run --all-files` if pre-commit is configured.
- CI definitions live in: `.github/workflows/*` (or equivalent)
- Prefer running the same commands locally as CI runs.
- If there is a `Makefile`, `meson.build`, `taskfile.yml`, or `noxfile.py`, use it.

---

## 3) Docs / commit conventions

- If asked to commit changes, use the Conventional Commits format.
- Keep PRs/commits focused; explain _why_ in the message body.
- **NEVER** commit unless user asks

---

## 4) Code quality

- Do not preserve backward compatibility unless the user explicitly asks for it

---

# Language Section (DELETE what you don’t need)

## Python

### Tooling defaults

- Use a virtual environment if the repo supports it (`.venv`).

### Formatting & linting

- If **Black** is used, it is mandatory for touched files.
- If **Ruff/Flake8** is used, fix lint errors; do not suppress unless necessary.
- Keep imports tidy and grouped: stdlib / third-party / local.

### Testing (pytest conventions)

- Run full suite: `pytest`
- Run one test: `pytest -xvs tests/<file>.py::test_name`
- Add/update tests for bug fixes and behavior changes.

### Error handling & logging

- Use `logging` for runtime diagnostics; avoid `print` for errors.
- Raise meaningful exceptions; catch only what you can handle.

### Typing (if the repo uses typing)

- Add type hints for public functions/APIs.
- Avoid `Any` unless justified.
- Use `# type: ignore[...]` only with a short comment explaining why.

---

## C / C++

### Build & configuration

- Prefer the repo’s build system (Meson/CMake/Make). Don’t introduce a new one.
- Keep build changes minimal; update build files when adding/removing sources.

### Formatting

- If `.clang-format` exists or formatting is documented, formatting is mandatory.
- Do not reformat unrelated code.

### Style & correctness expectations

- Match the existing conventions in the file (indentation, braces, naming).
- Validate pointers at public API boundaries where appropriate.
- Avoid heap allocation in hot paths unless required.

### Error handling

- Follow repo conventions (return codes vs errno vs exceptions in C++).
- Log errors consistently (stderr or project logger as applicable).

### Testing

- Run the repo’s unit tests after changes.
- Add tests for bug fixes where feasible.

---

## JavaScript / TypeScript

### Tooling

- Use the repo’s package manager (pnpm/yarn/npm) as configured.
- Use `package.json` scripts as the primary interface (lint/test/build).

### Formatting & linting

- If Prettier is configured, it is mandatory.
- If ESLint is configured, fix lint errors; don’t disable rules unless required.

### Testing

- Run unit tests (Vitest/Jest/etc.) relevant to the change.
- Update snapshots intentionally and explain why.

---

## Rust

- Format: `cargo fmt`
- Lint: `cargo clippy --all-targets --all-features`
- Test: `cargo test`
- Prefer idiomatic error handling with `Result` and `thiserror/anyhow` as used.

---

## Go

- Format: `gofmt` (or `go fmt`)
- Test: `go test ./...`
- Keep packages small; avoid circular deps; prefer table-driven tests.

---

## Validation Requirements

When identifying potential bugs or issues:

1. **Respect API Contracts**: All test cases must honor documented preconditions found in:
   - Function docstrings (e.g., `@pre: parameter must not be NULL`)
   - Inline comments and type annotations
   - Public API documentation

2. **Include Contract Guards**: When testing a function, include the same defensive code the library uses internally:

   ```c
   // Before testing a function, include its contract guards
   if (ptr == NULL) {
       // Handle contract violation as library does
       return;
   }
   ```

3. **Verify Internal Guards First**: Before reporting a bug, check if the function already has:
   - NULL checks at the start
   - Bounds validation
   - Precondition enforcement

4. **Test Against Real Usage**: Validate findings using the actual API as called by library consumers, not isolated snippets.

---
