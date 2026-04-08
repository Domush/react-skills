---
name: markdown
description: Use when authoring, editing, or reviewing Markdown files — covers proper syntax, lint rules, formatting best practices, tables, code blocks, and deprecated patterns to avoid
---

# Markdown Authoring

Reference skill for writing correct, clean, lint-compliant Markdown.
Covers CommonMark, GitHub Flavored Markdown (GFM), markdownlint rules, best practices, and common mistakes.

**Sub-files for detailed reference:**

- `markdown-reference.md` — Advanced examples, edge cases, tables, nested structures, GitHub extensions
- `documentation/markdownlint-rules.md` — Complete markdownlint rule list with IDs, aliases, tags, inline config
- `documentation/markdown-syntax-guide.md` — Full CommonMark and GFM syntax reference

---

## Best Practices Summary

### Document Structure

- **One H1 per document** (MD025) — use `#` for the document title only
- **Increment headings by one** (MD001) — never skip levels (e.g., `##` → `####`)
- **Start with a heading** (MD041) — first line should be `#` (unless YAML front matter)
- **End with a newline** (MD047) — single trailing newline at end of file
- **Use ATX headings** (MD003) — `# Heading` style, not setext underline style

### Spacing

- **Blank lines around block elements** — headings (MD022), lists (MD032), code blocks (MD031), tables (MD058)
- **No multiple blank lines** (MD012) — only one blank line between elements
- **No trailing spaces** (MD009) — remove invisible trailing whitespace
- **No hard tabs** (MD010) — use spaces for indentation
- **Space after `#`** (MD018) — `# Heading` not `#Heading`

### Lists

- **Consistent markers** (MD004) — use `-` for unordered lists, don't mix with `*` or `+`
- **Consistent indentation** (MD005) — indent nested items by 2 spaces
- **Surround with blank lines** (MD032) — blank line before and after every list

### Code Blocks

- **Always specify a language** (MD040) — ` ```typescript ` not ` ``` `
- **Use fenced blocks** (MD046) — never use indented code blocks
- **Use backticks** (MD048) — not tildes (`~~~`)
- **Surround with blank lines** (MD031) — blank line before and after every fenced block

### Links & Images

- **No bare URLs** (MD034) — use `[text](url)` or `<url>`
- **Descriptive link text** (MD059) — never use "click here" or "link"
- **Always add alt text** (MD045) — `![description](image.png)`
- **No empty links** (MD042) — every link must have an `href`

### Tables

- **Surround with blank lines** (MD058) — blank line before and after every table
- **Consistent column counts** (MD056) — every row must have the same number of pipes
- **Use leading/trailing pipes** — `| Cell |` not `Cell`
- **Compact tables preferred** — `| --- |` not `|---|` or `|  ---  |`
- **Aligned tables are not preferred** unless the cell content is very short

---

## Most Common Lint Rules (Quick Reference)

| Rule | Alias | What It Catches |
| ------ | ------- | ----------------- |
| MD001 | heading-increment | Skipped heading levels (`##` → `####`) |
| MD009 | no-trailing-spaces | Invisible trailing whitespace |
| MD012 | no-multiple-blanks | Two or more consecutive blank lines |
| MD013 | line-length | Lines longer than 80 chars (usually disabled, so safe to ignore) |
| MD022 | blanks-around-headings | Missing blank line before/after heading |
| MD031 | blanks-around-fences | Missing blank line before/after code block |
| MD032 | blanks-around-lists | Missing blank line before/after list |
| MD033 | no-inline-html | Raw HTML tags in Markdown |
| MD034 | no-bare-urls | URLs without link syntax |
| MD040 | fenced-code-language | Code block without language identifier |
| MD041 | first-line-h1 | File doesn't start with H1 heading |
| MD047 | single-trailing-newline | File doesn't end with exactly one newline |
| MD058 | blanks-around-tables | Missing blank line before/after table |

---

## Correct Patterns

### Headings

```markdown
# Document Title

## Section

### Subsection

#### Detail Level
```

### Table

#### Aligned tables (not preferred, unless cell content is very short)

