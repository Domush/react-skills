# MCP Reference

Full tool catalogs and recommended workflows per MCP namespace.

> Always call `tool_search_tool_regex` before using any MCP tool. See SKILL.md → Deferred Tools.

---

## Supabase MCP

**Load:** `tool_search_tool_regex("mcp_supabase")`

**Docs:** <https://supabase.com/docs/guides/getting-started/mcp>  
**Hosted MCP:** `https://mcp.supabase.com/mcp`  
**Local dev MCP:** `http://localhost:54321/mcp`

### Core Tools

| Tool | Use For |
| --- | --- |
| `mcp_supabase_list_tables` | Inspect current schema |
| `mcp_supabase_apply_migration` | Apply DDL schema changes |
| `mcp_supabase_list_migrations` | Verify migration history |
| `mcp_supabase_generate_typescript_types` | Regenerate app types after schema change |
| `mcp_supabase_get_advisors` | Security and performance issue scan |
| `mcp_supabase_get_logs` | Debug runtime errors by service |
| `mcp_supabase_get_project_url` | Get project API URL |
| `mcp_supabase_get_publishable_keys` | Get anon/public API keys |
| `mcp_supabase_search_docs` | Look up current Supabase behavior |
| `mcp_supabase_execute_sql` | Run read queries (avoid for DDL) |

### Activatable Groups (load separately)

- `mcp_supabase_create_branch` / `list_branches` / `merge_branch` / `reset_branch` / `rebase_branch` / `delete_branch` — Isolated branch-based DB work
- `mcp_supabase_deploy_edge_function` / `get_edge_function` / `list_edge_functions` — Edge function management
- `mcp_supabase_list_extensions` — Database extension inspection

### Recommended Schema Change Workflow

1. `mcp_supabase_list_tables` — review current state
2. `mcp_supabase_get_advisors` — check for security or performance risks
3. `mcp_supabase_apply_migration` — apply DDL (never raw `execute_sql` for schema changes)
4. `mcp_supabase_list_migrations` — confirm applied
5. `mcp_supabase_generate_typescript_types` — sync app types

### Security Notes

- Do not connect MCP to production data by default.
- Keep manual tool approval enabled.
- Prefer project-scoped, read-only mode when possible.

---

## Vercel MCP

**Load:** `tool_search_tool_regex("mcp_com_vercel")`

**Docs:** <https://vercel.com/docs/mcp>  
**Agent resources:** <https://vercel.com/docs/agent-resources/vercel-mcp>

### Core Tools

| Tool | Use For |
| --- | --- |
| `mcp_com_vercel_ve_list_teams` | Discover available teams |
| `mcp_com_vercel_ve_list_projects` | List projects in a team |
| `mcp_com_vercel_ve_get_project` | Project config and settings |
| `mcp_com_vercel_ve_list_deployments` | Recent deployment history |
| `mcp_com_vercel_ve_get_deployment` | Specific deployment details |
| `mcp_com_vercel_ve_get_deployment_build_logs` | Build failure diagnostics |
| `mcp_com_vercel_ve_get_runtime_logs` | Runtime error logs |
| `mcp_com_vercel_ve_get_access_to_vercel_url` | Access protected deployment URL |
| `mcp_com_vercel_ve_search_vercel_documentation` | Look up current Vercel behavior |
| `mcp_com_vercel_ve_check_domain_availability_and_price` | Domain availability check |
| `mcp_com_vercel_ve_web_fetch_vercel_url` | Fetch content from a Vercel deployment URL |

### Activatable Groups (load separately)

- Deployment tools — execute deployments
- Deployment inspection tools — deep build/runtime logs
- Toolbar thread management — `list_toolbar_threads`, `get_toolbar_thread`, `reply_to_toolbar_thread`, `edit_toolbar_message`, `add_toolbar_reaction`, `change_toolbar_thread_resolve_status`

### Recommended Deploy + Diagnose Workflow

1. `list_teams` + `list_projects` — confirm target project
2. Use deployment tools to trigger deploy
3. `get_deployment_build_logs` — review build output
4. `get_runtime_logs` — filter by level/source for runtime failures
5. `get_access_to_vercel_url` — confirm deployment is reachable

---

## Next.js DevTools MCP

**Load:** `tool_search_tool_regex("mcp_io_github_ver")`

**Docs:** <https://nextjs.org/docs/app/guides/mcp>

### Core Tools

