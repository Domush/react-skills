---
name: tools
description: Use when choosing between tools or shell commands, searching/reading/editing/creating files, checking for errors, using MCP servers, deciding whether a terminal command is justified, or selecting the right search strategy.
---

# Tools

## Overview

Prefer deterministic tools over shell commands for every file, search, and validation operation. Tools are faster, safer, and produce structured output. **If a dedicated tool can do it, the terminal is wrong.**

Read files before editing them. Always verify after edits.

---

## Quick Chooser

| Intent | Use This | Not This |
| --- | --- | --- |
| Find files by name/path | `file_search` | `find`, `dir`, `ls` |
| Search code by text/regex | `grep_search` | terminal `grep`/`rg` |
| Search by concept or meaning | `semantic_search` | broad guessing with grep |
| Read file contents | `read_file` | `cat`, `head`, `tail` |
| Explore a directory | `list_dir` | terminal `ls -la` |
| Create a new file | `create_file` | `echo >`, `New-Item` |
| Edit an existing file | `replace_string_in_file` | shell `sed`/`awk` edits |
| Multiple edits in one call | `multi_replace_string_in_file` | sequential single-replace calls |
| Find all symbol usages | `vscode_listCodeUsages` | broad text search |
| Rename symbol everywhere | `vscode_renameSymbol` | find-and-replace across files |
| Check diagnostics/linting | `get_errors` | scanning build logs manually |
| Analyze test failures | `test_failure` | log scraping |
| Clarify requirements | `vscode_askQuestions` | assuming or guessing |
| Track multi-step work | `manage_todo_list` | mental notes |
| Deep exploratory research | `runSubagent` (`Explore`) | chaining dozens of searches |

---

## When the Terminal Is Acceptable

Escalate to `run_in_terminal` **only** when no tool covers the task:

- Build / test / lint: `npm run build`, `npm test`, `npm run lint`
- Git operations: `git add`, `git commit`, `git tag`
- Package installs: `npm install`
- Project-specific CLI tools (e.g., `npx vercel`)

**Never** use the terminal to: read files, list directories, search code, create files, or edit files.
**Never** use terminal redirection or shell write commands to create/update workspace files (including temp files). Use file tools (`create_file`, `apply_patch`, or replace tools) instead.

Run one terminal command at a time. Wait for output before running the next.

---

## Search Strategy

| Scenario | Tool |
| --- | --- |
| Know the exact identifier, text, or error message | `grep_search` |
| Know the file name or path pattern | `file_search` |
| Know the concept but unsure of exact terms | `semantic_search` |
| Multiple known files to read at once | `read_file` (parallel calls) |

Stop calling `semantic_search` if successive results overlap — you have sufficient context.

---

## Parallelization

Batch **read-only** operations that don't depend on each other:

```
// Good – all at once
read_file(a), read_file(b), grep_search(c)

// Bad – waiting unnecessarily
read_file(a) → wait → read_file(b) → wait → read_file(c)
```

**Write operations and terminal commands are always sequential.** Never run `run_in_terminal` in parallel with another terminal call.

---

## Deferred Tools

Most MCP tools are **deferred** — they are not loaded until you explicitly call `tool_search_tool_regex`. Calling a deferred tool without loading it first will silently fail or error.

**Before using any MCP tool:**

1. Call `tool_search_tool_regex` with a pattern matching the tool name.
2. Confirm the tool appears in results.
3. Then call it.

```
// Supabase example
tool_search_tool_regex("mcp_supabase") → confirms available tools
mcp_supabase_list_tables(...)

// Vercel example
tool_search_tool_regex("mcp_com_vercel") → confirms available tools
mcp_com_vercel_ve_list_projects(...)
```

Never skip this step — even when confident the tool exists.

---

## MCP Namespaces at a Glance

See `mcp-reference.md` for full tool catalogs and workflows per namespace.

| Namespace | Purpose | Load Pattern |
| --- | --- | --- |
| Supabase | Schema, migrations, TypeScript types, logs | `tool_search_tool_regex("mcp_supabase")` |
| Vercel | Deployments, build/runtime logs, projects | `tool_search_tool_regex("mcp_com_vercel")` |
| Next.js DevTools | Runtime diagnostics, docs | `tool_search_tool_regex("mcp_io_github_ver")` |
| Context7 | Version-accurate library docs/snippets | `tool_search_tool_regex("mcp_io_github_ups")` |
| Browser (Playwright) | UI automation, screenshots, console/network | `tool_search_tool_regex("mcp_microsoft_pla")` |

**Always prefer MCP platform tools over terminal equivalents** for cloud operations (schema changes, deployments, log inspection).

---

## Verification Loop

After every file edit:

1. `get_errors` on changed files — fix any new diagnostics immediately.
2. Re-read the edited region to confirm correctness and check nothing was accidentally removed.
3. If tests cover the change, run them with `run_in_terminal`.

---

## Anti-Patterns

| Bad Move | Fix |
| --- | --- |
| Using terminal `grep` or `find` for code search | Use `grep_search` or `file_search` |
| Reading files with `cat` via terminal | Use `read_file` with an explicit line range |
| Calling a deferred MCP tool without loading it | Call `tool_search_tool_regex` first |
| Running two `run_in_terminal` calls in parallel | Always sequential — wait for output |
| Editing a file without reading it first | `read_file` before every `replace_string_in_file` |
| Repeating `semantic_search` when results overlap | Stop — context is sufficient |
| Guessing at user requirements | Use `vscode_askQuestions` |
| Sequential single-replace calls for multiple edits | Batch with `multi_replace_string_in_file` |
| Using `apply_patch` (not available here) | Use `replace_string_in_file` or `multi_replace_string_in_file` |
| Writing files via shell redirection (`>`, `>>`, `Set-Content`, `Out-File`) | Use `create_file` / file edit tools, then use terminal only for non-file-write commands |