> Pipes are aligned in every row, no before/after space requirement.

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |
```

#### Compact tables (preferred)

> One space before and after the pipe.

```markdown
| Column 1 | Column 2 | Column 3 |
| --- | --- | --- |
| Cell 1 | Cell 2 | Cell 3 |
| Cell 4 | Cell 5 | Cell 6 |
```

#### Tight tables (not preferred, hard to read)

> No space before or after the pipe.

```markdown
|Column 1|Column 2|Column 3|
|---|---|---|
|Cell 1|Cell 2|Cell 3|
|Cell 4|Cell 5|Cell 6|
```

#### Column Alignment

> Use colons to align columns.

```markdown
| Left     |  Center   | Right    |
|:---------|:---------:|---------:|
| Aligned  |  Aligned  |  Aligned |
```

- `:---` = Left-aligned (default)
- `:---:` = Center-aligned
- `---:` = Right-aligned

### Fenced Code Block

> Use 3 backticks for fenced code blocks. Code blocks MUST have a language identifier.

````markdown
```typescript
interface User {
  id: string;
  name: string;
}
```
````

### Links

```markdown
See the [configuration guide](https://example.com/config) for details.

Download the [latest release][release].

[release]: https://github.com/user/repo/releases/latest
```

### List

```markdown
Prerequisites:

- Node.js 20+
- npm 10+
- VS Code (recommended)

Steps:

1. Clone the repository
2. Install dependencies
3. Start the dev server
```

---

## Deprecated / Incorrect Patterns

| ❌ Deprecated / Wrong | ✅ Use Instead | Rule |
| ----------------------- | --------------- | ------ |
| `#Heading` (no space) | `# Heading` | MD018 |
| Setext headings (`===`, `---` underlines) | ATX headings (`#`, `##`) | MD003 |
| `#` at end of heading (`## Heading ##`) | No closing hashes | MD020/021 |
| Indented code blocks (4 spaces) | Fenced code blocks (` ``` `) | MD046 |
| ` ~~~ ` tilde fences | ` ``` ` backtick fences | MD048 |
| Bare URLs (`https://...`) | `[text](url)` or `<url>` | MD034 |
| `(text)[url]` (reversed) | `[text](url)` | MD011 |
| `* text *` (spaces in emphasis) | `*text*` | MD037 |
| `` ` code ` `` (spaces in code) | `` `code` `` | MD038 |
| `[ text ](url)` (spaces in link) | `[text](url)` | MD039 |
| Trailing punctuation in heading (`## Why?`) | `## Why` | MD026 |
| `**Bold Text**` used as heading | `## Bold Text` as proper heading | MD036 |
| "Click here" link text | Descriptive: "See the [API docs](url)" | MD059 |
| `![](image.png)` (no alt text) | `![Description](image.png)` | MD045 |

### Deprecated Setext Headings

```markdown
<!-- ❌ DEPRECATED: Setext style -->
My Heading
==========

Sub Heading
-----------

<!-- ✅ CORRECT: ATX style -->
# My Heading

## Sub Heading
```

### Deprecated Indented Code

````markdown
<!-- ❌ DEPRECATED: Indented code block -->
    function hello() {
      return "world";
    }

<!-- ✅ CORRECT: Fenced code block with language -->
```javascript
function hello() {
  return "world";
}
```
````

### Deprecated Closed ATX Headings

```markdown
<!-- ❌ DEPRECATED: Closed ATX -->
## My Heading ##

<!-- ✅ CORRECT: Open ATX -->
## My Heading
```

---

## Inline Lint Controls

Disable rules selectively when needed:

```markdown
<!-- Disable for the next line -->
<!-- markdownlint-disable-next-line MD013 -->
This is a deliberately long line that exceeds the default limit for display purposes.

<!-- Disable for a block, then restore -->
<!-- markdownlint-disable MD033 -->
<details>
<summary>Expandable section</summary>
Content here.
</details>
<!-- markdownlint-restore -->

<!-- Configure for entire file -->
<!-- markdownlint-configure-file { "MD013": { "line_length": 120 } } -->
```

> **Best practice:**
> Prefer `<!-- markdownlint-disable-next-line -->` for surgical exceptions.
> Avoid blanket `<!-- markdownlint-disable -->` without a corresponding `<!-- markdownlint-restore -->`.

---

## Common Mistakes

| Mistake | Fix |
| --------- | ----- |
| No blank line before a list | Add blank line — list won't render without it in some parsers |
| No blank line after heading | Add blank line — required by MD022 |
| Mixing `*`, `-`, `+` in lists | Pick one marker (prefer `-`) and use it consistently |
| Table missing separator row | Add `\|---\|---\|` row between header and body |
| Code block not closing | Match the number of backticks/tildes in opening and closing |
| Skipping heading levels | Never jump from `##` to `####` — go `##` → `###` → `####` |
| Using emphasis as a heading | Use a real `##` heading instead of `**Bold Text**` on its own line |
| Image without alt text | Always include `![meaningful description](url)` |
| File not ending with newline | Ensure single `\n` at end of file |

---

## GitHub Alerts (Admonitions)

```markdown
> [!NOTE]
> Background information the reader should know.

> [!TIP]
> Helpful advice for doing things better.

> [!IMPORTANT]
> Key information users need to know.

> [!WARNING]
> Potential issues that could cause problems.

> [!CAUTION]
> Actions that could cause data loss or security risks.
```

---

## Quick Reference Links

- [CommonMark Spec](https://spec.commonmark.org/current/)
- [GitHub Flavored Markdown Spec](https://github.github.com/gfm/)
- [Markdown Guide](https://www.markdownguide.org/)
- [markdownlint Rules](https://github.com/DavidAnson/markdownlint/blob/main/doc/Rules.md)
- [markdownlint Demo](https://dlaa.me/markdownlint/)
- [VS Code markdownlint Extension](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