| Tool | Use For |
| --- | --- |
| `mcp_io_github_ver_init` | Initialize MCP connection to dev server |
| `mcp_io_github_ver_nextjs_index` | Discover available server diagnostic tools |
| `mcp_io_github_ver_nextjs_call` | Execute runtime diagnostics |
| `mcp_io_github_ver_nextjs_docs` | Fetch official Next.js docs |
| `mcp_io_github_ver_browser_eval` | Evaluate JS in browser / take screenshots |
| `mcp_io_github_ver_upgrade_nextjs_16` | Upgrade codemod to Next.js 16 |
| `mcp_io_github_ver_enable_cache_components` | Enable Cache Components feature |

### Recommended Runtime Debug Workflow

1. `mcp_io_github_ver_init` — connect to dev server
2. `mcp_io_github_ver_nextjs_index` — discover tool names
3. `mcp_io_github_ver_nextjs_call` — run diagnostics
4. `mcp_io_github_ver_browser_eval` — verify rendered output
5. `mcp_io_github_ver_nextjs_docs` — reference docs when in doubt

### Notes

- Dev server must be running (`npm run dev`) before using runtime tools.
- Prefer `browser_eval` over raw HTTP for verifying Next.js rendered pages.

---

## Context7 MCP

**Load:** `tool_search_tool_regex("mcp_io_github_ups")`

**Repo:** <https://github.com/upstash/context7>  
**Docs:** <https://context7.com/docs/resources/all-clients>

### Core Tools

| Tool | Use For |
| --- | --- |
| `mcp_io_github_ups_resolve-library-id` | Get Context7-compatible library ID |
| `mcp_io_github_ups_get-library-docs` | Fetch version-accurate docs/snippets |

### Recommended Pattern

1. `resolve-library-id` with the npm package name
2. `get-library-docs` with the resolved ID, a focused topic, and desired mode (`code` or `info`)
3. Use explicit version ID when precision matters

**Use this before writing code for any third-party library** — avoids stale training data hallucinations.

---

## Browser Automation MCP (Playwright)

**Load:** `tool_search_tool_regex("mcp_microsoft_pla")`

**Repo:** <https://github.com/microsoft/playwright-mcp>

### Core Tools

| Tool | Use For |
| --- | --- |
| `mcp_microsoft_pla_browser_navigate` | Navigate to a URL |
| `mcp_microsoft_pla_browser_snapshot` | Capture accessibility tree / page state |
| `mcp_microsoft_pla_browser_take_screenshot` | Visual page capture |
| `mcp_microsoft_pla_browser_click` | Click an element |
| `mcp_microsoft_pla_browser_fill_form` | Fill form fields |
| `mcp_microsoft_pla_browser_type` | Type text into focused element |
| `mcp_microsoft_pla_browser_evaluate` | Run JS in browser context |
| `mcp_microsoft_pla_browser_console_messages` | Capture browser console logs |
| `mcp_microsoft_pla_browser_network_requests` | Inspect network activity |
| `mcp_microsoft_pla_browser_wait_for` | Wait for element/condition |
| `mcp_microsoft_pla_browser_tabs` | Manage browser tabs |
| `mcp_microsoft_pla_browser_close` | Close browser session |
| `mcp_microsoft_pla_browser_run_code` | Execute complex browser scripts |
| `mcp_microsoft_pla_browser_select_option` | Select dropdown options |
| `mcp_microsoft_pla_browser_press_key` | Send keyboard events |
| `mcp_microsoft_pla_browser_hover` | Hover over an element |
| `mcp_microsoft_pla_browser_drag` | Drag and drop |
| `mcp_microsoft_pla_browser_resize` | Resize browser window |
| `mcp_microsoft_pla_browser_navigate_back` | Navigate back in browser history |
| `mcp_microsoft_pla_browser_handle_dialog` | Accept/dismiss browser dialogs |
| `mcp_microsoft_pla_browser_file_upload` | Upload files via browser input |

### Notes

- Prefer browser automation over raw HTTP for validating Next.js rendered pages.
- Capture `console_messages` and `network_requests` together when debugging runtime issues.
- Use `snapshot` (accessibility tree) before `take_screenshot` — it's cheaper and often sufficient.

---

## Misc Tools

| Tool | Use For |
| --- | --- |
| `fetch_webpage` | Fetch and summarize a specific web page |
| `view_image` | Inspect image files (PNG, JPG, GIF, WebP) |
| `renderMermaidDiagram` | Render a Mermaid diagram to SVG |
| `open_browser_page` | Open a URL in the system browser |
| `get_changed_files` | List files changed in the current git diff |
| `github_repo` | Interact with GitHub repositories |
| `resolve_memory_file_uri` | Get URI for a memory file path |
